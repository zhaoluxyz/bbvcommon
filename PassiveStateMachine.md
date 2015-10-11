The `PassiveStateMachine` is a state machine that performs transitions on the same thread as the code calling the state machine. That means that when the call to `Fire` returns to the caller then the transition is performed.

## Actions queueing events ##
When a transition, exit or entry action fires an event on the state machine then this event is executed immediately after the initial event is processed and before the process flow is returned to the caller of the state machine.

## State Machine Events ##
All events of the state machine are fired on the same thread as the caller.