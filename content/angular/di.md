---
title: "Dependency Injection"
weight: 9
---
# Adding features! 🙌
## Dependency injection
This is how you provide new components with extra resources they need to function. To indicate that a class (**any class**, not just a component / service) has a dependency or is a dependency you decorate it with `@Injectable()`. From the docs
> The injector is the main mechanism. Angular creates an application-wide injector for you during the bootstrap process, and additional injectors as needed. You don't have to create injectors.
>
> An injector creates dependencies, and maintains a container of dependency instances that it reuses if possible.

Some important points:
* You declare dependencies of a class in its constructor
* For each dependency, you must have registered a **provider** with the injector. A provider creates the dependency object when required. (For a service, it can be the service class itself)

Again, from the docs listed at the top of the page:
> When Angular discovers that a component depends on a service, it first checks if the injector has any existing instances of that service. If a requested service instance doesn't yet exist, the injector makes one using the registered provider, and adds it to the injector before returning the service to Angular.
>
> When all requested services have been resolved and returned, Angular can call the component's constructor with those services as arguments.

### Register a provider
You can do it in three ways:
{{< columns >}}

#### In `@Injectable()`
You can specify the provider as the value to the `providedIn` key in the metadata object. If you specify `'root'` one instance will be available to all components.
<--->

#### With an `NgModule`
You specify the `providedIn` property as the `NgModule` where you want this service to be available.
<--->

#### With a component
You can place it in the `providers` array in the metadata of a component and it will get its own instance of that service.

{{< /columns >}}

To use a function as a provider [check out this example](./routing/#deactivation).
It is preferred to provide the service in the root injector unless there's a requirement to make it
available only in some ngmodule.

## Using `InjectToken`
This is a way for the injector to find the required provider from the list of available providers. Its an alternative to using a string. Its better since the token is a variable and the compiler will complain if there is another variable with the same name.

Create a new token like this:
```ts
import { InjectionToken } from '@angular/core'
let SOME_TOKEN = new InjectionToken<retType>('whatever description you want');
// Its a convention to capitalize the token name
// retType is the type of the object that this provider will create
// "any" is a good option in case of a third-party object like jQuery
```

Use it to register a new provider. The `provide` property will be set to `SOME_TOKEN`. The other prop. e.g. `useValue` contains the object that you want to provide e.g. jQuery's `$`.

The object can now be used via DI like so:
```ts
import { Inject } from '@angular/core'
import { SOME_TOKEN } from './something.service'
constructor(@Inject(SOME_TOKEN) private jqSelector) {}
ngOnInit() {
    this.jqSelector.doSomething()
}
```

## `useWhat`
Alternatives to `useValue`
* The usual way where you put the service class within the `providers` array of the ngModule is a shorthand for `{provider: someGoodClass, useClass: someGoodClass}`
* `useExisting` lets you use a different, **already registered (i.e. existing)**, provider when the user requests one provider. Something like this: `{provider: someGoodClass, useExisting: someShadyClass}`
* `useFactory` lets you use a function to create an object. This is complex. [Check out the docs](https://angular.io/guide/dependency-injection-providers#using-factory-providers).

## Injectors
Angular has three kinds of Injectors (and each have their hierarchies) which can be used when a dependency is requested:
1. ElementInjector (providing a dependency at a component level)
2. EnvironmentInjector (the root injector is in this hierarchy)
3. ModuleInjector (only for apps which use ngModule)

The above is also the resolution order. If nothing works, it'll end up searching the NullInjector and throwing an error.

It is also possible to customize the default resolution algorithm with some decorators:
* `@Optional()` will give a `null` value instead of throwing an error if a dependency is not found.
* `@Self()` will look into the current component's ElementInjector only

In a eager-loaded app, all the providers of all modules are available throughout the app. (Whatt??? Don't worry, this only happens for providedIn: 'any')  
For lazy-loaded modules, there is a new injector created for each loaded module which is then added to the injector hierarchy.