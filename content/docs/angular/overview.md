---
title: 'Overview'
---

# An overview (and miscellaneous stuff)
Angular is a platform, [apparently](https://angular.io/guide/what-is-angular), that includes a framework, some libraries, and developer tools to work with all of that.

## Typescript
Angular uses TS. It's a superset of JS that compiles to JS. You can adding typing via syntactic sugar which helps catch bugs when you compile. It also has editor plugins for the same. You can try it out [online](https://www.typescriptlang.org/play). Its main features are:
### Static typing
You can declare types while declaring variables, like so: `let var: number;`.
### Interfaces
They let you define a structure that objects must follow. An interface is a type. When you assign an object to a variable of that type, the object must have the properties in that interface (each of the same type). Like so
```ts
interface nice {
    name: string,
    age: number
}

let sic: nice;
sic = {
    name: "hai", // adding, removing, or changing the types of these properties causes an error
    age: 10
};
```
### Class properties
ES6 classes mainly define their properties via their constructors. TS lets you to define these properties in the class itself. You can optionally specify types for them. This is only for readability. E.g.
```ts
class Hip {
    num: number
    wutdis
    constructor() {
        this.num = 10;
        this.wutdis = "hmm";
    }
}
```
### Public / Private access
All class properties in ES6 are public. You can make them private in TS by adding the `private` before them during declaration. It also provides this nifty shorthand for the constructor:
```ts
class Hip {
    constructor(public wutdis: string, private num) {
        // it implicitly does this.wutdis = wutdis
    }
}
let f = new Hip("sic", 10);
console.log(f); // { wutdis: "sic", num: 10 }
```

## The CLI
You can create a new project with `ng new projectname`. The `src/app` directory is where you'd do most of your work. The `src/assests` directory holds static content like images and site-wide CSS.

## Hierarchy
When a user visits the site, they are sent to the root component. From where they are routed to the next component and so on till they reach their destination URL. These component trees are ...

## The bootstrap process
* The first file that is run is `main.ts` (as defined in `angular.json`). 
* It bootstraps a module, `app/module.ts` by default.
* It bootstraps the root component, `app/app.component.ts` by default.
* The root component is placed in `app/index.html` which is what is rendered.