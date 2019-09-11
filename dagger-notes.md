- Component (an interface) creates, stores our objects and provide them to us. You can also call it The Injector. It is aware of the dependency graph (DAG). By adding **@Component** Dagger will implement the interface and create all necessary code
- Name convention of implemented component interface: *DaggerComponentInterfaceName*
- In order for Dagger to instantiate a class, class constructor has to be marked with **@Inject**, remember that would also have the effect of injecting constructor params
- Generally we want to do constructor injection everywhere but it is not possible e.g. if root like an android Activity instantiation is not in our control so then we instantiate *DaggerComponentInterfaceName* to access injected object or use Field Injection but for that you need to give the Component access to parent object (e.g. Activity) via *inject* method
- Injection order in a class: Constructor, Fields & then Methods
- Field & Method injection are automatically executed if we also do a constructor injection (but this won’t happen in case of Android activity so we manually trigger it via Component)
- By putting **@Inject** on a constructor you are saying hey Dagger this is how you instantiate an object of this class but e.g. a third party class won’t allow that (as we don’t have access to code) so this is where **@Module** comes in which uses **@Provides**
- **@Provides** also helps to do any configuration on an object after instantiation 
- **@Module** is just a java class, used for providing/injecting objects in more complex cases
- specifying a Module in **@Component** annotation tells the component where to get a certain class instance from, you can specify multiple modules in {curly braces} & comma separate them
- you use @Binds (instead of **@Provides**) whenever you want to provide a concrete class against an interface return type, bind does not support any configuration since there is no method body
- if you need to provide arguments (which could be runtime) then you use *.builder()* on component instead of *.create()*. This way you have introduced State into your module
- with **@BindsInstance** (and **@Component.Builder**) we can get variables (e.g. int) into our dependency graph at runtime which has same effect as passing a runtime value to a module but with latter approach there is drawback of Dagger creating an additional instance to hold that state in Module instance. Values get associated/injected based on data type hence we use **@Named**. With **@BindsInstance** approach you can leave Module abstract
- You can annotate **@Binds**, **@Provides** with **@Singleton** in addition to the class itself. A singleton is specific to a component so if you want an application scope singleton then associate component with Application lifecycle
- Custom Scope are used to reduce the lifecycle of an object e.g. to a an Activity, Fragment etc.
- **@Documented** and **@Retention** are meta annotations for **@Scope**, don't matter much, you annotate an interface with **@Scope** to create a custom scope which makes it act like annotations. 
- The name of interface indicates what is the scope e.g. *PerActivity.java* (**@PerActivity**) and you annotate a class with that interface name (annotation) to modify class lifecycle scope BUT you have to change/limit scope by associating it with another component e.g. a Component for an Activity a Component for the application
- You annotate the new component (e.g. ActivityComponent.java) with the custom scope e.g. **@PerActivity** to establish that relationship
- You generally use **@Singleton** on an app component
- These annotations (e.g. **@Singleton**) can be written in multiple places e.g. with a provide method
- You can also provide Module a constructor which is sometimes used to provide application context (only available at runtime) to app component
- specifying a component dependency (*dependencies =*) in @Component annotation tells Dagger that this component needs the dependencies inside the other Component (e.g. App Component) to work. You will have to pass app component dependency (*.appComponent()*) when building the activity component and this is how we make the connection between two components so to use their objects together
- Ultimately we are responsible for managing the scopes and the way we do it is by instantiating and releasing our different dagger components in correct places. Components are just java objects who get released from memory when there is no reference to them.
- There is another way to connect components: **@Subcomponent**, a subcomponents knows about all the objects of its parent component e.g. if we turn our activity component into sub component it'll be able to access the dependency graph of the app component
- the parent component has to provide an accessor for the subcomponent
- **Component Dependencies:** Use this when you want to keep two components independent
- **Subcomponents:** Use this when you want to keep two components coupled

### Source
https://www.youtube.com/playlist?list=PLrnPJCHvNZuA2ioi4soDZKz8euUQnJW65
