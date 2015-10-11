# Introduction #

Extensions are used to change or access internals of the state machine. An example for an extension is the `Log4NetExtension` ([Logging](Logging.md)) which can be used for logging with log4net.

# Changing the Behaviour of a State Machine #

Extensions can change the behaviour of the state machine in these ways:
  * change the state into which the state machine is initialized
  * change the event and/or event arguments fired on the state machine
  * change the exceptions thrown by the state machine with custom exceptions
  * Firing events on the state machine as a reaction to a call on the extension (e.g. perform transition to the error state on an exception)

# Interface #

A state machine extension has to implement the following interface:

```
    /// <summary>
    /// Extensions for a state machine have to implement this interface.
    /// </summary>
    /// <typeparam name="TState">The type of the state.</typeparam>
    /// <typeparam name="TEvent">The type of the event.</typeparam>
    public interface IExtension<TState, TEvent>
        where TState : struct, IComparable
        where TEvent : struct, IComparable
    {
        /// <summary>
        /// Called after the state machine switched states.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="oldState">The old state.</param>
        /// <param name="newState">The new state.</param>
        void SwitchedState(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            IState<TState, TEvent> oldState, 
            IState<TState, TEvent> newState);

        /// <summary>
        /// Called when the state machine is initializing.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="initialState">The initial state. Can be replaced by the extension.</param>
        void InitializingStateMachine(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            ref TState initialState);

        /// <summary>
        /// Called when the state machine was initialized.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="stateContext">The state context of the initial state.</param>
        /// <param name="initialState">The initial state.</param>
        void InitializedStateMachine(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            StateContext<TState, TEvent> stateContext, 
            TState initialState);

        /// <summary>
        /// Called when an event is firing on the state machine.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="eventId">The event id. Can be replaced by the extension.</param>
        /// <param name="eventArguments">The event arguments. Can be replaced by the extension.</param>
        void FiringEvent(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            ref TEvent eventId, 
            ref object[] eventArguments);

        /// <summary>
        /// Called when an event was fired on the state machine.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="context">The transition context.</param>
        void FiredEvent(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            TransitionContext<TState, TEvent> context);

        /// <summary>
        /// Called before an entry action exception is handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="state">The state.</param>
        /// <param name="stateContext">The state context.</param>
        /// <param name="exception">The exception. Can be replaced by the extension.</param>
        void HandlingEntryActionException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            IState<TState, TEvent> state, 
            StateContext<TState, TEvent> stateContext, 
            ref Exception exception);

        /// <summary>
        /// Called after an entry action exception was handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="state">The state.</param>
        /// <param name="stateContext">The state context.</param>
        /// <param name="exception">The exception.</param>
        void HandledEntryActionException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            IState<TState, TEvent> state, 
            StateContext<TState, TEvent> stateContext, 
            Exception exception);

        /// <summary>
        /// Called before an exit action exception is handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="state">The state.</param>
        /// <param name="stateContext">The state context.</param>
        /// <param name="exception">The exception. Can be replaced by the extension.</param>
        void HandlingExitActionException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            IState<TState, TEvent> state, 
            StateContext<TState, TEvent> stateContext, 
            ref Exception exception);

        /// <summary>
        /// Called after an exit action exception was handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="state">The state.</param>
        /// <param name="stateContext">The state context.</param>
        /// <param name="exception">The exception.</param>
        void HandledExitActionException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            IState<TState, TEvent> state, 
            StateContext<TState, TEvent> stateContext, 
            Exception exception);

        /// <summary>
        /// Called before a guard exception is handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="transition">The transition.</param>
        /// <param name="transitionContext">The transition context.</param>
        /// <param name="exception">The exception. Can be replaced by the extension.</param>
        void HandlingGuardException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            ITransition<TState, TEvent> transition, 
            TransitionContext<TState, TEvent> transitionContext, 
            ref Exception exception);

        /// <summary>
        /// Called after a guard exception was handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="transition">The transition.</param>
        /// <param name="transitionContext">The transition context.</param>
        /// <param name="exception">The exception.</param>
        void HandledGuardException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            ITransition<TState, TEvent> transition, 
            TransitionContext<TState, TEvent> transitionContext, 
            Exception exception);

        /// <summary>
        /// Called before a transition exception is handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="transition">The transition.</param>
        /// <param name="context">The context.</param>
        /// <param name="exception">The exception. Can be replaced by the extension.</param>
        void HandlingTransitionException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            ITransition<TState, TEvent> transition, 
            TransitionContext<TState, TEvent> context, 
            ref Exception exception);

        /// <summary>
        /// Called after a transition exception is handled.
        /// </summary>
        /// <param name="stateMachine">The state machine.</param>
        /// <param name="transition">The transition.</param>
        /// <param name="transitionContext">The transition context.</param>
        /// <param name="exception">The exception.</param>
        void HandledTransitionException(
            IStateMachineInformation<TState, TEvent> stateMachine, 
            ITransition<TState, TEvent> transition, 
            TransitionContext<TState, TEvent> transitionContext, 
            Exception exception);
    }
```

You can use the `ExtensionBase` class in the namespace `bbv.Common.StateMachine.Extensions` to derive your extensions. The `ExtensionsBase` class implements all interface members with virtual methods.