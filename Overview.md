## Bootstrapping ##
[Bootstrapper](Bootstrapper.md)
> Bootstrapper provides a simple and flexible way to make your application's startup and shutdown behavior pluggable and extendable.

## Events ##

EventBroker
> Notification component for synchronous and asynchronous loosly coupled notification with automatic thread switching

DistributedEventBroker
> A powerful extension for the EventBroker which allows to fire events over process boundaries!

MappingEventBroker
> A powerful EventBroker extension which allows to map source events to destination events.

[generic event arguments](EventArgsT.md)
> `EventArgs<YourTypeGoesHere>`

EventFilter
> Event filter to aggregate and delay frequently occurring events (e.g. file changes)

## Hierarchical State Machine ##
StateMachine
> Hierarchical state machine with fluent definition syntax (active or passive)

## Asynchronous Operations ##
AsyncResult
> Implementation for IAsyncResult to execute asynchonous operations.

AsyncWorker
> Extends BackgroundWorker with exception handling that does not swallow exception and simpler usage within code.

AsyncModule
> Provide an easy way to a multithreaded application. Arbitrary objects can be extended with one or more worker threads and message queues to perform asynchronous tasks.

UserInterfaceThreadSynchronizer
> Simple to use thread switcher to execute operations synchronously or asynchronously on user interface thread (WinForms, WPF and ASP.NET)

## Evaluation Engine / Rule Engine ##
EvaluationEngine
> Foundation for evaluations (rules, validations, computations, ...): Single entry point for evaluations, loosely coupled, strict separation between questioner and solution definition.

## Test Utilities ##
EventTester / EventTestList
> Simplifies testing of event invocations (automatic registration and deregistration, invocation count test, ...) 

Log4NetHelper
> Test log messages generated using log4net.

## csv ##
CsvParser / CsvWriter
> Parser and Writer for .csv (comma separated values) files.

## IO ##
TextFileReader / TextFileWriter
> Wrapper for files access to enable unit testing

TemporaryFileHolder
> Manages temporarily needed files (automatic deletion on dispose)

FileActionCommand
> Execute commands recursively on the file system: Get all files, move files, number of folders and files, extensible with custom commands.

FileSystemHelper / DriveUtilities
> Provides information about the file system and drives.

## Streams ##
StreamHelper
> Provides functionality for manipulating streams (copy and compare).

StreamLoggerStream / StreamDecoratorStream
> Stream decorators for logging or generic use.

## Miscellaneous ##
ApplicationHelper
> Provides functionality to check whether application is already running and optionally switch to running instance.

WindowsHelper
> Provides shutdown and logoff functionality.

HighResolutionStopWatch
> StopWatch that uses processor ticks for high resolution timing.

FormatHelper
> String.Format replacement that does not crash if too few arguments are defined but returns an error string.

StringTruncationFormatter
> Format provider for formatting string with a maximal length.

Resources
> Resource loaders to load data from file system or embedded resources.