---
title: "Testing"
weight: 10
---
# Unit & Integrated tests
Angular uses Jasmine for unit testing and Karma to run the tests on browsers. Tests can be:

{{< columns >}}

#### Isolated
* Only tests the JS class not the component
* Mostly used for services and pipes
* Need to create instances of classes on your own
<--->
#### Integrated
* Tests the class and template together
* Mostly used for components and directives
* The objects are created by the framework

{{< /columns >}}

## Unit tests
They test a single unit (like a class, function, or a bunch of classes) in isolation. Some features of unit tests:
* Fast (to code and to execute)
* Should handle one state change at max
* Assert one thing
* Minimal dependencies

They follow a structure of:
* Assemble - The setup code for the test
* Action - The thing that the user will likely do. Can be a state change
* Assert - "Is the result what is expected?"

Run unit tests with `ng test` which asks Karma to look for *.spec.ts files and execute the tests within them.
A basic test looks like this:
```ts
describe('a nice test', () => {
    // do something
    beforeEach(() => {
        // code to run before each 'it'
    })

    it('exact test stuff', () => {
        // do something
        expected(valA).toBe('good stuff');
    })
})
```
{{< hint "info" >}}
`toBe` is just one of the many "matchers" jasmine has. The official docs has [a list of matchers](https://jasmine.github.io/api/4.0/matchers)
{{< /hint >}}

### Mocking
To minimize depedencies on external code they are **mocked** for things like network calls, DB use, side effects etc. E.g. instead of making HTTP requests with the http dependency use a mock http dependency that simulates network calls and returns results immediately.

* Mock with jasmine like this: `const mockStuff jasmine.createSpyObj('some desc', ['method1', 'method2', 'method3'])`.
* A particular method can be modified to return a particular value like this: `mockStuff.method1.and.returnValue(anyValue)`
* To check if a method is being called in the function with particular params, use: `expect(mockStuff.method2).toHaveBeenCalledWith(param1, param2)` where the parameters can be exact values or it can just check that the types match by passing in an arg like `jasmine.any(ClassName)`
* Components can be tested just like services for isolated tests. Instantiate the class with mock dependencies, set any input params and then test whatever methods needed to be tested.