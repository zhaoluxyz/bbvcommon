## Setup of the distributed event broker ##
The usage of the mapping event broker is astonishingly simple once you have setup your mapping technology. Basically it is only two lines of code.
```
// Use existing event broker or create special one for events which need to be mapped
var eventBroker = new EventBroker();

// Use extension method AddMappingExtension
var extension = // new TechnologySpecificMappingEventBrokerExtension
eventBroker.AddMappingExtension(extension);
```

## Mapping technologies ##
The sections below are not meant to serve as an introduction into the specific mapping technologies. For mapping technology specific setup, read the documentation pages provided by the project owners.
### AutoMapper ###
```
var extension = new AutoMapperEventBrokerExtension();
eventBroker.AddMappingExtension(extension);
```

Make sure that automapper is correctly configured with the appropriate source to destination event argument type mappings.

### Topic Conventions ###
Topic conventions are queried when a new topic is created on the event broker where the extension is added. The default topic convention inspects all topics whose source URI starts with _topic://_ and maps them to the destination URI _mapped://_. This means that all events of the format _topic://{Event}_ fired on the event broker which has the extension will be mapped to the destination format _mapped://{Event}_.

If you want to use your own convention you have to implement  `ITopicConvention`. For simple scenarios the `FuncTopicConvention` can be used which accepts lambdas which act as convention implementation.

Example:
```
var convention = FuncTopicConvention(
  t => t.Uri.StartsWith("topicdistributed://", StringComparison.Ordinal),
  dest => dest.Replace("topicdistributed://", "topiclocal://")
)
```

Or roll your own by implementing `ITopicConvention`:

Example:
```
    public class MyCustomConvetion: ITopicConvention
    {
        private IEnumerable<string> ExcludedCandidates
        {
            get
            {
                yield return "topicdistributed://SomeEvilEvent";
                yield return "topicdistributed://TooHeavyEvent";
            }
        }

        public string MapTopic(string topic)
        {
            return topic.Replace("topicdistributed://", "topiclocal://");
        }

        public bool IsCandidate(IEventTopicInfo eventTopic)
        {
            return eventTopic.Uri.StartsWith("topicdistributed://", StringComparison.Ordinal) && 
               !this.ExcludedCandidates.Any(s => eventTopic.Uri.Equals(s));
        }
    }
```
### Destination Event Argument Type Providers ###
The mapping event broker has no knowledge about the destination event arguments type. In order to be able to retrieve destination event argument types you have to provide an implementation of `IDestinationEventArgsTypeProvider` to the mapping event broker extension. The usage is basically very simple, you just need to map a destinationTopic URI to a destination event argument type. If you want to skip the mapping you just return `null` instead.:

```
    public class DestinationEventArgsTypeProvider : IDestinationEventArgsTypeProvider
    {
        private Dictionary<string, Type> mappings = 
          new Dictionary<string, Type> 
        {
           { "topicdistributed://EventA", typeof(DestinationEventAEventArgs) }, 
        };

        public Type GetDestinationEventArgsType(string destinationTopic, Type sourceEventArgsType)
        {
            Type destinationType;
            typeMappings.TryGet(destinationTopic, out destinationType));
            
            return destinationType;
        }
    }
```

Notice you also receive the `sourceEventArgsType`. This allows to build up convention based mappings.
## Restrictions ##
  * Currently only automapper is supported.