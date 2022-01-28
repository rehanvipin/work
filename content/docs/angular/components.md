---
title: "Components"
weight: 2
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# Creating them
A bare component is a class, defined in a .ts file. You can create new one like this:
* Create a `.ts` file and export a class that is decorated by `Component` from `@angular/core`. The decorator takes in an object as argument. The object has properties like `selector` (the HTML selector) and `template`.
* Import this class in `app.module.ts` and add it to declarations in the `NgModule` decorator of AppModule.

Data binding: Accessing the component's properties within its template via the `{{ any JS expression }}` syntax.