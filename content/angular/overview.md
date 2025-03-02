---
title: 'Overview'
weight: 1
---

# An overview (and miscellaneous stuff)
Angular is a platform, [apparently](https://angular.io/guide/what-is-angular), that includes a framework, some libraries, and developer tools to work with all of that. For most purposes, it's called a front-end web development framework.

## The bootstrap process
* The first file that is run is `main.ts` (as defined in `angular.json`  **That's a very useful file**). 
* It bootstraps a module, `app/module.ts` by default.
* It bootstraps the root component, `app/app.component.ts` by default.
* The root component is placed in `app/index.html` which is what is rendered.

## Modules and libraries
* Only the root NgModule should have a bootstrap property in its decorator.
The bootstrap property sets the root component for the app.
* Libraries are plain JS modules which can have angular modules.
* A component / directive / pipe can be declared in only one `NgModule`
* To use stuff that other module provide, you import that **`NgModule`** in this `NgModule`
* When you list services in the providers array, they are available app wide.
* It's possible to have multiple entry point components in the `bootstrap` array.

## File structure of workspace

## Deployment 🎈
This is done with the `ng build` command which:
* Minifies code (removes whitespaces and renames variables)
* Bundling (merges many JS file into a single JS file) so fewer requests are made
* Tree shaking (the production app doesn't have code that is never used). It's a *little* different from dead code elimination.

Angular has an Ahead Of Time (AOT) compiler that converts the HTML of templates into JS code that manupilates the DOM. 
It can help catch template errors, reduce the output size, and make rendering on the browser faster.

How is the compiler helpful? It converts all components into JS functions which creates the component view / updates it.
These are called template factory functions.

The runtime app just needs to call these functions. It does not have to deal with parsing the raw templates etc.

By default, running `ng build` will generate a "development" build which can still be deployed but isn't as optimized as it could be.
To run it in "production" mode use `ng build --prod` which reduces the number of requests and file sizes.

Deployment just involves copying the directory within "dist" to the web server!

## Zones
Angular used to use a `zone.js` library which would patch Web APIs of the browser and call Angular's change detection
when there were any asynchronous actions or user interactions. This abstracted change detection but could lead to too many calls to change detection, or sometimes, too few if some fresh browser API is not yet patched by the library.

Nowadays, it is possible to remove zone.js entirely and get change-detection via other means. One of the old ways was via the ChangeDetectionRef and it's detectChanges function.