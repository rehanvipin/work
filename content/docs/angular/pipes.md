---
title: "Pipes"
weight: 7
---
# Transforming data

## Built-in pipes
You can transform data before it is rendered in a template. There are many [inbuilt pipes](https://angular.io/api/common#pipes). The format is `{{ yourContent | pipeName:pipeArg1 }}` where the arguments are JS objects / literals and are optional. Example: `<p>A: {{a | currency:'CAD':'code'}}</p>`.

## Custom pipe
An example pipe:
```ts
// in the file goodie.pipe.ts
import {Pipe, PipeTransform} from '@angular/core'

@Pipe({
    name: 'goodiepipe' // the name used to call the pipe in the template
})
export class GoodiePipe implements PipeTransform {
    // this is the function that is called with the args
    transform(input: string, arg1: number) {
        if(arg1 < 5)
            return input+" is low";
        return input+" is high";
    }
}
```
You must then add it to the `declarations` of the NgModule where you want to use it. It can then be used like so: `{{ someVar | goodpipe:32 }}`

## Filter and Sort
AngularJS had the ability to do this with pipes. This meant that every time the data changed (i.e. a change detection cycle), the pipe would have to be called. This was **slow**.

{{< hint "warning" >}}
Angular doesn't recommend that you sort or filter with pipes (you usually can't but there's the option to do it with **impure pipes**). It is recommended to sort / filter in the component itself.

Angular only runs a pipe when the identity of the data has changed. It's value. **It does not check if the reference has changed.** Therefore, if you change the properties of an object, Angular will not run the pipe for it.
{{< /hint >}}

## Async
You can subscribe and process values from RxJS observables within the template itself. This is possible via the async pipe. It's often used with ngFor like this: `<div *ngFor="let book of books | async">We have {{ book.name }}</div>` where `books` is an observable which has multiple `next` calls and a `complete` call.