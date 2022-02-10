---
title: "Rxjs"
weight: 9
---
# Async & network stuff
Not everything happens right at once in a webapp. It shouldn't wait for certain events when it can execute other things. This is where asynchronous processing is useful. Promises were the preferred way to do it but now there are observables (from rxjs). Promises represent **one event that happens in the future** while observables represent **zero or more events that could happen synchronously or in the future**. This particular section is about RxJS and HTTP in Angular.

Good resources for RxJS:
* [A gist that explains reactive programming](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
* [A single page crash course giving an overview of RxJS](https://www.learnrxjs.io/learn-rxjs/concepts/rxjs-primer)
* [Overview of main concepts. Official docs](https://rxjs.dev/guide/overview)
* [Video showing off some RxJS stuff live](https://youtu.be/ewcoEYS85Co)
* [Video about RxJS stuff specific to Angular](https://www.youtube.com/watch?v=65Us8NwmYf4)

## HTTP
To communicate via HTTP in JS first import `HttpClientModule` from "@angular/common/http" into the ngModule of your choice. Wherever required, a dependency of type `HttpClient` from "@angular/common/http" can be injected so that HTTP calls can be made with it.