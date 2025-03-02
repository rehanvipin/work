---
title: "Reactivity with Signals"
weight: 13
---
# Signals
Apparently, it is used to make Angular apps more responsive and improve Change Detection.

Signals can be thought of as a combination of state and a notification mechanism for changes to that state.

How is that different from RxJS observables?
Observables are a notification mechanism for *events* and don't really hold state.  
What about Subjects then?? Well, they are similar, but state management code is simpler with signals (based on trials).  
One such benefit is that there is no need to manually "complete" & "unsubscribe".

Sometimes, rxjs pipelines may be better use, given that they will process every single emission. Unlike a `computed` with signals.

RxJS is still the choice when the domain has asynchronous activites, e.g., fetching data over a network.

This is a good course : [RxJS and Angular Signals Fundamentals](https://app.pluralsight.com/library/courses/rxjs-angular-signals-fundamentals/table-of-contents)

## Simple uses
They can be created in the component like so: `const newSig = signal<TypeOrLetItBeInferred>(defaultValue)`. The default value is a must.

They can be read anywhere in the TS code like so: `newSig()`. Same thing in the template file.
When a signal is used in a template, Angular registers it as a dependency of the template and, whenver required, will re-render the portion of the template where the signal is used.

To update values, just do `newSig.set(newVal)` and it will set the value and notify dependents.
Similary, there is a `newSig.update((oldVal) => { /* code to return new value based on old value */ })` to set the value and notify dependents.


## Effects
When a signal's value changes, there is a delay till the next change detection cycle for the updates to be visible in the template.

One dependent of a signal is a computed signal. It's a readonly signal whose value is derived from other signals.  
It's defined like this `const compSig = computed(() => { /* may use some signals to return the value */ })`.
All the signals used in the function block become the dependencies.  
It will only do the computation if the value of the computed signal is read anywhere, and it won't do the computation again if none of the dependencies have changed. Memoization basically.

Computed signals are lazily evaluated also.

Another dependent is an `effect` which schedules an execution of the arrow function whenever dependencies change. E.g., `effect(() => { /**/ })`
Unlike a `computed`, it will at least run once. (because there is no concept of "reading" an effect. it is only for side-effects)
If there are multiple dependency updates, it is possible that only one execution is scheduled for all those updates.

The effect actually returns a "ref" which can be used to cancel any queued scheduled executions for that effect.

In both above cases, it's not recommended to update any dependency / dependents inside the function block as it can cause circular executions.

An `effect` can be setup only in an "injection context" such as constructor etc. or you can pass it an injector reference.  
More about that here <https://github.com/angular/angular/issues/56357>

Angular itself mentions that `effect`s are not likely to be found in most applications and that `computed`s are generally better.

## Notifications
A signal will not notify the dependents if the "value" of the signal is the same as it was before, even if someone had .set or .update the signal.

How does it know value has changed? Object reference. That's why if your signal is holding an array, you should update the array reference itself when performing updates.

E.g., `sig.update(currentArr => [...currentArr, {val: 'newVal'}])` instead of getting array ref from signal and pushing to that array.

You should always use `.set` or `.update` to inform about a value update, but even with that, there are problems for arrays due to obj ref if you do
`sig.update(cArr => cArr.push({val: 'newVal'}))`. That's why the above method is better.

Alternatively, can we define a signal's equality checker to solve this problem?
```ts
uniqSig = signal([5, 'hi'], {
    equal: (prev, cur) => {
        if (prev.length !== cur.length) {
            return false;
        }
        return prev.every((pval, i) => pval === cur[i]);
    }
});
```

Not in this case. Since we are modifying the array that signal is holding, `prev` and `cur` will always be the same.

Computed / Effect signals will only track signals within the enclosing function which were read during the last execution.  
At every execution, they update the list of dependencies.

## Error handling
Signals are a store for values. They don't emit error events. They just store a value.
However, if there is an error thrown in a computed signal, the error will be propagated till the place where the computed signal's value is read.
Whether that is TS code or in the template.
If the error is in the template, it can also cause problems with displaying any other signals which are the dependencies of the computed signal.
dependents will anyways error out.

## Fine grained reactivity
All resources mention that signals improve change detection and provide "fine grained reactivity" compared to observables.
But what exactly is the difference?

```html
<form class=form-1>
    <label class=input-field-holder>
        <p>Box is {{ /* signal or observable | async */ }}</p>
        <input type=text / [(ngModel)]="someText">
    </label>
    <div> {{ someData }} </div>
</form>
<div class=data-block>
    <ul>
        <li *ngFor="let data of datalist; i = index"> Data for {{i}} is {{data}}.</li>
    </ul>
</div>
```

If the signal's value changes and the notification is sent to the change detector, it will only check the `<p>` tag.  
If an observable is used, it would trigger change-detection for the entire component. Requiring all bindings to be evaluated again.  

## Use with RxJS
RxJS used to be the only way to handle reactivity. Now signals can share that responsibility.  
RxJS is still recommended for asynchronous ops, data fetching and transformations in the service layer. Also for event management. 
Signals are the recommended ones for components. why? "improved change detection" apparently.
They also make state management easier to read. In services and components.

Also, rxjs makes sure observers get every single event which comes from an observable.  
Not the case with signals. Effects or computed might only get some values if many values are sent before the effect / computed logic gets a chance to be scheduled to run.
Once they get a chance to run, it is guaranteed that they will get the last value of the dependency signal.

There is an `rxjs-interop` library under the Angular monorepo which helps to convert between rxjs primitives and signals.

### Observable to Signal
Use the `toSignal(observable, options)` function. The optional parameter can help specify an initial value if there is one.
E.g., `{initialValue: 123}`. It creates a read-only signal and subscribes to the observable.

### Signal to Observable
If you want to use the signal's value in an existing rxjs pipeline, it can be useful to convert the signal to an observable with `toObservable(signalName)`.  
However, the resultant observable will not emit all values which the signal contains.
This function works by using an `effect()` so the observable may end up emitting some of the values similar to how `effect()` works.

It might be something like this:
```ts
makeObs<T>(signal: Signal<T>): Observable<T> {
    const tempSubject = new Subject<T>();
    effect((onclean) => {
        try {
            tempSubject.next(signal());
        } catch(e) {
            tempSubject.error(e);
        }
        onclean(() => tempSubject.complete());
    });
    return tempSubject.asObservable();
}
```

A possible problem to watch out is that once an observable emits an error, it will close and won't emit values.