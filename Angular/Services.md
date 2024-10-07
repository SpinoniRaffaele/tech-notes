# Services

Service: logics that is shared between multiple components, or external communication
Dependency Injection

It can solve problem like

* Importing the same object everywhere (singleton)
* Create a new instance everytime (factory)
* Create environment specific variables

Service file should end with service.ts and the class inside should end with Service too.
It needs the @Injectable() tag only if it uses other service injected inside it.
It is used with Injection in order to implement the IoC (inversion of control) principle

**Hierarchical Injector**
Automatical creation of class instantiation (like spring beans).
If we provide a service on a component A, the service instance is available for all the components child of the component A. for example if I inject (putting in the **providers** array) in the app.component, the service instance will be available for all the components (but not for other services, in order to make it available to other services inject it in the App Module)
When I'm using a single Service declared in the AppModule Provider's and injected in multiple components, I'm basically applying the singleton pattern.

to avoid strange behaviors you should avoid injecting the service in components and in their child too (creating multiple instance), instead let the component inherit the instance of the service (otherwise the state data is not going to be consistent).

In order to have the service injected you should put it as an input of the constructor using the argument decorator **@Inject** before it.
Another way is to specify the **providers: \[MyService\]** inside the @Component tag (this way doesn't need the inject tag).  If you want to import the service in the module you should use this syntax.

When you are writing **providers: \[MyService\]**   you are using the abbreviation for **providers: \[{provide: MyService, useClass: MyService}\]**
Here you can create different mappings, for example you can provide an interface, and in different modules provide different implementations as different classes In useClass.
We can do the same with constants: **providers: \[{provide: 'URL', useValue: '[www.value.com](http://www.value.com)'}\]**  and then use it like this (with Inject): **constructor(@Inject('URL')) url: string)**

You can use eventEmitter Inside the service to implement cross-components communications without the need to go through the whole chain of components. At the downside of having the service

If you store a state in a service then you can either pass directly the data of the state or copy it, but if you copy you will need to inform the components of the change of the value (if any), so you need to use an event emitter and emit the new value everytime is changed (ideally the change should be done by calling a function of the service itself).
Then in the components you subscribe from the ngInit in order to always have an updated copy of the value.
