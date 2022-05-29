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
You can specify the provider as the value to the `provider` key in the metadata object. If you specify `'root'` one instance will be available to all components.
<--->

#### With an `NgModule`
You specify the provider (e.g. the service class) in the `providers` array and one instance will be available to all components in the module.
<--->

#### With a component
You can place it in the `providers` array in the metadata of a component and it will get its own instance of that service.

{{< /columns >}}

To use a function as a provider [check out this example](./routing/#deactivation).

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