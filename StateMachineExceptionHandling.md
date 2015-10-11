The passive and active state machine catch all exceptions that are thrown. This is to prevent the caller from having to handle such an exception. The reason is that the caller normally does not know enough about the state machine to take a clever action to handle an exception.

Therefore, the state machine fires events in case of an exception. The "holder" of the state machine can register these events and act correspondingly:

```
stateMachine.ExceptionThrown += (sender, eventArgs) =>
                                   {
                                      // react to error
                                   };

stateMachine.TransitionExceptionThrown += (sender, eventArgs) =>
                                   {
                                      // react to transition error
                                   };
```

Normally, you want send the state machine in a error state to reflect this situation.

If you do not register the error events, the state machine will throw exceptions anyway to prevent that exceptions are swallowed.