# Introduction #
To get started with the bootstrapper you need the following three things:
  1. Extension interface
  1. Strategy
  1. Bootstrapper setup
## Extension interface ##
The extension interface defines the extension points which are called by the bootstrapper. The custom extension interface must inherit from `IExtension` and can only declare methods which return `void` as extension points. A very basic extension interface could look like the following:

```
    public interface ICustomExtension : IExtension
    {
        void Start();
        void Stop();
    }
```
The extension interface above would indicate that the applications extension points are:
  * `Start`
  * `Stop`
That's basically it for the extension interface. Although we generally advice to build a base class which defines virtual members for the extension points. For example:

```
    public abstract class CustomExtensionBase : ICustomExtension
    {
        public string Name
        {
            get { return this.GetType().FullNameToString(); }
        }

        public virtual void Start() { }
        public virtual void Stop() { }
        public abstract string Describe();
    }
```
This allows inheritors to override only the extension points that they are interested in.

_Note: `IExtension` inherits from `IDescribable` which defines a name and a description. The name and the description are consumed by the reporting capability. The more expressive your names and descriptions are the more meaningful your report will be!_
## Strategy ##
The strategy defines the order of execution for extension points. The custom strategy must inherit from `IStrategy`. For convenience there is a abstract base class `AbstractStrategy{TExtension}` which simplifies defining a custom strategy. A very basic strategy could look like the following:

```
    public class CustomStrategy : AbstractStrategy<ICustomExtension>
    {
        protected override void DefineRunSyntax(ISyntaxBuilder<ICustomExtension> builder)
        {
            builder.Execute(extension => extension.Start());
        }

        protected override void DefineShutdownSyntax(ISyntaxBuilder<ICustomExtension> builder)
        {
            builder.Execute(extension => extension.Stop());
        }
    }
```
The strategy above would configure the applications startup and shutdown phase to:
  * During startup call the extension point `Start` on all registered extensions
  * During shutdown call the extension point `Stop` on all registered extensions
Now let's look how the custom extension interface and the strategy get combined together in the bootstrapper.
## Bootstrapper setup ##
The bootstrapper setup is simply and straight forward. You can always follow the following steps:
  1. Instantiate a new `DefaultBoostrapper<TExtension>`
  1. Instantiate a new strategy
  1. Initialize the bootstrapper with the strategy
  1. Add the required extensions
  1. Call `Run` when the application starts
  1. Call `Shutdown` and then `Dispose` when the application stops.

```
    using(var bootstrapper = new DefaultBootstrapper<ICustomExtension>())
    {
        var strategy = new CustomStrategy();
        bootstrapper.Initialize(strategy);

        bootstrapper.AddExtension(new FirstCustomExtension());
        bootstrapper.AddExtension(...);
        bootstrapper.AddExtension(new NthCustomExtension());
        
        bootstrapper.Run();
		
        // When application finished call
		
        bootstrapper.Shutdown();
    }
```
_Note:`DefaultBootstrapper{TExtension}` and `AbstractStrategy{TExtension}` implement `IDisposable`. The bootstrapper takes care of disposing the strategy and the extensions if the correct behavior is attached. If you are using FxCop to check your compiled code it will produces warnings that you should dispose the strategy and the extensions which implement `IDisposable` before the references move out of scope. You can safely suppress these warnings._
# Advanced #
The bootstrapper can do more! Let us look into a more complex scenario. Often it is required to collect context information during the bootstrapping process and pass this information to the extension points.

Imagine you are using an inversion of control container which intakes `IModule` implementations to register dependencies during the bootstrapping process. All `IModule` implementations need to be passed into the `IContainer` implementation upon construction. For backward compatibility the bootstrapper must automatically check whether an extension implements `IModuleProvider` and register the provided modules on the `IContainer` implementation.

The application we are designing allocates both managed and unmanaged resources which need to be removed when the application is shutting down. To get this behavior some extension occasionally implement `IDisposable` which needs to be called when shutting down.

Furthermore the application uses configuration sections to be able to overwrite some default configurations without recompiling the application. These configuration entries must be parsed and passed to the extensions which rely on their existence.
## Extension interface ##
A custom extension interface for this example could look like the following:

```
    public interface ICustomExtension : IExtension
    {
        void Start();
        void ContainerInitializing(ICollection<IModule> modules);
        void ContainerInitialized(IContainer container);
        void Ready();
        void Stop();
    }
```
The extension interface above would indicate that the applications extension points are:
  * `Start`
  * `ContainerInitializing` which allows to add `IModule` implementations
  * `ContainerInitialized` which allows to resolve dependencies on the `IContainer`
  * `Ready`
  * `Stop`

An abstract base class for this use case:
```
    public abstract class CustomExtensionBase : ICustomExtension
    {
        public string Name
        {
            get { return this.GetType().FullNameToString(); }
        }

        protected IContainer Container { get; private set; }

        public virtual void Start() { }
        public virtual void ContainerInitializing(ICollection<IModule> modules) { }

        public void ContainerInitialized(IContainer container)
        {
            this.Container = container;
	    this.ContainerInitializedCore(container);
        }

        public virtual void Ready() { }
        public virtual void Stop() { }
        public abstract string Describe();
	protected virtual void ContainerInitializedCore(IContainer container) { }
    }
```
This allows inheritors to override only the extension points that they are interested in.

The extension interface does not include:
  * the fact that the extensions must be checked whether they implement `IModuleProvider`
  * the fact that the extensions must be checked whether they implement `IDisposable`
  * the fact that configuration sections must be loaded for an extension if available

How can we extend the bootstrapping mechanism without modifying the custom extension interface? We need behaviors!
## Behaviors ##
Behaviors allow extending the bootstrapping process in an aspect oriented style. Behaviors gain access to extensions which are participating in the bootstrapper process and can therefore influence them for example by injecting additional runtime information into an extension. Behaviors must implement `IBehavior<TExtension>`. They automatically gain access to all extensions participating the bootstrapping process. Behaviors are executed before the corresponding extension point is called.

Let's us look into an example. Imagine you have the following module provider interface below:
```
    public interface IModuleProvider : IEnumerable<IModule> { }
```
The behavior below demonstrates how extensions can be scanned whether they are module providers. If an extension implements `IModuleProvider` all provided modules are automatically collected in the module collection the behavior receives upon construction.
```
    public class ModuleProvidingBehavior : IBehavior<ICustomExtension>
    {
        private readonly ICollection<IModule> modules;
		
        public ModuleProvidingBehavior(ICollection<IModule> modules)
        {
            this.modules = modules;
        }

        public string Name { get { return this.GetType().FullNameToString(); } }

        public void Behave(IEnumerable<ICustomExtension> extensions)
        {
            extensions.OfType<IModuleProvider>()
				.SelectMany(provider => provider, (provider, module) => module)
				.ToList()
				.ForEach(module => this.modules.Add(module));
        }

        public string Describe() { return "Collects all IModule's on extensions which implement IModuleProvider during bootstrapping."; }
    }
```
Below is a sample `IModule` implementation which uses a made up fluent syntax to register dependencies.
```
    public class CustomModule : IModule
    {
        public void Load(IContainer container)
        {
	    container.Register<DependencyUsingBehavior>().ToSelf();
            container.Register<IDependency>().To<Dependency>();
            container.Register<IBehavior<ICustomExtension>>().ToMethod(ctn => ctn.Resolve<DependencyUsingBehavior>());
        }
    }
```
Below an extension which implements IModuleProvider and yield returns a custom module.
_Note:Of course the custom module could also have been added by overriding `ContainerInitializing` and adding the module to the collection. But the sample should demonstrate the power of behaviors._
```
    public class ExtensionWhichIsModuleProvider : CustomExtensionBase, IModuleProvider
    {
        public IEnumerator<IModule> GetEnumerator()
        {
            yield return new CustomModule();
        }

        IEnumerator IEnumerable.GetEnumerator()
        {
            return this.GetEnumerator();
        }

        public override string Describe() { return "Extension which implements IModuleProvider"; }
    }
```
The `DependencyUsingBehavior` is a special behavior which does not consume extensions but execute an operation on a dependency. The dependency is resolved by using constructor injection. To enable constructor injection when constructing the `DependencyUsingBehavior` the `IContainer` implementation must already be built up and the required dependencies registered.
```
    public class DependencyUsingBehavior : IBehavior<ICustomExtension>
    {
        private readonly IDependency dependency;

        public DependencyUsingBehavior(IDependency dependency)
        {
            this.dependency = dependency;
        }

	public string Name { get { return this.GetType().FullNameToString(); } }

        public void Behave(IEnumerable<ICustomExtension> extensions)
        {
            this.dependency.Hello();
        }

        public string Describe() { "Uses IDependency and executes \"Hello()\" in the provided dependency."; }
    }
```
The `DisposeExtensionBehavior` calls Dispose on all extensions which implement `IDisposable`.
```
    public class DisposeExtensionBehavior : IBehavior<IExtension>
    {
	public string Name { get { return this.GetType().FullNameToString(); } }

        public void Behave(IEnumerable<IExtension> extensions)
        {
            foreach (IDisposable extension in extensions.OfType<IDisposable>())
            {
                extension.Dispose();
            }
        }

        public string Describe() { return "Disposes all extensions which implement IDisposable."; }
    }
```
Now all these pieces must be sticked together in the strategy. Let's look how to define it.
## Strategy ##
The strategy defines the order of execution for extension points and behaviors. The custom strategy must inherit from `IStrategy`. For convenience, use the provided abstract base class `AbstractStrategy{TExtension}` which simplifies defining a custom strategy. The strategy could look like the following:
```
    public class CustomStrategy : AbstractStrategy<ICustomExtension>
    {
        private readonly Collection<IModule> modules;
        private readonly Lazy<IContainer> container;

        public CustomStrategy()
        {
            this.modules = new Collection<IModule>();
            this.container = new Lazy<IContainer>(() => new Container());
        }

        protected override void DefineRunSyntax(ISyntaxBuilder<ICustomExtension> builder)
        {
            builder
                .Begin
                    .With(new ConfigurationSectionBehavior())
                    .With(new ExtensionConfigurationSectionBehavior())
                .Execute(extension => extension.Start())
                .Execute(() => this.modules, (extension, modules) => extension.ContainerInitializing(modules))
                    .With(modules => new ModuleProvidingBehavior(modules))
                .Execute(() => this.BuildContainer(), (extension, container) => extension.ContainerInitialized(ctx))
                    .With(container => container.Resolve<IBehavior<ICustomExtension>>())
                .Execute(extension => extension.Ready());
        }

        protected override void DefineShutdownSyntax(ISyntaxBuilder<ICustomExtension> builder)
        {
            builder
                .Execute(extension => extension.Stop())
                .End.With(new DisposeExtensionBehavior());
        }

        protected override void Dispose(bool disposing)
        {
            // Dispose the container
            base.Dispose(disposing);
        }

        private IContainer BuildContainer()
        {
            var lazyContainer = this.container.Value;
            foreach (IModule module in this.modules) {
                module.Load(lazyContainer);
            }
            return lazyContainer;
        }
    }
```

The strategy above would configure the applications startup and shutdown phase to:
  * During startup
    * before calling any extension point load configuration sections
    * and then load extension configuration sections.
    * Call the extension point `Start` on all registered extensions
    * Initialize the context with `this.modules` and call the extension point `ContainerInitializing` with the context on all registered extensions
      * Execute the behavior `ModuleProvidingBehavior` which gains access to the context.
    * Initialize the context by calling `BuildContainer` and call the extension point `ContainerInitialized` with the context on all registered extensions
      * Execute the lazy resolved `IBehavior<ICustomExtension>`
    * Call the extension point `Ready` on all registered extensions
  * During shutdown
    * call the extension point `Stop` on all registered extensions
    * dispose all extensions which implement `IDisposable`
## Syntax ##
The strategy above shows part of the fluent definition syntax the bootstrapper uses to gain knowledge about the startup and shutdown phase of the system. Here the Extended Backus-Naur-Form of the syntax:
```
Syntax = [ Begin ] { Execute } [ End ] .
Begin = "Begin" { With } | End .
End = "End" { EndWith } .
Execute = "Execute" ExecuteAction | ExecuteActionOnExtension | ExecuteActionOnExtensionWithContext .
ExecuteAction = "Expression<Action>" { With | Execute | End } .
ExecuteActionOnExtension = "Expression<Action<TExtension>>" { With | Execute | End } .
ExecuteActionOnExtensionWithContext = "Expression<Func<TContext>>" "Expression<Action<TExtension, TContext>>" { WithOnContext | Execute | EndWithOnContext } .
With = "With" Behavior | LazyBehavior { With | Execute | End } .
WithOnContext = "With" LazyBehaviorWithContext { WithOnContext | Execute | EndWithOnContext } .
EndWith = Behavior | LazyBehavior { EndWith } .
EndWithOnContext = LazyBehaviorWithContext { EndWithOnContext } .
Behavior = "IBehavior<TExtension>" .
LazyBehavior = "Expression<Func<IBehavior<TExtension>>>" .
LazyBehaviorWithContext = "Expression<Func<TContext, IBehavior<TExtension>>>" .
```
![http://bbvcommon.googlecode.com/svn/wikiFiles/Bootstrapper/SyntaxEBNF.png](http://bbvcommon.googlecode.com/svn/wikiFiles/Bootstrapper/SyntaxEBNF.png)
_Note:TODO Check EBNF. The diagramm above is created with the amazing [ebnfvisualize](http://code.google.com/p/ebnfvisualize/)._
# Configuration sections #
The bootstrapper supports loading of configuration sections through behaviors. The behaviors responsible for loading configuration sections must be applied in the begin section of the run syntax.
## ConfigurationSection ##
To be able to load configuration sections the `ConfigurationSectionBehavior` must be added in the strategy.
```
    public class CustomStrategy : AbstractStrategy<ICustomExtension>
    {
        protected override void DefineRunSyntax(ISyntaxBuilder<ICustomExtension> builder)
        {
            builder
                .Begin
                    .With(new ConfigurationSectionBehavior())
				...
        }
    }
```
Furthermore for each extension which needs access to a configuration section there must be a configuration section definition in the application configuration file. By convention the section name must be the class name of the extension which consumes the configuration section. The convention can be controlled by implementing a set of interfaces which is described in the customization section.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="ExtensionWithCustomConfigurationSection" type="bbv.Common.Bootstrapper.Sample.Complex.Configuration.CustomConfigurationSection, bbv.Common.Bootstrapper.Sample"/>
  </configSections>
  <ExtensionWithCustomConfigurationSection remoteOnly="true">
    <font name="TimesNewRoman" size="18"/>
    <color background="000000" foreground="FFFFFF"/>
  </ExtensionWithCustomConfigurationSection>
</configuration>
```
The extension which wants to receive the loaded configuration section must implement `IConsumeConfigurationSection`.
```
    public class ExtensionWithCustomConfigurationSection : CustomExtensionBase, IConsumeConfigurationSection
    {
        private CustomConfigurationSection section;

        public void Apply(ConfigurationSection section)
        {
            this.section = (CustomConfigurationSection)section;
        }
    }
```
## ExtensionConfigurationSection ##
To be able to load extension configuration sections the `ExtensionConfigurationSectionBehavior` must be added in the strategy.
```
    public class CustomStrategy : AbstractStrategy<ICustomExtension>
    {
        protected override void DefineRunSyntax(ISyntaxBuilder<ICustomExtension> builder)
        {
            builder
                .Begin
                    .With(new ExtensionConfigurationSectionBehavior())
				...
        }
    }
```
Furthermore for each extension which needs access to an extension configuration section there must be an extension configuration section definition in the application configuration file. By convention the section name must be the class name of the extension which consumes the extension configuration section. The convention can be controlled by implementing a set of interfaces which is described in the customization section.
```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="ExtensionWithExtensionConfigurationSection" type="bbv.Common.Bootstrapper.Configuration.ExtensionConfigurationSection, bbv.Common.Bootstrapper"/>
  </configSections>
  <ExtensionWithExtensionConfigurationSection>
    <Configuration>
      <add key="EndpointAddress" value="127.0.0.1"/>
      <add key="StartServer" value="true"/>
    </Configuration>
  </ExtensionWithExtensionConfigurationSection>
</configuration>
```
The corresponding extension can now simply declare properties with names matching the keys in the configuration dictionary. The behavior automatically fills the properties with the values found in the configuration dictionary. The bootstrapper tries to automatically convert the values found in the dictionary to the target property type by using `System.Convert.ChangeType`. When complex conversions need to be done it can be customized according to the description in the customization section. _Note: The properties must have an accessible setter._
```
    public class ExtensionWithExtensionConfigurationSection : CustomExtensionBase
    {
        public string EndpointAddress { get; set; }

        public bool StartServer { get; set; }
    }
```
It is also possible to directly consume the configuration dictionary. Simply declare the extension configuration section
```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="ExtensionWithExtensionConfigurationSectionWithDictionary" type="bbv.Common.Bootstrapper.Configuration.ExtensionConfigurationSection, bbv.Common.Bootstrapper"/>
  </configSections>
  <ExtensionWithExtensionConfigurationSectionWithDictionary>
    <Configuration>
      <add key="SerialPort" value="COM3"/>
      <add key="BaudRate" value="9600"/>
    </Configuration>
  </ExtensionWithExtensionConfigurationSectionWithDictionary>
</configuration>
```
The extension must implement `IConsumeConfiguration`. The `Configuration` property must return a dictionary which will be filled with the keys and values found in the corresponding extension configuration section.
```
    public class ExtensionWithExtensionConfigurationSectionWithDictionary : CustomExtensionBase,
        IConsumeConfiguration
    {
        public ExtensionWithExtensionConfigurationSectionWithDictionary()
        {
            this.Configuration = new Dictionary<string, string>();
        }

        public IDictionary<string, string> Configuration { get; private set; }
    }
```
# Reporting #
The reporting mechanism allows creating a full report of the bootstrapping process. To be able to report the bootstrapping process the process must actually run and a reporter must be present. By default the bootstrapper uses a null reporter which does nothing with the report. But it is also possible to hook in a report generator which creates Visio, Enterprise Architect or ... (you name it!) diagrams.

A custom reporter must implement the reporter interface `IReporter`. The reporter receives an `IReportingContext` which contains all necessary information about the bootstrapping process. The custom reporter must be passed upon the construction of the `DefaultBootstrapper<TExtension>`.

```
	new DefaultBootstrapper<ICustomExtension>( /* Input your IReporter */ )
```

The reporting context is structured like the following:

![http://bbvcommon.googlecode.com/svn/wikiFiles/Bootstrapper/ReportingContext.png](http://bbvcommon.googlecode.com/svn/wikiFiles/Bootstrapper/ReportingContext.png)
# Customization #
## Executors ##
Executors are responsible for executing extension points on extensions and the behaviors attached to it. Different executors can be used for the startup and shutdown phase of the application. An executor must implement `IExecutor<TExtension>`. There are two executors:
  * RunExecutor
  * ShutdownExecutor
Executors can be customized by implementing `CreateRunExecutor` and/or `CreateShutdownExecutor` on `IStrategy<TExtension>`. For example below you see sample implementations of a run executor which executes each executable on its own thread.
```
    public class AsynchronousRunExecutor : IExecutor<ICustomExtension>
    {
        public void Execute(ISyntax<ICustomExtension> syntax, IEnumerable<ICustomExtension> extensions, IExecutionContext executionContext)
        {
            foreach (IExecutable<ICustomExtension> executable in syntax)
            {
                using (var worker = new Task(
                    state =>
                    {
                        var e = (IExecutable<ICustomExtension>)state;
                        IExecutableContext executableContext = executionContext.CreateExecutableContext(e);

                        e.Execute(extensions, executableContext);
                    },
                    executable))
                {
                    worker.Start();
                    worker.Wait();
                }
            }
        }
    }
```
And here the strategy...
```
    public class CustomizationStrategy : AbstractStrategy<ICustomExtension>
    {
        public override IExecutor<IComplexExtension> CreateRunExecutor()
        {
            return new AsynchronousRunExecutor();
        }

        public override IExecutor<IComplexExtension> CreateShutdownExecutor()
        {
            return ...;
        }
    }
```
## Executables ##
Executables are the core execution contexts which are executed by the executors. Executables are responsible for calling an extension point and all behaviors attached to it. Normally it should not be necessary to write own executable implementations. One use case could be if you want to special treat some kind of exceptions or log all exceptions thrown by an extension point. To be able to plug in your own executables you must do the following steps:
  * Write your own `IExecutableFactory<TExtension>`
  * Write your own `ActionExecutable`
  * Write your own `ActionOnExtensionExecutable`
  * Write your own `ActionOnExtensionWithInitializerExecutable`
  * Provide new `IExecutableFactory<TExtension>` to the run and shutdown syntax builder in the constructor of the `AbstractStrategy<TExtension>`.
Let's look into an example.
```
    public class DecoratingExecutable<TExtension> : IExecutable<TExtension>
        where TExtension : IExtension
    {
        private readonly IExecutable<TExtension> decoratedExecutable;

        public DecoratingExecutable(IExecutable<TExtension> decoratedExecutable)
        {
            this.decoratedExecutable = decoratedExecutable;
        }

        public string Name { get { return this.decoratedExecutable.Name; } }

        public void Add(IBehavior<TExtension> behavior)
        {
            Console.WriteLine("::: Adding behavior {0}", behavior.GetType().Name);

            this.decoratedExecutable.Add(behavior);

            Console.WriteLine("::: Added behavior {0}", behavior.GetType().Name);
        }

        public void Execute(IEnumerable<TExtension> extensions, IExecutableContext executableContext)
        {
            Console.WriteLine("::: Executing executable {0}", executableContext.Name);

            this.decoratedExecutable.Execute(extensions, executableContext);

            Console.WriteLine("::: Executed executable {0}", executableContext.Name);
        }

        public string Describe() { return this.decoratedExecutable.Describe(); }
    }
```
The executable factory...
```
    public class DecoratingExecutableFactory<TExtension> : IExecutableFactory<TExtension>
        where TExtension : IExtension
    {
        private readonly IExecutableFactory<TExtension> decoratedExecutableFactory;

        public DecoratingExecutableFactory()
            : this(new ExecutableFactory<TExtension>())
        {
        }

        public DecoratingExecutableFactory(IExecutableFactory<TExtension> decoratedExecutableFactory)
        {
            this.decoratedExecutableFactory = decoratedExecutableFactory;
        }

        public IExecutable<TExtension> CreateExecutable(Expression<Action> action)
        {
            IExecutable<TExtension> decoratedExecutable = this.decoratedExecutableFactory.CreateExecutable(action);

            return new DecoratingExecutable<TExtension>(decoratedExecutable);
        }

        public IExecutable<TExtension> CreateExecutable<TContext>(Expression<Func<TContext>> initializer, Expression<Action<TExtension, TContext>> action, Action<IBehaviorAware<TExtension>, TContext> contextInterceptor)
        {
            IExecutable<TExtension> decoratedExecutable = this.decoratedExecutableFactory.CreateExecutable(initializer, action, contextInterceptor);

            return new DecoratingExecutable<TExtension>(decoratedExecutable);
        }

        public IExecutable<TExtension> CreateExecutable(Expression<Action<TExtension>> action)
        {
            IExecutable<TExtension> decoratedExecutable = this.decoratedExecutableFactory.CreateExecutable(action);

            return new DecoratingExecutable<TExtension>(decoratedExecutable);
        }
    }
```
And here the strategy...
```
    public class CustomizationStrategy : AbstractStrategy<ICustomExtension>
    {
        public CustomizationStrategy()
            : base(new SyntaxBuilder<ICustomExtension>(new DecoratingExecutableFactory<ICustomExtension>()), new SyntaxBuilder<ICustomExtension>(new DecoratingExecutableFactory<ICustomExtension>()))
        {
        }
    }
```
## Extension Resolvers ##
An extension resolver allows plugging in extensions into the bootstrapper. All extensions which are resolved by the extension resolver are added after manually added extensions. An extension resolver must implement `IExtensionResolver<TExtension>` and add all resolved extensions to the provided `IExtensionPoint<TExtension>`.
```
    public class CustomExtensionResolver : IExtensionResolver<ICustomExtension>
    {
        public void Resolve(IExtensionPoint<ICustomExtension> extensionPoint)
        {
            extensionPoint.AddExtension(...);
            ...
        }
    }
```
And here the strategy...
```
    public class CustomizationStrategy : AbstractStrategy<ICustomExtension>
    {
        public override IExtensionResolver<ICustomExtension> CreateExtensionResolver()
        {
            return new CustomExtensionResolver();
        }
    }
```
## Reporting Context ##
The reporting context tracks all bootstrapping information during the startup and shutdown phase. If the reporting context creation needs to be customized this can be achieved by overriding the `CreaterReportingContext` method on the `AbstractStrategy<TExtension>`. The custom reporting context must implement `IReportingContext`.
```
    public class CustomizationStrategy : AbstractStrategy<ICustomExtension>
    {
        public override IReportingContext CreateReportingContext()
        {
            return new CustomReportingContext();
        }
    }
```
The default reporting context implementation offers several virtual members to overload the creation of the several child context implementations used in the reporting context. It is generally easier to start with overriding the virtual members instead of creating an own reporting context.
## ConfigurationSection ##
### Section Name ###
As already described by convention the class name of the extension is used to determine the name of the configuration section to load. If there is no configuration section per extension (meaning several extensions need to load the same configuration section) or a different configuration section name than the class name needs to be used, the name detection can customized by implementing `IHaveConfigurationSectionName` on the extension.
```
    public class ExtensionWithConfigurationSectionName : ICustomExtension,
        IHaveConfigurationSectionName, IConsumeConfigurationSection
    {
        public string SectionName { get { return "SomeSpecialSectionName"; } }

        public void Apply(ConfigurationSection section) { }
    }
```
### Configuration Section Loading ###
If a configuration section needs to be loaded from another source than the application configuration file, for example from a database, this can be achieved by implementing `ILoadConfigurationSection` on the extension which needs a special loading mechanism.
```
    public class ExtensionWithCustomizedLoading : ICustomExtension,
        ILoadConfigurationSection, IConsumeConfigurationSection
    {
        public ConfigurationSection GetSection(string sectionName)
        {
           // load from custom source...
        }
		
        public void Apply(ConfigurationSection section) { }
    }
```
## ExtensionConfigurationSection ##
### Section Name ###
See ConfigurationSection / Section Name.
### Configuration Section Loading ###
See ConfigurationSection / Configuration Section Loading.
### Configuration value to property conversion ###
As previously shown the key value pairs loaded from the `ExtensionConfigurationSectionBehavior` can be automatically set in the properties on an extension with automatic destination type conversion. If a special conversion is needed this can be customized by implementing `IHaveConversionCallbacks`:
```

    <ExtensionWithConversionCallbacks>
        <Configuration>
            <add key="EndpointAddress" value="127.0.0.1"/>
        </Configuration>
    </ExtensionWithConversionCallbacks>

    public class ExtensionWithConversionCallbacks : ICustomExtension,
        IHaveConversionCallbacks
    {
        private readonly IDictionary<string, IConversionCallback> conversion;

        public ExtensionWithConversionCallbacks()
        {
            this.conversion = 
		new Dictionary<string, IConversionCallback> { { "EndpointAddress", new FuncConversionCallback((input, prop) => IPAddress.Parse(input)) } };
        }

        public IPAddress EndpointAddress { get; set; }

        public IDictionary<string, IConversionCallback> ConversionCallbacks { get { return this.conversion; } }
    }
```
`FuncConversionCallback` is a small helper class which allows to define a function delegate as conversion callback. Of course the sample above could also be written with the extension itself implementing `IConversionCallback`.

If the default conversion is not suitable it can be overridden by implementing `IHaveDefaultConversionCallback`. You also have to provide an implementation of `IConversionCallback`. The default conversion callback will be called whenever there is no explicit conversion callback defined.