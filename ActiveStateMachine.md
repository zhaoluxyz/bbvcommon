The `ActiveStateMachine` is a state machine that has its own worker thread on which the transitions are performed.
The program flow returns to the caller of the state machine immediately after the event was queued.


# Actions Queueing Events #
When an action fires an event onto the state machine then this event is queued and the action continues.

Note that other threads may add events themselves. There is no guarantee that the event fired by the action is the next event actually executed.


# Events #
The state machine fires the events on its worker thread.