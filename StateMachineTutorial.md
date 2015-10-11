## Create State Machine ##
Create either a passive or an active state machine:
```
var fsm = new PassiveStateMachine<States, Events>()
```

```
var fsm = new ActiveStateMachine<States, Events>()
```

The above sample uses the enums `States` and `Events` the defined the available states and events.

## Define Transitions ##

### A simple transition: ###
```
fsm.In(States.A)
    .On(Events.B).Goto(States.B);
```
If the state machine is in state `A` and receives event `B` then it performs a transition to state `B`.

### Transition with action ###
```
fsm.In(States.A)
    .On(Events.B)
        .Goto(States.B)
        .Execute( 
            arguments => { /* do something here */ },
            this.MyAction );
```
Actions are defined with `Execute`. Multiple actions can be defined on a single transition. The actions are performed in the same order as they are defined. An action is defined as a delegate. Therefore, you can use lambda expressions or normal delegates.
The following signatures are supported:
```
void TransitionAction(object[] arguments) // to get access to all arguments passed to Fire

void TransitionAction<T>(T parameter) // if Fire is calles with a single argument

void TransitionAction() // for actions that do not need access to the parameters passed to Fire
```

Actions can use the event arguments that were passed to the `Fire` method of the state machine.

### Transition with guard ###
```
fsm.In(States.A)
    .On(Events.B)
        .If(arguments => false).Goto(States.B1)
        .If(arguments => true).Goto(States.B2);
```
Guards are used to decide which transition to take if there are multiple transitions defined for a single event. Guards can use the event arguments that were passed to the `Fire` method of the state machine.
The first transition with a guard that returns `true` is taken.

The signature of guards is as follows:
```
bool Guard(object[] arguments) // to get access to all arguments passed in Fire

bool Guard<T>(T argument) // if a single argument is passed to Fire

bool Guard() // for guards that do not need access to the parameters passed to Fire
```

### Entry and Exit Actions ###
```
fsm.In(States.A)
    .ExecuteOnEntry(() => { /* execute entry action stuff */ }
    .ExecuteOnExit(() => { /* execute exit action stuff */ };
```

The signature of entry and exit actions are as follows:
```
void EntryOrExitAction()
```

### Internal and Self Transitions ###
Internal and Self transitions are transitions from a state to itself.

When an internal transition is performed then the state is not exited, i.e. no exit or entry action is performed.
When an self transition is performed then the state is exited and re-entered, i.e. exit and entry actions, if any, are performed.

```
fsm.In(States.A)
    .On(Events.Self).Goto(States.A) // self transition
    .On(Events.Internal)            // internal transition
```

## Define Hierarchies ##
The following sample defines that `B1` and `B2` are sub states of state `B`.
`B1` is defined to be the initial sub state of state `B`.
```
fsm.DefineHierarchyOn(States.B, States.B1, HistoryType.None, States.B1, States.B2);
```

## History Types ##
When defining hierarchies then you can define which history type is used when a state is re-entered:
  * None
> > The state enters into its initial sub state. The sub state itself enters its initial sub state and so on until the innermost nested state is reached.
  * Deep
> > The state enters into its last active sub state. The sub state itself enters into its last active state and so on until the innermost nested state is reached.
  * Shallow
> > The state enters into its last active sub state. The sub state itself enters its initial sub state and so on until the innermost nested state is reached.

## Initialize, Start and Stop State Machine ##
Once you have defined your state machine then you can start using it.

First you gave to initialize the state machine to set the first state (`A` in the sample):
```
fsm.Initialize(States.A);
```

Afterward, you start the state machine:
```
fsm.Start();
```

Events are processed only if the state machine is started. However, you can queue up events before starting the state machine. As soon as you start the state machine, it will start performing the events.

To suspend event processing, you can stop the state machine:
```
fsm.Stop();
```

If you want, you can then start the state machine again, then stop it, start again and so on.

## Fire Events ##
To get the state machine to do its work, you send events to it:
```
fsm.Fire(Events.B);
```

This fires the event `B` on the state machine and it will perform the corresponding transition for this event on its current state.

You can also pass arguments to the state machine that can be used by transition actions and guards:
```
fsm.Fire(Events.B, anArgument, anotherArgument, yetAnotherArgument);
```

Another possibility is to send a priority event:
```
fsm.FirePriority(Events.B);
```
In this case, the event `B` is enqueued in front of all queued events. This is especially helpful in error scenarios to go to the error state immediately without performing any other already queued events first.

That's it for the tutorial. See rest of documentation for more details on specific topics.