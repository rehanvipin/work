---
title: "Libraries"
weight: 12
---
# Ultra level reusability
They provide extra functionality to Angular. 
They don't run on their own. They need to be used in an application.
There are many first party libraries that are regularly used, like "@angular/forms".

Angular libraries (e.g. the @angular/x ones) are `NgModules`.

Some functionality of an app can be packaged into a library so it can be reused with other apps.
They can then be published in "npm" or wherever.

## Installing them
Install via npm like so: `npm i jquery`. 
If the library does not contain typings for typescript within in a `.d.ts` file
you can try to get them via `npm i @types/jquery`.
It can then be use like so: `import * as $ from jquery`

You can also define types on your own via a `.d.ts` file e.g. `cooltypes.d.ts` in `src/`
where you delcare interfaces and functions.

An alternate way to use them is to put them in the global scope as if they were put in a script tag.
That's done by adding the source to the `scripts` array in `angular.json`

## Creating one
It's a convention to prefix library names with `ngx-` to denote they are Angular libs.
