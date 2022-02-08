---
title: "Components"
weight: 2
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# Components

## Creating them
A bare component is a class, defined in a .ts file. You can create new one like this:
* Create a `.ts` file and export a class that is decorated by `Component` from `@angular/core`. The decorator takes in an object as argument. The object has properties like `selector` (the HTML selector) and `template` (or `templateUrl`).
* Import this class in `app.module.ts` and add it to declarations in the `NgModule` decorator of AppModule.

Data binding: Accessing the component's properties within its template via the `{{ any JS expression }}` syntax.

## Communicating with a child component
When you have a child component within your component you can pass data to it via HTML attributes. The process is:
* Create a property on the child component class that is prefixed by the `@Input()` decorator. E.g. `@Input() title: string`
* In the child component's tag in the parent component's template, pass the value like this: `<child-comp [title]="any JS expression"></child-comp>`

## Communicating with the parent component
The common patern here is passing along an event to the parent component. It works like this, overall:
* Create an event handler to respond to some event in the child HTML. Just a function property of the class. Like so, `handleHere() {}`
* Create an event **emitter** property that is decorated by `Output`. Like so, `@Output() handledByParent = new EventEmitter()`
* In the even handler you can call `handledByParent.emit(anyVal)` which will emit an event by the child component
* In the parent, you can create a handler for the event, like so: `<child-comp (handledByParent)="parentHandler($event)></child-comp>`. `$event` is a special keyword that contains the value passed to `emit`. You can only pass **one value**. `parentHandler` is a function property of the parent component.

## Styling em
You can add styles for a component (other than the global styles), in two ways:
* Create an array of strings, where each one can have CSS style rules and assign that to the `styles` property of the decorator object of the component
* Add the rules in a file and create an array of strings (with file names) and assing it to `styleUrls`.

## CSS Encapsulation
When you add CSS styles to your component, it only matches the elements directly in the component. It does not apply to a parent or child component. This is how that works:  
Angular automatically adds attributes to every element within a component. Within a component, they all have the same attribute which is unique to elements in that component. It also modifies the CSS of that component to apply only when that attribute is present.  
There is a way to **pass down styles to children** by using a "deep" selector (google when needed).  
**Global styles are not mangled like this**.