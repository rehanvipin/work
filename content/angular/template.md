---
title: "Template Syntax"
weight: 3
---
# The template syntax
The template builds the HTML content that is displayed for a component. It is HTML with some Angular template syntax which lets you make the component reusable.

{{< details "Binding in HTML" >}}
## JS in HTML?
There are multiple ways you can use JS objects in HTML. Two of them are:
### Interpolation
In places within the HTML where you can put custom values (within tags, properties of attributes, etc) you can use the result of a JS expression like this: `<div>{{ some JS expression }}</div>`. All the public properties of the component class are available. The result is always a string. This is because the `toString()` method is called on the result of the expression. The binding between the class properties and the text is one way. **Updates to the property will be reflected in the HTML but not the other way around.**

The variables referenced within the expression could come from multiple places, the order of search is:
1. The template variable name.
2. A name in the directive's context.
3. The component's member names.

Interpolation is secure since Angular will process the text to make sure any HTML / JS the user might have
provided is not executed, only displayed on the screen.

### Property binding
HTML elements have properties e.g. "class, style, action, id, nonce, etc". You can set the values of these properties, like so: `<div [nonce]=" some JS expression "></div>`. The result is **not** converted to a string.

Angular sanitizes values passed to a property to prevent XSS.

### Rules for expressions
You can't use a few things, like these:
* Assignment operators
* The `new` keyword to create objects
* `;` to use multiple expressions
* Global namespace objects like `console`, `window`, etc.

In general, here are a few guidelines you should follow:
* The expression shouldn't have side effects
* Try to keep them simple and fast

### Event binding
HTML elements also have events that they can emit e.g. "click, focus, hover, etc". You can bind these events (which means running code when it emitted) to statements, like so: `<div (hover)=" some JS statement "></div>`.

### Rules for statements
Statements can have assignments but only with `=`, not `+=` or other stuff. 
They can have multiple expressions separated by `;` but the other restrictions 
that held for expressions still hold. They mostly do have side effects.
{{< /details >}}

### Two way binding
This can be done like so : `<child-elem [(lol)]="someVal"></child-elem>` which does a bunch of things:
* The child element should have a property "lol" and an `EventEmitter` "lolChange"
* The parent should have a property "someVal"
* Whenever `someVal` is updated, `lol` will get the new value, just like normal property binding
* Whenever `lolChange` emits a value, `someVal` will be set to that value.

## `*ngFor`
ngFor is a structural directive. That means that it is capable of changing the DOM structure. It works like a for loop, where for each iteration it creates another instance of the element (and its children) on which it is called. It also provides a template reference variable for the element / its children to use. An example below:
```
<!-- x is the templ ref var and y is a property of the comp class -->
<figure *ngFor="let x of y"> 
    <img [src]="x.imzSrc">
    <figcaption>{{x.imzCap10}}</figcaption>
</figure>
```

## `undefined` values
When you try to access an undefined value within a template, Angular will not display that element and it will throw an error in the console :(. If you are expecting something to be undefined you can use the **JavaScript safe navigation operator** `?.` which returns `undefined` if its LHS is undefined else goes ahead. [Check out the syntax on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining).

## `*ngIf`
ngIf lets you conditionally render the element it is put on. It decides this based on the expression provided to it, like so: `<img *ngIf="some JS expression">`.  
{{< hint warning >}}
It is important to note that in case the condition is falsy, the entire element is commented out. It's not just hidden by some CSS / HTML property. In case some operations are done when the element is rendered and it is added / removed multiple times, it can **slow down performance**.

If you want to hide and un-hide the element multiple times you can do so with the HTML attribute "hidden" which you set like so: `<img [hidden]="some JS expression">`.
{{< /hint >}}

## `ngSwitch` & co
This is to ngIf like a switch case is to an if condition. You define the switch condition like so: `<div [ngSwitch]=""></div>`. On the children you can use either:
* `<img *ngSwitchCase="">` to compare particular JS objects (objects, not just strings) OR
* `<img *ngSwitchDefault>` which will come to life if none of the other cases matched

## Attribute binding
It's recommended to use property binding wherever possible but sometimes the required element property does
not exist. Some SVG attributes cannot be set via property binding because they have no "property targets".

The simplest way to do this is: `<elem [attr.whateverattributename]="expr"></elem>`. Note that the `attr.`
prefix is necessary. If the expression evaluates to `null` or `undefined` the attribute is removed from the element.

### Adding classes
There are multiple ways to do this. The simple way is for a single property like so: `<img [class.klassname]="bool JS expression">` which will add the class "klassname" to the element if the expression is truthy. 

When you want to add multiple classes you can do so with `ngClass` like so `<img [ngClass]="fancy stuff">` where "fancy stuff" can be any JS expression that returns one of these:
* An object, where the keys are class names and values are booleans which decide if that class will be added
* A string of space separated class names
* An array of strings, each of which are class names

### Adding styles
The simple way to set a style would be using attribute binding like so: `<elem [style.width]="CSS.px(10)"></elem>`. It's also possible to set multiple styles like so: `<a [style]="{width: '100px', height: '2px'}"></a>`.

`ngStyle` can also be used to set styles. `ngStyle` has a similar syntax to `ngClass` except that the return value of the expression should be an object where the **keys are CSS style keys** and **values are CSS style values**. A small example: HTML element with `<img [ngStyle]="komPute()">`. The function:
```ts
komPute() {
    if(/* condition */)
        return {'background-color': 'red', display: 'inline'};
    return {display: 'block'};
}
```

## Template reference variable
They help you use one part of a template in another. You can declare a variable on a tag, a component, or a bunch of other things as seen in the [docs](https://angular.io/guide/template-reference-variables). For a tag, it is a reference to the TS object of that tag and for a component it a reference to an object of that child component class. If you define one in an `ngIf` or an `ngFor` you can't access it outside those boundaries. This is how you use them:
* Declare the variable like this `<comp-or-tag #refVar></comp-or-tag>`
* You can then use this in other parts of the template, if it's a component you can access its **public properties**. Like so, `<other-tag [lul]="refVar.val1" (snap)="refVar.func1()"> {{ refVar.val2 }} </other-tag>`
* It is not accessible in the TS of the parent component.

{{< hint info >}}
How does this work with `ngModel`? According to the docs:  
If the variable specifies a name on the right-hand side, such as #var="ngModel", the variable refers to the directive or component on the element with a matching exportAs name.
{{< /hint >}}

## Styling
Write styles in the sheets provided for the components. Angular performs "view encapsulation" which means
a component's styles are applied to only its elements. This happens by adding special attributes to the 
elements in the DOM e.g. `<h2 somerandomNGattribute></h2>` and adding that attribute to all encapuslated
styles.

Styles provided in the global stylesheet are still applicable to all componenents.
To remove this encapsulation you can either:
* set `encapsulation` to `ViewEncapsulation.None` in the component's metadata
* add `::ng-deep` to child styles. This doesn't remove the encapsulation but allows adding styles to
elements that aren't in the template.

That's not the only type of encapsulation, there's also:
* `ViewEncapsulation.ShadowDom` which gives this component a shadow DOM and places elements under
that shadow root. All shadow elements styles are separated from global styles. Any style declared
for components within this shadow DOM are attached to the shadow root.
* `ViewEncapsulation.Emulated` is the default and sets styles for only that component's elements.

{{< hint warning >}}
The shadow DOM API is not available in all browsers (e.g. IE and Opera Mobile) but it's available in most
modern ones and is available to at least 94% of all users at the time of writing this. [Check caniuse](https://caniuse.com/shadowdomv1)
{{< /hint >}}

{{< hint info >}}
Thought only HTML files could be used as templates? SVGs can be used too!
{{< /hint >}}