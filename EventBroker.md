## Features ##
  * Synchronous and asynchronous notification
  * Automatic thread switching: to background or UI thread
  * Loose coupling of event topic publishers and subscribers
  * Publishers and subscribers are referenced by weak references. Therefore they can be garbage collected
  * Multiple publishers and/or subscribers for a single event topic
  * Matchers for publications and subscriptions
  * Thorough customizable logging
  * Extension support

![http://bbvcommon.googlecode.com/svn/wikiFiles/EventBroker/EventBroker.png](http://bbvcommon.googlecode.com/svn/wikiFiles/EventBroker/EventBroker.png)

_For a quick introduction see the [Tutorial](EventBrokerTutorial.md)._

## Documentation ##

### Multithreading / Thread Synchronization ###

A subscriber can specify whether its events are handled synchronously or asynchronously.

A publisher can constrain how its event can be handled (synchronously or asynchronously). This is needed for example in events with results in the event arguments that are evaluated by the publisher after firing the event (-> constraint for synchronous handling).

A subscriber can request an automatic thread switch if needed onto a background thread or the user interface thread - thus eliminating the need for manual thread switching (no need for Control.Invoke anymore). There exist the following options:
  * handle on current thread (use Publisher handler)
  * handle on background thread from ThreadPool (use Background handler)
  * handle synchronously on user interface thread (use UserInterface handler)
  * handle asynchronously on user interface thread (use UserInterfaceAsync handler)


### Weak References ###

Publishers and subscribers are referenced with weak references which will not prevent them from being garbage collected like in normal event registration model.


### Loose Coupling / Multiple Publishers and Subscriber ###

The subscriber does not have to know the publisher. Both only need to know the event topic URI (a string identifying the eventtopic uniquely in the system). This facilitates building loosely coupled systems.

An event (or better an event topic) can have multiple publishers and subscribers.


### Matchers ###

Matchers are used to get additional control how events are routed from publishers to subscribers.
You can register matchers on publications, subscription or globally on the event broker.

Whenever an event is published, the event broker determines all possible subscriptions and uses the matchers to select the matching ones. Only these are then notified about the event.

This is for example used when you want to stop calling handler methods if the CancelEventArgs are already cancelled.


### Scopes ###

Only publishers and subscribers that are registered on the same EventBroker are wired together when events are fired. Normally you’ll use a single EventBroker in your system that handles all event notification.

However, in special cases it is useful to define a new EventBroker for a sub system. This gives you the possibility to define a scope for your event notification.

Alternatively, it is possible to use matchers to build a hierarchy of publishers and subscribers. To achieve this, you have to name your publishers and subscribers by implementing . Then you can use the matchers PublishGlobal, PublishToChildren, PublishToParents, SubscribeGlobal, SubscribeToChildren, SubscribeToParents or your own to define which events are handled by your subscribers.


### Advanced Publication and Subscription Registrations ###

When your publisher or subscriber implements the interface _IEventBrokerRegisterable_ then you get hook methods that are called when an instance of this class is registered or unregistered on an event broker. You can then programatically specify publications and subscription without using attributes.

This is useful when you have for example matchers or handlers that cannot be defined compile time constant (constraint of attributes).

For more details, please see the corresponding unit tests.


## Further Information ##

Code samples: See sample project and unit tests in code for more samples.

Performance comparison: http://www.planetgeek.ch/2009/07/12/event-broker-performance/