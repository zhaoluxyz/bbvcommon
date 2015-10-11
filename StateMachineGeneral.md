bbv.Common contains three different state machines:
  * [Passive State Machine](PassiveStateMachine.md)
  * [Active State Machine](ActiveStateMachine.md)
  * [Unit Test State Machine](UnitTestStateMachine.md)

All these state machines implement the interface `IStateMachine`.

For better testability and flexibility, I suggest that you reference your state machine only by the interface and use dependency injection to get either of the implementations.

## States and Events ##

Either state machine is defined using `States` and `Events`. States and events have to be `IComparable`s (`enum`, `int`, `string`, ...).

If you have a well known set of states and events they I suggest you use enums which make the code much better readable.

If you plan to build some flexibility into your state machine (e.g. add states, transitions in base classes) then you better use an "open" type like string, integer.

## Interface Declaration ##

The interface provides the following:

> `event EventHandler<TransitionEventArgs<TState, TEvent>> TransitionDeclined`
> > Occurs when no transition could be executed for the fired event. The state machine remains in the current state.


> `event EventHandler<ExceptionEventArgs<TState, TEvent>> ExceptionThrown`
> > Occurs when an exception was thrown inside the state machine.


> `event EventHandler<TransitionExceptionEventArgs<TState, TEvent>> TransitionExceptionThrown`
> > Occurs when an exception was thrown inside a transition of the state machine.


> `event EventHandler<TransitionEventArgs<TState, TEvent>> TransitionBegin`
> > Occurs when a transition begins.


> `event EventHandler<TransitionCompletedEventArgs<TState, TEvent>> TransitionCompleted`
> > Occurs when a transition completed.


> `bool IsRunning { get; }`
> > Gets a value indicating whether this instance is running. The state machine is running if if was started and not yet stopped.


> `IEntryActionSyntax<TState, TEvent> In(TState state)`
> > Defines the behavior of a state.


> `void DefineHierarchyOn(TState superStateId, TState initialSubStateId, HistoryType historyType, params TState[] subStateIds)`
> > Defines a state hierarchy.


> `void Fire(TEvent eventId, params object[] eventArguments)`
> > Fires an event. The event is queued last.


> `void FirePriority(TEvent eventId, params object[] eventArguments)`
> > Fires an event. The event is queued first.


> `void Initialize(TState initialState)`
> > Initializes the state machine to the specified initial state. Can only be called once. Note that the initial state is not yet entered until the state machine is started.


> `void Start()`
> > Starts the state machine. Events will be processed.
> > If the state machine is not started then the events will be queued until the state machine is started.
> > Already queued events are processed


> `void Stop()`
> > Stops the state machine. Events will be queued until the state machine is started.


> `void AddExtension(IExtension<TState, TEvent> extension)`
> > Adds an extension (e.g. logging extension)


> `void ClearExtensions()`
> > Clears all extensions.


> `void Report(IStateMachineReport<TState, TEvent> reportGenerator)`
> > Creates a report with the specified generator (e.g. csv, yEd)