---
title: "Directives"
weight: 5
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Directives: Hmmm ...
These are just classes that add extra behavior to elements ~ Angular docs.

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

## Builtins
### Attribute
Some examples are:
{{< button relref="/angular/template#adding-classes" >}}ngClass{{< /button >}}
{{< button relref="/angular/template#adding-styles" >}}ngStyle{{< /button >}}

### Structural
Some basic stuff has been covered here in the docs:
{{< button relref="/angular/template#ngif" >}}ngIf{{< /button >}}
{{< button relref="/angular/template#ngfor" >}}ngFor{{< /button >}}

`<a *ngIf="expr"></a>` is really a shortcut, it expands to: `<ng-template [ngIf]="expr"><a></a></ng-template>`.

It's possible to have an else block like so:
```
<a *ngIf="expr; else theOtherBlock">first</a>
<a #theOtherBlock>second</a>
```
If you want to be consistent you could write that as:
```
<div *ngIf="expr; then theFirstBlock else theOtherBlock>
<a #theFirstBlock>first</a>
<a #theOtherBlock>second</a>
```

{{< hint info >}}
Does using a `<div>` there seem weird? It's possible to use `<ng-container>` instead.
It doesn't even get output into the final HTML
{{< /hint >}}

Want to have an index while looping with ngFor? Do this:
```
<div *ngFor="let item of items; let i=index">{{i + 1}} - {{item.name}}</div>
```

When the list that ngFor is rendering changes, it's possible to optimize the rendering by only
rendering the elements that have changed (modified / removed / added). This is done with `trackBy`:
```
<div *ngFor="let item of items; trackBy: (i, d) => d.id">
  ({{item.id}}) {{item.name}}
</div>
```

`ngFor` has a bunch of local variables which can help reduce template code.
[They can be found in the docs](https://angular.io/api/common/NgForOf#local-variables) but here is an example:
```
<li *ngFor="let user of users; index as i; count as c">
  {{user.name}} is {{i}} out of {{c}} in the list
</li>
```

## Attribute
How to make a custom one!  
Start off with `ng generate directive somecoolstuff`. The base may not be enough, so set it up like this:
```ts
import { Directive, ElementRef, HostListener } from '@angular/core';

@Directive({
  selector: '[appSomecoolstuff]'
})
export class SomecoolstuffDirective {
    constructor(private el: ElementRef) {
        // can access DOM node with el.nativeElement
    }
    @HostListener('click') handler() {
        // do stuff to the element
    }    
}
```
It can be added to an element like this: `<a appSomecoolstuff></a>`.
Multiple values can be passed to it using `@Input()` e.g.:
```ts
@Input() randomProp1: string;
@Input() randomProp2: number;
```
which can be used like so: `<a appSomecoolstuff randomProp1="wowcool" [randomProp2]="1+2"></a>`.

## Structural
These modify the DOM by adding / removing elements. Only one structural directive can be applied per element.
It's possible to make your own like so: `ng g directive whatADirective`. The class can be modified like this:
```ts
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({ selector: '[appWhatADirective]'})
export class WhatADirectiveDirective {
  private someStuff = 'wow';

  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef
  ) { }

  @Input() set appWhatADirective(randomStuff: string) {
    // do some stuff to the view container
  }
}
```

which can then be used like so:
```
<p *wow="'somestring'">
maybe do something with this content here?
</p>
```

The translation between structural directive shortcuts and what Angular actually does [can be found here](https://angular.io/guide/structural-directives#shorthand-examples)