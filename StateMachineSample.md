This is a sample state machine

![http://bbvcommon.googlecode.com/svn/wikiFiles/StateMachine/ElevatorStateMachine.gif](http://bbvcommon.googlecode.com/svn/wikiFiles/StateMachine/ElevatorStateMachine.gif)

## Definition ##

```
var elevator = new PassiveStateMachine<States, Events>("Elevator");

elevator.DefineHierarchyOn(States.Healthy, States.OnFloor, HistoryType.Deep, States.OnFloor, States.Moving);
elevator.DefineHierarchyOn(States.Moving, States.MovingUp, HistoryType.Shallow, States.MovingUp, States.MovingDown);
elevator.DefineHierarchyOn(States.OnFloor, States.DoorClosed, HistoryType.None, States.DoorClosed, States.DoorOpen);

elevator.In(States.Healthy)
    .On(Events.ErrorOccured).Goto(States.Error);

elevator.In(States.Error)
    .On(Events.Reset).Goto(States.Healthy)
    .On(Events.ErrorOccured);

elevator.In(States.OnFloor)
    .ExecuteOnEntry(this.AnnounceFloor)
    .ExecuteOnExit(Beep, Beep)
    .On(Events.CloseDoor).Goto(States.DoorClosed)
    .On(Events.OpenDoor).Goto(States.DoorOpen)
    .On(Events.GoUp)
        .If(CheckOverload).Goto(States.MovingUp)
        .Otherwise().Execute(this.AnnounceOverload, Beep)
    .On(Events.GoDown)
        .If(CheckOverload).Goto(States.MovingDown)
        .Otherwise().Execute(this.AnnounceOverload);

elevator.In(States.Moving)
    .On(Events.Stop).Goto(States.OnFloor);

elevator.Initialize(States.OnFloor);
```

The above state machine uses these actions and guards:
```
private void AnnounceFloor()
{
    /* announce floor number */
}

private void AnnounceOverload()
{
    /* announce overload */
}

private void Beep()
{
    /* announce overload */
}

private bool CheckOverload()
{
    return whetherElevatorHasOverload;
}
```

## Run the State Machine ##

This is a small sample to show how to interact with the state machine:
```
// queue some events to be performed when state machine is started.
elevator.Fire(Events.ErrorOccured);
elevator.Fire(Events.Reset);
            
elevator.Start();

// these events are performed immediately
elevator.Fire(Events.OpenDoor);
elevator.Fire(Events.CloseDoor);
elevator.Fire(Events.GoUp);
elevator.Fire(Events.Stop);
elevator.Fire(Events.OpenDoor);

elevator.Stop();
```

## Log ##

If you add

```
elevator.AddExtension(new Extensions.Log4NetExtension<States, Events>("Elevator"));
```

to the above code then these are the log messages (if all are enabled - see log4net documentation on how to configure log messages).

Note how the state exits and enters are logged, especially for hierarchical transitions.

```
Logger      Level   Message
Elevator    INFO    State machine Elevator initializes to state OnFloor.
Elevator    INFO    State machine Elevator switched from state  to state DoorClosed.
Elevator    DEBUG    State machine Elevator performed  -> Enter Healthy -> Enter OnFloor -> Enter DoorClosed.
Elevator    INFO    Fire event ErrorOccured on state machine Elevator with current state DoorClosed and event arguments .
Elevator    INFO    State machine Elevator switched from state DoorClosed to state Error.
Elevator    DEBUG    State machine Elevator performed  -> Exit DoorClosed -> Exit OnFloor -> Exit Healthy -> Enter Error.
Elevator    INFO    Fire event Reset on state machine Elevator with current state Error and event arguments .
Elevator    INFO    State machine Elevator switched from state Error to state DoorClosed.
Elevator    DEBUG    State machine Elevator performed  -> Exit Error -> Enter Healthy -> Enter OnFloor -> Enter DoorClosed.
Elevator    INFO    Fire event OpenDoor on state machine Elevator with current state DoorClosed and event arguments .
Elevator    INFO    State machine Elevator switched from state DoorClosed to state DoorOpen.
Elevator    DEBUG    State machine Elevator performed  -> Exit DoorClosed -> Enter DoorOpen.
Elevator    INFO    Fire event CloseDoor on state machine Elevator with current state DoorOpen and event arguments .
Elevator    INFO    State machine Elevator switched from state DoorOpen to state DoorClosed.
Elevator    DEBUG    State machine Elevator performed  -> Exit DoorOpen -> Enter DoorClosed.
Elevator    INFO    Fire event GoUp on state machine Elevator with current state DoorClosed and event arguments .
Elevator    INFO    State machine Elevator switched from state DoorClosed to state MovingUp.
Elevator    DEBUG    State machine Elevator performed  -> Exit DoorClosed -> Exit OnFloor -> Enter Moving -> Enter MovingUp.
Elevator    INFO    Fire event Stop on state machine Elevator with current state MovingUp and event arguments .
Elevator    INFO    State machine Elevator switched from state MovingUp to state DoorClosed.
Elevator    DEBUG    State machine Elevator performed  -> Exit MovingUp -> Exit Moving -> Enter OnFloor -> Enter DoorClosed.
Elevator    INFO    Fire event OpenDoor on state machine Elevator with current state DoorClosed and event arguments .
Elevator    INFO    State machine Elevator switched from state DoorClosed to state DoorOpen.
Elevator    DEBUG    State machine Elevator performed  -> Exit DoorClosed -> Enter DoorOpen.
```

You can write your own extension for different logging.

## Report ##

There exist 3 different state machines out of the box (you can add your own by implementing `IStateMachineReport`)

### .graphml report that can be automatically layout with yEd ###

The picture on top of this page shows this type of report.

### csv files for states and transitions ###

| **Source** | **Entry** | **Exit** | **Children** |
|:-----------|:----------|:---------|:-------------|
| OnFloor    | AnnounceFloor | Beep, Beep | DoorClosed, DoorOpen |
| Moving     |           |          | MovingUp, MovingDown |
| Healthy    |           |          | OnFloor, Moving |
| MovingUp   |           |          |              |
| MovingDown |           |          |              |
| DoorClosed |           |          |              |
| DoorOpen   |           |          |              |
| Error      |           |          |              |

| **Source** | **Event** | **Guard** | **Target** | **Actions** |
|:-----------|:----------|:----------|:-----------|:------------|
| OnFloor    | CloseDoor |           | DoorClosed |             |
| OnFloor    | OpenDoor  |           | DoorOpen   |             |
| OnFloor    | GoUp      | CheckOverload | MovingUp   |             |
| OnFloor    | GoUp      |           | internal transition | AnnounceOverload, Beep |
| OnFloor    | GoDown    | CheckOverload | MovingDown |             |
| OnFloor    | GoDown    |           | internal transition | AnnounceOverload |
| Moving     | Stop      |           | OnFloor    |             |
| Healthy    | ErrorOccured |           | Error      |             |
| Error      | Reset     |           | Healthy    |             |
| Error      | ErrorOccured |           | internal transition |             |


### Textual State Machine Report `StateMachineReportGenerator` ###

```
Elevator: initial state = OnFloor
    Healthy: initial state = OnFloor history type = Deep
        entry action: 
        exit action: 
        ErrorOccured -> Error actions:  guard: 
        OnFloor: initial state = DoorClosed history type = None
            entry action: AnnounceFloor
            exit action: Beep, Beep
            CloseDoor -> DoorClosed actions:  guard: 
            OpenDoor -> DoorOpen actions:  guard: 
            GoUp -> MovingUp actions:  guard: CheckOverload
            GoUp -> internal actions: AnnounceOverload, Beep guard: 
            GoDown -> MovingDown actions:  guard: CheckOverload
            GoDown -> internal actions: AnnounceOverload guard: 
            DoorClosed: initial state = None history type = None
                entry action: 
                exit action: 
            DoorOpen: initial state = None history type = None
                entry action: 
                exit action: 
        Moving: initial state = MovingUp history type = Shallow
            entry action: 
            exit action: 
            Stop -> OnFloor actions:  guard: 
            MovingUp: initial state = None history type = None
                entry action: 
                exit action: 
            MovingDown: initial state = None history type = None
                entry action: 
                exit action: 
    Error: initial state = None history type = None
        entry action: 
        exit action: 
        Reset -> Healthy actions:  guard: 
        ErrorOccured -> internal actions:  guard: 
```