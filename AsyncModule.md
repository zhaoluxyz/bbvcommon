The `AsyncModule` support the developer in two major steps of asynchronous programming:
  * Creating code that runs asynchronously in its own thread.
  * Communication between threads.

The components in `AsyncModule` provide an easy way to set up asynchronously operating modules with a few attributes. With the attribute driven approach the changes to existing code are minimal.

The communication with asynchronous modules is message and queue based.

# Features #
  * Asynchronous processing of free definable messages on a background worker thread.
  * Messages are synchronized over a queue, which means that the order of messages is obtained.
  * Worker threads can be started and stopped anytime
    * When stopping a worker thread, you can define a time out during which a message already processing has time to finish.
    * Extensibility: you can add extensions that can hook up several extension points in the message processing:
      * Before and after  a module starts.
      * Before and after a module stops.
      * Before and after a message is enqueued.
      * Before and after a message is consumed.
    * Logging of all enqueued and processed messages.
    * Exception handling that guarantees that the worker thread does not die.

# Message Driven Asynchronous Execution #
In order to execute methods of a class as an asynchronous module, you have to do the following:

Define a class that contains methods that should be executed asynchronously:

```
    public class Module
    {
        
    }
```

Create a `ModulController` for your class somewhere in your code:

```
    Module module = new Module();
    IModuleController controller = new ModuleController(module);
```

Add a message consumer method to the module:

```
    public class Module
    { 
        [MessageConsumer]
        public void ConsumeMessage(Message message)
        {
            
        }
    }
```

This method acts as a message consumer for all messages of the type specified by the parameter message. That means you can have several message consumer methods that consume different types of messages, thus eliminating an inner switch statement to tell the messages apart from each other.

Start the asynchronous message processing:

```
    controller.Start();
```

Send messages to your module:

```
    controller.EnqueueMessage(new Message("Test"));
```

This message will cause that the `ConsumeMessage` method on your module is invoked on the worker thread of the controller and the `new Message("Test")` is passed as argument.