---
title: "Directives"
weight: 12
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# Directives: Hmmm ...
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
Some basic stuff has been covered here in the docs:
{{< button relref="/angular/template#ngif" >}}ngIf{{< /button >}}
{{< button relref="/angular/template#ngfor" >}}ngFor{{< /button >}}