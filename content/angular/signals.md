---
title: "Reactivity with Signals"
weight: 13
---
# Signals
Apparently, it is used to make Angular apps more responsive and improve Change Detection.

Signals can be thought of as a combination of state and a notification mechanism.

How is that different from RxJS observables?
Observables are a notification mechanism for *events* and don't really hold state. What about Subjects then?? 

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

Another dependent is an `effect` which schedules an execution of the arrow function whenever dependencies change.
Unlike a `computed`, it will at least run once. (because there is no concept of "reading" an effect. it is only for side-effects)
If there are multiple dependency updates, it is possible that only one execution is scheduled for all those updates.

The effect actually returns a "ref" which can be used to cancel any queued scheduled executions for that effect.

In both above cases, it's not recommended to update any dependency / dependents inside the function block as it can cause circular executions.