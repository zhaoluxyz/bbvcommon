# Features #

  * The bootstrapper provides a single entry point to startup and shutdown the application.
  * Fluent definition syntax allowing to expressively define the startup and shutdown behavior of the application.
  * Pluggable behaviors which allow to extend the bootstrapping mechanism in AOP like style.
  * ConfigurationSection support with behavior.
  * Unobtrusive Key/Value pair configuration section support with behavior.
  * Thorough reporting of application startup and shutdown behavior.
  * Heavily extendable and customizable core infrastructure

_For a quick introduction see the [Tutorial](BootstrapperTutorial.md)._

_For the specification see the [Specification](BootstrapperSpecification.md)_

# Documentation #
## Bootstrapper ##
When building a new application the bootstrapper is essentially the only root object which needs to be instantiated in your application main method. The bootstrapper is the central point of your application's startup and shutdown behavior.
## Extensions ##
Extensions contain methods which are called upon startup and shutdown of the bootstrapper. The methods of the extensions are extension points which can consume information gathered during the bootstrapping process for example configuration. Extensions are added directly on the bootstrapper.
## Extension Resolver ##
An extension resolver allows to plug in extensions into the bootstrapper. All extensions which are resolved by the extension resolver are added after manually added extensions.
## Behaviors ##
Behaviors allow to extend the bootstrapping process in an aspect oriented style. Behaviors gain access to extensions which are participating in the bootstrapper process and can therefore influence them for example by injecting additional runtime information into an extension.
## Strategy ##
The strategy defines the order of execution for extension points and behaviors. The strategy is also the context provider for context information which needs to be passed to the extension points. The strategy allows to hooking in custom executors, extension resolver and reporting context.
## Executors ##
Executors are responsible for executing extension points on extensions and the behaviors attached to it. Different executors can be used for the startup and shutdown phase of the application.
## Core behaviors ##
The bootstrapper package ships with some core behaviors which can be plugged into your bootstrapping mechanism. Core behaviors include
  * Support to automatically load configuration sections
  * Support for bootstrapper configuration sections which are basically key/value pairs and their values get automatically propagated to properties of extensions
  * Support to automatically dispose extensions which implement IDisposable
## Reporting ##
The bootstrapper records the bootstrapping process and allows reporting the whole process to a reporter. This can be used to automatically generate architecture and design documents which describe the bootstrapping mechanism.
# Further Information #
Code samples: See sample project and unit tests in code for more samples.