---
title: "Components"
weight: 2
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# The real CONTENT
Fun fact: The `@Component` decorator actually extends `@Directive` decorator because components are *technically* directives.

## Creating them
A bare component is a class, defined in a .ts file. You can create new one like this:
* Create a `.ts` file and export a class that is decorated by `Component` from `@angular/core`. The decorator takes in an object as argument.
The object has properties like `selector` (which is a CSS selector to find out which HTML elements this component should apply to) and `template` (or `templateUrl`).
* Import this class in `app.module.ts` and add it to declarations in the `NgModule` decorator of AppModule.

Data binding: Accessing the component's properties within its template via the `{{ any JS expression }}` syntax.

The constructor of a component should ideally be empty, put intialization code in `ngOnInit`.

## Communicating with a child component
When you have a child component within your component you can pass data to it via HTML attributes. The process is:
* Create a property on the child component class that is prefixed by the `@Input()` decorator. E.g. `@Input() title: string`
* In the child component's tag in the parent component's template, pass the value like this: `<child-comp [title]="any JS expression"></child-comp>`
* You can react to changes in any of the input properties via `ngOnChanges(){}`
* You can also set an input propert setter like so: `@Input() set propname(val) {}` where the function is called every time propname changes. Just like a JS setter.

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
* Within the styles, if you want to refer to the element which the component is tied to
(i.e., the one in the component's selector) use `:host`, e.g., `:host { font-style: monospace; }`

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

{{< hint "info" >}}
The `ng-container` tag itself will not be present in the DOM.
{{< /hint >}}

## Accessing DOM with `viewChild`
To access the DOM of the HTML of a component:
* Give it a temp-ref-var like so: `<div #theBestElem></div>`
* Create a property on the component decoreated by `viewchild` like so: `viewChild('theBestElem') goodie: ElementRef`
* The selector could also be a component / directive / service class
* You can then access its DOM properties or get even more granular control with `nativeElement`.

## Lifecycle
A component goes through a set of events during its lifecycle. It's possible to "hook" to those events
(i.e., perform some extra actions you want). Some of these hooks are:
* `ngOnInit`
* `ngOnChanges`
* `ngAfterContentInit`
* `ngAfterViewInit`

A full list of them, when they're called and what they do is present [in the docs](https://angular.io/guide/lifecycle-hooks#lifecycle-event-sequence)

And some good examples for each one are in this [article](https://angularindepth.com/posts/1494/complete-guide-angular-lifecycle-hooks)

## Dynamic loading!
It is possible dynamically add new components to the DOM from JS. The two options are:
* `<ng-container *ngComponentOutlet="expressionWhichReturnsAComponent"></ng-container>`
* `ViewContainerRef`, `createComponent` function.