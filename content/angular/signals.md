---
title: "Reactivity with Signals"
weight: 13
---
# Signals
Apparently, it is used to make Angular apps more responsive and improve Change Detection.

Signals can be thought of as a combination of state and a notification mechanism.

How is that different from RxJS observables?
Observables are a notification mechanism for *events* and don't really hold state. What about Subjects then?? 

It is still recommeneded to use RxJS for asynchronous event handling.

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

Another dependent is an `effect` which schedules an execution of the arrow function whenever dependencies change. E.g., `effect(() => { /**/ })`
Unlike a `computed`, it will at least run once. (because there is no concept of "reading" an effect. it is only for side-effects)
If there are multiple dependency updates, it is possible that only one execution is scheduled for all those updates.

The effect actually returns a "ref" which can be used to cancel any queued scheduled executions for that effect.

In both above cases, it's not recommended to update any dependency / dependents inside the function block as it can cause circular executions.

An `effect` can be setup only in an "injection context" such as constructor etc. or you can pass it an injector reference.  
More about that here <https://github.com/angular/angular/issues/56357>

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