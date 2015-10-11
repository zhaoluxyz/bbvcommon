## Features ##
  * use enums, ints or strings for states and events - resulting in single class state machines.
  * actions
    * on transitions
    * entry and exit actions
  * transition guards
  * hierarchical
    * different history behaviors to initialize state always to same state or last active state
  * fluent definition interface
  * synchronous/asynchronous state machine
    * passive state machine handles state transitions synchronously
    * active state machine handles state transitions asynchronously on the worker thread of the state machine
    * special unit test state machine to simplify unit testing (exception behavior)
  * extensible thorough logging simplifies debugging
    * currently there exists a log4net log extension out of the box
  * state machine reports
    * textual description of state machine
    * csv
      * for states containing state, entry and exit actions
      * for transitions containing source, event, guard, target, transition actions

## Documentation ##
[Tutorial](StateMachineTutorial.md)

[General](StateMachineGeneral.md)

[Passive State Machine](PassiveStateMachine.md)

[Active State Machine](ActiveStateMachine.md)

[Unit Test State Machine](UnitTestStateMachine.md)

[Hierarchical Transitions](StateMachineHierarchicalTransitions.md)

[Exception Handling](StateMachineExceptionHandling.md)

[Extensions](StateMachineExtensions.md)

[Logging](StateMachineLogging.md)

[Report](StateMachineReport.md)

[Complete Sample State Machine](StateMachineSample.md)

[Specification](StateMachineSpecification.md)