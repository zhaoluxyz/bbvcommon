## Setup of the distributed event broker ##
The usage of the distributed event broker is astonishingly simple when you have prepared your transport layer. Basically it is only two lines of code.
```
// Use existing event broker or create special one for distributed events
var eventBroker = new EventBroker();

// Use extension method AddDistributedExtension
var extension = // new TechnologySpecificDistributedEventBrokerExtension
eventBroker.AddDistributedExtension(extension);
```

## Transport layers ##
The sections below are not meant to serve as an introduction into the specific transport layers. For transport layer specific setup, read the documentation pages provided by the project owners.
### NServiceBus ###
```
IBus bus = // Get IBus instance
var extension = new NServiceBusDistributedEventBrokerExtension("DistributedEventBroker", bus);
eventBroker.AddDistributedExtension(extension);
```

Make sure that bbv.Common.DistributedEventBroker.NServiceBusAdapter.dll is scanned by NServiceBus when auto loading of message handlers is enabled.
### MassTransit ###
```
IServiceBus serviceBus = // Get IServiceBus instance
var extension = new MassTransitDistributedEventBrokerExtension
("DistributedEventBroker", serviceBus);
eventBroker.AddDistributedExtension(extension);
```

Make sure the `MassTransitEventFiredHandler` is registered with transient life time in the underlying container. When supported, use assembly scanning functionality of container and include bbv.Common.DistributedEventBroker.MassTransitAdapter.dll in the search.
### RhinoESB ###
```
IServiceBus serviceBus = // Get IServiceBus instance
var extension = new RhinoEsbDistributedEventBrokerExtension("DistributedEventBroker", serviceBus);
eventBroker.AddDistributedExtension(extension);
```

Make sure the `RhinoEsbEventFiredHandler` is registered in the windsor container.

## Scoping & Identification ##
### Distributed Event Broker Identification ###
![http://bbvcommon.googlecode.com/svn/wikiFiles/EventBroker/DistributedEventBrokerIdentification.png](http://bbvcommon.googlecode.com/svn/wikiFiles/EventBroker/DistributedEventBrokerIdentification.png)

The distributed event broker identification allows to bind several event brokers together. Only events fired in the same context are recognized from the event brokers participating in the context. That allows to have several distributed event brokers in one application.

Example (here with NServiceBus Transport):
```
// App A
// Setup event broker and extension for business domain "X"
var extensionX = new NServiceBusDistributedEventBrokerExtension("DistributedEventBrokerX", bus);
eventBrokerX.AddDistributedExtension(extensionX);

// Setup event broker and extension for business domain "Y"
var extensionY = new NServiceBusDistributedEventBrokerExtension("DistributedEventBrokerY", bus);
eventBrokerY.AddDistributedExtension(extensionY);

// App B
// Setup event broker and extension for business domain "X"
var extensionX = new NServiceBusDistributedEventBrokerExtension("DistributedEventBrokerX", bus);
eventBrokerX.AddDistributedExtension(extensionX);

// App C
// Setup event broker and extension for business domain "Y"
var extensionY = new NServiceBusDistributedEventBrokerExtension("DistributedEventBrokerY", bus);
eventBrokerY.AddDistributedExtension(extensionY);
```

Although eventBrokerX and eventBrokerY are running in the same process events published and handled on the distributed event broker of the business domain "X" are not seen on the distributed event broker of the business domain "Y" and vice versa.
### Event Broker Identification ###
Each event broker can be identified by specifying an event broker identification. This information is just transmitted when an event is published without further impact on the distributed event broker. But it lets you distinguish from which location the event was coming from.

The identification can be specified by using the following `AddDistributedExtension` overload:

```
eventBroker.AddDistributedExtension(extension, "ApplicationXGlobalEventBroker");
```

When using the extension method without specifying the event broker identification a new System.Guid is generated for each event broker.

```
// Will identify event broker by using System.Guid internally.
eventBroker.AddDistributedExtension(extension);
```


## Customization ##

### Serializer ###

If you want to use another or a custom serializer you have to inherit from `DefaultDistributedFactory` and override `CreateEventArgsSerializer`.

Example:
```
    public class CustomDistributedFactory: DefaultDistributedFactory     {
        public override IEventArgsSerializer CreateEventArgsSerializer()
        {
            return new XmlEventArgsSerializer();
        }
    }
```


### Custom message ###

If you want to extend the standard message with additional information or if your transport layer has restrictions on the message contracts, you have to inherit from `DefaultDistributedFactory` and override `CreateMessageFactory`.

Your message factory can extend `AbstractEventMessageFactory` for simple scenarios.

Example (taken from NServiceBus transport):

```
    // Custom message interface
    public interface INServiceBusEventFired : IEventFired, IMessage
    {
    }
    // Custom message implementation
    public class NServiceBusEventFired : EventFired, INServiceBusEventFired
    {
    }
    // Custom message factory
    public class NServiceBusEventMessageFactory : AbstractEventMessageFactory
    {
        public override IEventFired CreateEventFiredMessage(Action<IEventFired> initializer)
        {
            return this.CreateEventFiredMessage<NServiceBusEventFired, IEventFired>(initializer);
        }
    }
    // Custom distributed factory
    public class NServiceBusDistributedFactory : DefaultDistributedFactory
    {
        public override IEventMessageFactory CreateMessageFactory()
        {
            return new NServiceBusEventMessageFactory();
        }
    }
```
### Selection Strategy ###
Selection strategies are queried when a new topic is created on the event broker where the extension is added. The default selection strategy accepts all topics. This means that all events fired on the event broker which has the extension will be published on the transport layer.

If you want to use your own convention you have to inherit from `DefaultDistributedFactory` and override `CreateTopicSelectionStrategy`. For simple scenarios the `FuncTopicSelectionStrategy` can be used which accepts a lambda which acts as strategy implementation.

Example:
```
    public class CustomDistributedFactory: DefaultDistributedFactory     {

        public override ITopicSelectionStrategy CreateTopicSelectionStrategy()
        {
            return new FuncTopicSelectionStrategy(topic => 
topic.Uri.StartsWith("distributed://", StringComparison.Ordinal);
        }
    }
```

The strategy above would only accept event publications where the topic URI starts with distributed://. All other publications would not be published on the transport layer.
## Restrictions ##
Synchronous subscription of events which are fired on the distributed event broker is not allowed. In our point of view this makes no sense and would couple the distributed event broker too tight to its participants in the network. Therefore all publications must use asynchronous handler restrictions.