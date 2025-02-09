---
title: "RxJS"
weight: 10
---
# Async & network stuff
Not everything happens right at once in a webapp. It shouldn't wait for certain events when it can execute other things. This is where asynchronous processing is useful. Promises were the preferred way to do it but now there are observables (from rxjs). Promises represent **one event that happens in the future** while observables represent **zero or more events that could happen synchronously or in the future**. This particular section is about RxJS and HTTP in Angular.

The docs [tells how observables could be used in place of promises](https://angular.io/guide/comparing-observables).

Good resources for RxJS:
* [A gist that explains reactive programming](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
* [A single page crash course giving an overview of RxJS](https://www.learnrxjs.io/learn-rxjs/concepts/rxjs-primer)
* [Overview of main concepts. Official docs](https://rxjs.dev/guide/overview)
* [Video showing off some RxJS stuff live](https://youtu.be/ewcoEYS85Co)
* [Video about RxJS stuff specific to Angular](https://www.youtube.com/watch?v=65Us8NwmYf4)

## HTTP
To communicate via HTTP in JS first import `HttpClientModule` from "@angular/common/http" into the ngModule of your choice. Wherever required, a dependency of type `HttpClient` from "@angular/common/http" can be injected so that HTTP calls can be made with it.

Once you have an object of `HttpClient`, say `http`, you can perform calls such as: `http.get<T>(url)` where `T` is the type of the response you expect. The result is an `Observable<T>` which can be subscribed to.

To add query parameters just do **string concatenation :)**. E.g. `http.get<T>(url+"?userid="+searchUserId)` 🌴

There's also the `delete` method and `post` method. The `post` method needs a few more args like so: `http.post<T>(url, body, options)`. Where `body` is an object containing the data you want to give and options is also an object which has properties like HTTP headers.

The observables that the HTTP methods return just pass one value and then complete.

## RxJS 💧
Helps in managing events in a functional programming and asynchronous manner. Its main concepts are:

### Observables
An observable is a stream of lazy push-based computations. They can deliver values (zero to infinite) synchronously or asynchronously. Create one like this:
```ts
import { Observable } from 'rxjs';
const obsv = new Observable(subscriber => {
    // a stream of nexts optionally followed by either an error or a complete
    // e.g. :
    subscriber.next(anyValue1);
    subscriber.next(anyValue2);
    subscriber.next(anyValue3);
    subscriber.next(anyValue4);
    // only one of the following two should be there
    subscriber.error(anyError);
    subscriber.complete();
});
```

The observable can then be subscribed to like so:
```ts
const subc = obsv.subscribe(value => {
    console.log(value);
})
```

The observable can also be stopped like so:
```ts
subc.unsubscribe();
// the behaviour of the function can be customized
// by setting the return value of the subscription function
const custom = new Observable(subscriber => {
    // do some computation
    return function unsubscribe() {
        // do clean up perhaps?
    }
});
```

Observables can be used in HTML templates. As a convention append a "$" while naming an observable
so that it is distinguishable in the template.

{{< hint "info" >}}
There can be multiple subscribers. Each one will get the same stream of data.
An observable may `complete` before some async operations within it have run. All subscribers will receive same series of `next`, `error` and `complete` events as each other.
When one of them unsubscribes, it stops the flow of data to only that one.
{{< /hint >}}

### Observer
A consumer of an observable. Its just an object like this:
```ts
const observer = {
    next: value => console.log('Got a value: ' + value),
    error: err => console.log('Finished with an error: ' + err),
    complete: () => console.log('Finished peacefully');
};
const subc = obsv.subscribe(observer);
```
Some or all properties can be ignored

### Subscriptions
They are the objects returned on subscribing to an observable. They essentially have only 3 methods:
* `unsubscribe()`: stop receiving data from the observable
* `add(otherSubscription)`: so that unsubscribing this subscription will unsubscribe the other one too
* `remove(otherSubscription)`: to undo the add method described above

### Operators
[Easy to use resource to figure out what kind of operator you need.](https://rxjs.dev/operator-decision-tree)

They transform the data produced by observables. They are functions. There are two kinds:
{{< columns >}}

#### Creation
They generate observables from some kind of data / instruction. These functions return observables. An example is `of` from 'rxjs'. It takes in one or more arguments and then creates an observable that pushes these objects one after the other.

<--->

#### Pipeable
They generate observables from other observables. **These functions return functions that take in an observable and return an observable.** An example is `map` from 'rxjs/operators'. You use it like `let funk = map(x => x+x)` and then `outputObsv = funk(inputObsv)`.

{{< /columns >}}

The official docs has [a useful list of commonly used observables, grouped by category](https://rxjs.dev/guide/operators#categories-of-operators).

Many times multiple pipeable operators are used in sequence. It becomes messy, thus we have the `pipe` function which can be used like this:
```ts
const obsv = of(1, 2, 3);
const obsv1 = map(x => x * x)(obsv);
const obsv2 = filter(y => y > 3)(obsv1);
obsv2.subscribe(v => console.log(v));

// is equivalent to

const subscrip = of(1, 2, 3).pipe(
    map(x => x * x),
    filter(y => y > 3)
).subscribe(
    v => console.log(v)
);
```

### Subjects
An observerable that can also be used as an observer. They can be used to make an observable multicast (They are unicast by default).
Using it as an observable:
```ts
import { Subject } from 'rxjs';
const obsv = new Subject<any>(); // the default type is void
obsv.subscribe(/* an observer */);

obsv.next(someVal1);
obsv.next(someVal2);
obsv.complete();
```

It can also be used as an observer. Just pass the Subject object to an observer's subscribe method. This works since the Subject already has `next`, `error`, and `complete` methods.

{{< hint "info" >}}
The important difference between a subject and a normal observable is that a subject does not maintain separate execution contexts.
A normal observer creates an independent execution context for each observer it has.
Whenever an observer subscribes to a subject it can receive values from there on only. Not the old values.
There are specialized subscribers that can buffer some old values to give new observers.
{{< /hint >}}

**A cool trick is to make an observable multicast with a subject.** Like so:
```ts
import { of, Subject } from 'rxjs'
const obsv = of([1, 2, 3, 4, 5]);
const sub = new Subject<number>();
const scp1 = sub.subscribe(e => console.log(e));
const scp2 = sub.subscribe(e => console.log(e));
obsv.subscribe(sub);
```

Some useful specialized subjects:
#### BehaviourSubject
Holds the current value that was pushed. When new observers subscribe, they immediately get that value. Afterwards they continue to get pushed values like other old observers.

#### ReplaySubject
A generalized form of `BehaviourSubject`. It can hold a buffer of n previously pushed values and pass them at once to new observers.
They are used like this: `const sub = new ReplaySubject<number>(bufferSize, expirationTimeForEachValue [optional])`

#### Void Subject
This is the default type of subject created when no type is specified. The subscriber for this doesn't receive any values for its `next` function. It's useful when you just want to know that some event occured.

### Scheduler
Controls when subscriptions start. Can modify this so that subscriptions start immediately, on the next event loop, next micro loop, or after some timeout. This is kinda complex so [here's a link to the docs](https://rxjs.dev/guide/scheduler).

## RxJS operators
Another section for this topic?? Yep, there's a lot of variety here and these are the popular ones:

Bunch of useful ones grouped by category: <https://www.learnrxjs.io/learn-rxjs/operators>

### Higher-order observable transformers
Convert higher order observables into regular ones i.e., `Observable<Observable<T>>` to `Observable<T>`.
Nested subscriptions are not recommended since it is harder to unsubscribe and easier to cause timing issues.
All the below transformers take an argument of a function returning an observable.
Thus, there are two observables here. An "outer" one and an "inner" one.
For all of them, the final returned observable will go to error state if either inner or outer go to error.

* `concatMap`: For each emitted value of outer observable, get all emmitted values of inner observable till completion, and then proceed to next emitted value of outer and repeat the same.
* `mergeMap`: Goes through emissions of outer observable and starts the inner observable for each one, but all the inner observable emit in parallel and the values that appear finally depends on their times of emissions.
* `switchMap`: For each emitted value of outer observable, cancel any previous subscribtions of inner observable and start new subscription.

Example:
```ts
function getInnerObs(x: number): Observable<string> {
    // does some operations
}

const outerObs = interval(1000).pipe(take(5));
outerObs.pipe(
    xMap(outerVal => getInnerObs(outerVal))
).subscribe(
    // observer logic
)
```

{{< hint "info" >}}
What about `flatMap` ? That's just a deprecated alias for `mergeMap`.
{{< /hint >}}

### Multicast
Assume an observable `o1`. When it is subscribed in two places, the subscriber logic of the observable runs twice.
If it's an observable got from `http`, that would mean it would make the API call twice.
This may not be expected. Especially in situations where the subscription is implicit e.g., async pipe and / or the subscriber logic is expensive.

Generally, this is avoided by creating a subject which takes value from the observable and there are multiple subscriptions to that subject.

However, that is not required. It can also be done with a `shareReplay(n)` operator. Where `n` is number of emissions to give to new subscriptions.

Example:
```ts
mainObs = new Observable<number>((subscriber) => {
    let total = 0;
    for(let i=0; i<100; i++) {
        total += i;
    }
    subscriber.next(total);
});
sideObs = this.mainObs.pipe(
    tap(val => console.log('before shareReplay', val)),
    shareReplay(1),
    tap(() => console.log('after shareReplay'))
);

user1 = this.sideObs;
user2 = this.sideObs.pipe(
    map(val => val*-2)
);
```

There is also the `share()` operator, but that will not give values to late subscriber. E.g., user2 will not get a value
```ts
mainObs = new Observable<number>((subscriber) => {
    let total = 0;
    for(let i=0; i<100; i++) {
        total += i;
    }
    subscriber.next(total);
});
sideObs = this.mainObs.pipe(
    tap(val => console.log('before shareReplay', val)),
    share(),
    tap(() => console.log('after shareReplay'))
);

user1 = this.sideObs;
user2!: Observable<number>;

constructor() {
    setTimeout(() => {
        this.user2 = this.sideObs.pipe(
        map(val => val*-2)
        );  
    }, 1000);
}
```

## Error handling
Whenever there is a an error thrown in the subscriber logic of the observable, or somewhere in the pipeline, rxjs catches automatically it to emit an error notification to the observers. E.g.,
```ts
export class SubComponent {
    notifs: {t:string, val:any}[] = [];

    obs = new Observable<number>(subscriber => {
        subscriber.next(1);
        throw new Error('custom err'); // if we comment this out, the error would be thrown later on in the pipeline
        subscriber.next(2);
        subscriber.next(3);
        subscriber.next(4);
    }).pipe(
        map(val => {
            if (val < 3) {
                return val;
            }
            throw new Error('val gte 3')
        })
    ).subscribe({
        next: (val) => this.notifs.push({t: 'next', val}),
        error: (val) => this.notifs.push({t: 'error', val}),
        complete: () => this.notifs.push({t: 'complete', val: 'dunzo'})
    })
}
```

An error state is a terminal state for an observable. There won't be any emissions after that.

It is possible to catch errors and return some value instead. This is through the `catchError` operator.
The argument to the operator is a function which returns a replacement observable.

catchError will only catch errors which happen above it in the pipeline or in the subscriber logic.
In the example above, a catchError could be added after the map like so:
```
catchError((err) => {
    console.log('got an error', err);
    return new Observable(sub => { /* any logic*/ });
})
```

There are some frequently used observables for this case:
* `EMPTY`. completes immediately without emitting any value
* `throwError(fnWhichReturnsAnError)`. emits an error notification immediately. The error object is as per the function passed to this operator.

What if the observer doesn't specify a function for the error notification? 
If it just has a `next` handler, then the error is thrown up the call stack, and in the case of a browser, will end up in a `console.error`. 