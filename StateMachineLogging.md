# log4net #

There exists a log4net extension that provides logging using log4net.

You can add an instance of the `Log4NetExtension` to the state machine:

```
stateMachine.AddExtension(new Log4NetExtension<States, Events>());
```

You can define the logger that log4net uses by specifing the name in the constructor:

```
stateMachine.AddExtension(new Log4NetExtension<States, Events>("my.logger.name"));
```

# custom logging #

If you want to change either the logging framework or what and how is logged then you can write your own logging extension. See [Extensions](StateMachineExtensions.md) for information how to write an extension for the state machine.