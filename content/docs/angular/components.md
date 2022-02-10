---
title: "Components"
weight: 2
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# The real CONTENT

## Creating them
A bare component is a class, defined in a .ts file. You can create new one like this:
* Create a `.ts` file and export a class that is decorated by `Component` from `@angular/core`. The decorator takes in an object as argument. The object has properties like `selector` (the HTML selector) and `template` (or `templateUrl`).
* Import this class in `app.module.ts` and add it to declarations in the `NgModule` decorator of AppModule.

Data binding: Accessing the component's properties within its template via the `{{ any JS expression }}` syntax.

## Communicating with a child component
When you have a child component within your component you can pass data to it via HTML attributes. The process is:
* Create a property on the child component class that is prefixed by the `@Input()` decorator. E.g. `@Input() title: string`
* In the child component's tag in the parent component's template, pass the value like this: `<child-comp [title]="any JS expression"></child-comp>`
* You can react to changes in any of the input properties via `ngOnChanges(){}`

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

## Content Projection
AKA **"How to reuse components"**. Components have "content slots" within them. You can reuse a component by putting different content within these slots.

If the parent template looks like:
```html
<h2>Hip hop</h2>
<somechildcomp>
    <button>woop</button>
    <h3>sneak</h3>
</somechildcomp>
```  
The button and the h3 (basically all content within those tags) will be available to the child component via `<ng-content></ng-content>`

Multiple content slots can be used with multiple ng-contents. This is how you would do it:
In the parent template:
```html
<div someattr1>
    <!-- stuff for first content slot -->
</div>
<div id="whodisboi">
    <!-- stuff for second content slot -->
</div>
```

The HTML content within those slots can be used like so:
```html
<header>
    <ng-content select="[someattr1]"></ng-content>
</header>
<section>
    <ng-content select="#whodisboi"></ng-content>
</section>
```

The select attribute of ng-content takes in CSS selectors to match the HTML content from the parent.

## Accessing DOM with `viewChild`
To access the DOM of the HTML of a component:
* Give it a temp-ref-var like so: `<div #theBestElem></div>`
* Create a property on the component decoreated by `viewchild` like so: `viewChild('theBestElem') goodie: ElementRef`
* You can then access its DOM properties or get even more granular control with `nativeElement`.

# Directives
Components are represented in HTML as elements. Directives are represented as attributes on those elements. A directive can also be applied on a simple HTML element, not just components.

## Creating them
You can create a directive class in a file like `nice.directive.ts` like so:
```ts
import { Directive, OnInit } from '@angular/core'

Directive({
    selector: '' // CSS style selector e.g. [nice-stuff]
    // the HTML would be like <somecomp nice-stuff></somecomp>
})
export class NiceDirective {

}
```

They need to be added to the declaration section of the ngModule where they will be used. They can get a reference to the element they were used on via the `ElementRef` service which can be added as a dependency.

Raw access to the DOM element can be gotten via the ElementRef object like so: `ref.nativeElement`.

## Passing values to it
To pass in some value you just set the value of the attribute. E.g. for a directive with a selector "sneaky-wookie", do this: `<div sneaky-wookie="some nice string"></div>`. This can then be accessed via an `@Input()` property like so: `@Input('sneaky-wookie') aliasName: string` within the directive class.

An alias had to be used since the directive selector had a '-' in it.