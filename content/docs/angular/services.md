---
title: "Services"
weight: 4
---
# Services (do STUFF)
They are classes where you can put business logic for components. They should be specific. Components and services should be as reusable as possible. This is a good, short read about them: [Services and DI](https://angular.io/guide/architecture-services).

## Creating them
You usually create one in a `.service.ts` file. It is an exported class with functions which perform the required business actions. It can then be **injected** into the required classes. It might depend on other services too.

Read more about [dependency injection here](./di).