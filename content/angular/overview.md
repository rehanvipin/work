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

## Change Detection
After Angular has created all the component views, change detection for any component can be called at some point (e.g., by zonejs as mentioned above).  
Why? It ensures that the view and model are up-to-date. All data in a component is synced with the rendered view and with child components.  

The nice part about this is that developers only (mostly) need to be worried about updating the model. This feature is also called "rendering" loosely.

How? At a high-level, it can look like this:
1. update bound properties passed to a child, then call the lifecycle hooks for that child. do this for every child directive.
2. update bound properties in the current component's view.
3. call content-level lifecycle hooks - AfterContentChecked on all children components. AfterContentInit on the first time
4. run change detection for children. it will perform a pre-validation to see whether child component is "dirty" or "always check",
and will early exit the function in the child if possible (not doing unncessary things like template updates).
5. call view-level lifecycle hooks - AfterViewChecked on all children components and self. AfterViewInit on the first time

Angular always maintains the list of bound properties which are used by children / in its own DOM.
This is in a separate store (called LView) from the object properties of the instantiated component JS object. It can help determine if values have changed.
The LView data is updated in the first and second step of the function for children and self respectively.

Angular has a principle of uni-directional dataflow from parent to child components, for performance benefits.  
To enforce that, one of the things it does is run a check after all the above operations are done in the Change Detection function. `checkNoChanges`.
It compares the LView data with the instantiated object properties to make sure that view & model are in-sync after Change Detection.  
If they are not, Angular throws an error : ExpressionChangedAfterItHasBeenChecked to warn users that the data displayed on the page might be wrong.

If there was no expectation of uni-directional dataflow, change detection would need to run an unknown number of times before the view and model are synced.

Angular is really a tree of Views, with each one having an LView data structure. Components are compiled into functions to create views, with each view having access to an object instantiated of the Component's class.
Each of view trees start at a rootViewRef. Bootstrapping an app creates one of these. Things like modals end up in another view tree. They are all managed by one `ApplicationRef`.
Change Detection is independent for them.

The starting element of a root view would be a `HostView` which attaches Angular views to regular DOM elements such as `document`, or `body` or wherever.  
`HostView` is not an element though. The element would be of the Angular Component e.g., AppComponent.