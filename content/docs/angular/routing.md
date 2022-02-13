---
title: "Routing"
weight: 5
---
# Routing & Co.
Some modern apps, like the one built with Angular are **Single Page Apps**. They only load one HTML file from the server (index.html mostly) and then use JS to fetch new content and change parts of the page as the user navigates the site. It behaves like a regular sites in all aspects (look at the address bar) while being more efficient!

## Creating routes
Routes are defined as an array of objects. The array is of the type `Routes` from "@angular/router". 
* The object should be like: `{path:'', component: ComponentKlass}` which is of type `Route`.
* You then pass the array as an argument to `RouterModule.forRoot(routes)` in an `NgModule` of your app (e.g. `AppModule` in `app.module.ts`)
* If you want a route to redirect, you can set it like so: `{path:'blue', redirectTo: '/red', pathMatch: 'full'}`

## Route parameters
You can pass parameters in a route path, like so: `path: 'warehouse/:id'` where "id" is the parameter. To access it within your component code you should:
* Add `ActivatedRoute` as a dependency to your component
* Access the parameter via the dependency object, like so: `route.snapshot.params["id"]`
* It is also recommended to setup a callback function with each param so that when navigating from that component to itself, the content is updated properly. This can be done like so:
```ts
route.params.forEach((param: Param) => {
    // logic for updating content
})
```

## Links to routes
You want Angular to generate the links to routes, not hardcode them. This is how you do it:
* Add the `routerLink` property to an element. As its value provide an array of path elements, like this: `['where', someParam]`, which will be joined with '/' and used.
* That element will be clickable and will go to that route when clicked. `<a>` elements will get an `href` property with the destination route as the value.

## Navigate with JS
You can also navigate the site, not just by clicking links, but also with JS if required. You just put `Router` from "@angular/router" as a dependency and then call `routerName.navigate([/* path as an array of strings */])`

## Guards
When you don't want a user to visit or leave ceratin routes.
### Activation
Used to prevent users from visiting a route unless some condition is satisfied. You can specify the condition via a function or service. You can do it with a service, like so:
* Define a service which has a `canActivate` method that takes in an `ActivatedRouteSnap` object as a param. This function returns true / false telling if the should be able to accessed. If you want you can do things like navigate to a 404 page from that function.
* Pass in the service in an array as the value to the `canActivate` key of the `Route` object you wish to guard. Like so: `{path: 'event/:id', component: EventDetailsComponent, canActivate: [NavigationService]}`
### Deactivation
Used to prevent users from leaving a page (they can always leave by changing the address in the address bar) unless some condition is satisfied. You can do it with a service, or with a function, like so:
* Define a function and add it as a provider. This means you need to create a `Provider` object and register it in the `NgModule`'s `providers` array. It needs the `provide` property which is a string that you can use to access this provider and `useValue` property whose value the function you just defined. 
* An example: `{provide: 'dontLoseData', useValue: checkBadDataState}`.
* The function must return true / false which determines if the user can leave the page.
* You can then use this provider by accessing it via its key, like so: `{path: 'event/new', component: abc, canDeactivate: ['dontLoseData']}`

## Active route
You can add classes to elements which have `[routerLink]` when the current page is that link. That's done with `routerLinkActive='someKlass'` property. To match exactly, you can set `[routerLinkActiveOptions]` to `{exact: true}`

## Lazy loading a Module
You can lazy load a module so that it only loads under a particular route. Therefore it saves bandwidth for the user when they just visit the main site. You define the module, its routes, and its components normally. Some changes:
* You import and use `CommonModule` instead of `BrowserModule` in `NgModule`.
* You use `RouterModule.forChild` instead of `RouterModule.forRoot`.

Then to load this particular module on a route you can define it in the main route, like so:
```ts
{ // this is a Route object
    path: 'extraa',
    loadChildren: () => import('extraStuff.module' /*the file where the module is defined*/).then(
        mod => mod.FancyModule // the NgModule'd class
    )
}
```

To improve perceived performance, this module can be prefetched whenever the browser is free.
This can be done by adding another argument to the root router module like so:
```ts
@NgModule({
    imports: [
        // other modules
        RouterModule.forRoot(mainRoutes, {preLoadingStrategy: PreLoadAllModules})
    ]
})
```

## Prefetching with resolve
When a user visits a route a resolver loads the data required for the page before the component is loaded. It helps fix issues caused when the component is ready but the data required for its content is not.

A resolver is usually a service that has a `resolve` method that takes in an `ActivatedRoute` object and returns an `Observable`. **The observable is automatically subscribed to.**

In the list of routes, for a `Route` object, specify a `resolve` property that is equal to an object where the keys can be used to access the data on the route later on. Like so: `resolve: { users: getUsersService }`. This can then be accessed via the `ActivatedRoute` like so: `this.route.snapshot.data['users']`
