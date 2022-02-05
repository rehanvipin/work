---
title: "Services"
weight: 4
---
# Services
They are classes where you can put business logic for components. They should be specific. Components and services should be as reusable as possible. This is a good, short read about them: [Services and DI](https://angular.io/guide/architecture-services).

## Creating them
You usually create one in a `.service.ts` file. It is an exported class with functions which perform the required business actions. It can then be **injected** into the required classes. It might depend on other services too.

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