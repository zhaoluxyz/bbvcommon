## Sample publisher ##

Publish an event topic:
```
public class Publisher
{
    [EventPublication("topic://EventBrokerSample/SimpleEvent")]
    public event EventHandler SimpleEvent;
    
    ///<summary>Fires the SimpleEvent</summary>
    public void CallSimpleEvent()
    {
        SimpleEvent(this, EventArgs.Empty);
    }
}
```

Register the publisher with your event broker (you have to hold an instance of the event broker somewhere in your code). The sample assumes there is a service that holds the event broker instance for us:

```
EventBroker eb = Service.EventBroker;
Publisher p = new Publisher();
eb.Register(p);
```

On registration of the publisher, the event broker inspects the publisher for published events (events with the `EventPublication` attribute).

## Sample subscriber ##

Subscribe to an event topic:
```
public class Subscriber
{
    [EventSubscription(
        "topic://EventBrokerSample/SimpleEvent", 
        typeof(Handlers.Publisher))]
    public void SimpleEvent(object sender, EventArgs e)
    {
        // do something useful or at least funny
    }
}
```

Register the subscriber with the event broker:

```
EventBroker eb = Service.EventBroker; 
Subscriber s = new Subscriber();
eb.Register(s); 
```

The event broker will inspect the subscriber, on registration, for subscription to event topics (methods with the `EventSubscription` attribute).

If a publisher fires an event topic for that subscribers are registered, then the event broker will relay them to the subscribers by calling the subscription handler methods with the `sender` and `EventArgs` the publisher used to fire its event.

## Publication options ##
### Simple ###
```
[EventPublication("Simple")]
public event EventHandler SimpleEvent;
```

### With custom Eventargs ###
Note: `CustomEventArgs` has simply to be derived from `EventArgs`.

```
[EventPublication("CustomEventArgs")]
public event EventHandler<CustomEventArguments> CustomEventArgs;
```

### Publish multiple event topics with one single event ###
```
[EventPublication("Event1")]
[EventPublication("Event2")]
[EventPublication("Event3")]
public event EventHandler MultiplePublicationTopics;
```

### Allow only synchronous subscription handlers ###
```
[EventPublication("test", HandlerRestriction.Synchronous)]
public event EventHandler AnEvent; 
```

### Allow only asynchronous subscription handlers ###
```
[EventPublication("test", HandlerRestriction.Asynchronous)]
public event EventHandler AnEvent;
```

## Subscription options ##
### Simple ###
```
[EventSubscription("Simple", typeof(Handlers.Publisher)]
public void SimpleEvent(object sender, EventArgs e) {} 
```

### Custom EventArgs ###
```
[EventSubscription("CustomEventArgs"), typeof(Handlers.Publisher))]
public void CustomEventArgs(object sender, CustomEventArgs e) {} 
```

### Subscribe multiple event topics ###
```
[EventSubscription("Event1", typeof(Handlers.Publisher))]
[EventSubscription("Event2", typeof(Handlers.Publisher))]
[EventSubscription("Event3", typeof(Handlers.Publisher))]
public void MultipleSubscriptionTopics(object sender, EventArgs e) {} 
```

### Execute handler on background thread (asynchronous) ###
The event broker creates a worker thread to execute the handler method on. The publisher can immediately continue processing.

```
[EventSubscription("Background", typeof(Handlers.Background))]
public void BackgroundThread(object sender, EventArgs e) {} 
```

### Execute handler on UI thread ###
Use this option if calling from a background worker thread to a user interface component that updates the user interface - no need for Control.Invoke(...) anymore.

```
[EventSubscription("UI", typeof(Handlers.UserInterface))]
public void UI(object sender, EventArgs e) {}
```

Note that if you use the <tt>UserInterface</tt> handler, then you have to make sure that you register the subscriber on the user interface thread. Otherwise, the EventBroker won't be able to switch to the user interface thread, and will throw an exception.
Execute handler on UI thread asynchronously

The same as above, but the publisher is not blocked until the subscriber has processed the event.

```
[EventSubscription("UIAsync", typeof(Handlers.UserInterfaceAsync))]
public void UI(object sender, EventArgs e) {}  
```

## Fire event topics directly on EventBroker ##

Event topics can be fired directly on the EventBroker without the need of registering a publisher. This comes in handy whenever you need to fire an event topic from an object that lives only very shortly.

A good example for such a scenario is a scheduled job execution. At the scheduled time, an instance of a job class is instantiated and executed. It would be cumbersome to register this instance, fire the event, and unregister it again - it's far easier to fire the event directly on the EventBroker:

```
eventBroker.Fire("topic", sender, eventArgs);
```

## Scope ##

Sometimes it is necessary to limit the scope an event is published to. This can be achieved in two different ways:

### Multiple instances of event brokers ###
Subscribers can only listen to events of publishers that are registered on the same event broker. Therefore, an event broker automatically builds a scope.

This is the easiest solution to have several event handling scopes in your application, and should always be preferred. However, sometimes, you need more control over scopes within the objects on one single event broker. This scenario is described in the following section.

### Hierarchical naming ###
Publishers and subscribers can be named by implementing the <tt>INamedItem</tt> interface. This interface provides a single property:

```
string EventBrokerItemName { get; }
```

This allows identifying an object within the event broker whereas the publication and subscription attributes are always bound to the class.

The naming is hierarchical by using the same scheme as namespaces:

  * Test is a parent of Test.MyPublisher1
  * Test.MyName1 is a sibling of Test.MyName2
  * Test.MyName1.Subname is a child of Test.MyName1
  * Test.MyName1 is a twin of Test.MyName1 (two objects with the same name are twins)

Now, a publisher can define the scope it wants to publish the event to, by defining a scope in the publication attribute:

```
[EventPublication("Topic")]
public event EventHandler PublishedGlobally;

[EventPublication("Topic", typeof(ScopeMatchers.PublishToParents)]
public event EventHandler PublishedToParentsAndTwinsOnly;

[EventPublication("Topic", typeof(ScopeMatchers.PublishToChildren)]
public event EventHandler PublishedToChildrenAndTwinsOnly;
```

The first event is a global event that all subscribers can receive. The second event is only passed to subscribers that are parents or twins of the publisher. The third event is only passed to subscribers that are children or twins of the publisher.

A subscriber can define the scope it wants to receive events from accordingly:

```
[EventSubscription("Topic", typeof(Handlers.Publisher)]
public void GlobalHandler(object sender, EventArgs e)

[EventSubscription(
    "Topic", 
    typeof(Handlers.Publisher), 
    typeof(ScopeMatchers.SubscribeToParents)]
public void ParentHandler(object sender, EventArgs e)

[EventSubscription(
    "Topic", 
    typeof(Handlers.Publisher), 
    typeof(ScopeMatchers.SubscribeToChildren)]
public void ChildrenHandler(object sender, EventArgs e)
```

The first subscription is a global subscription that will handle all the events passed to it. The second subscription will only be called when a parent of the subscriber is firing the event. The third subscription will only be called when a child of the subscriber is firing the event.

Twins (different objects with the same name) are handled specially. A twin is always a parent and child of its twin, and will therefore will always receive all the events from its twin.