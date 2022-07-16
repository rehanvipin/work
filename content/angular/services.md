---
title: "Services"
weight: 4
---
# Services (do STUFF)
They are classes where you can put business logic for components. They should be specific. Components and services should be as reusable as possible. This is a good, short read about them: [Services and DI](https://angular.io/guide/architecture-services).

According to that page linked above:
> A component can delegate certain tasks to services, such as fetching data from the server, validating user input, or logging directly to the console

## Creating them
You usually create one in a `.service.ts` file. It is an exported class with functions which perform the required business actions. It can then be **injected** into the required classes. It might depend on other services too.

Providers tell injectors how to obtain or create the required dependencies.

## Using them
Services don't just need to be provided in modules or the root, it's also possible to provide a service
object just for a component by setting the providers property in the component metadata.

Read more about [dependency injection here](./di).

TODO: Read this to learn how they are used: https://angular.io/guide/providers.