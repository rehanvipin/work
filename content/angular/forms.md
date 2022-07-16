---
title: "Forms"
weight: 7
---
# Forms and validation
There are two kinds of forms in Angular:
* Template based (the content and the logic is in the HTML)
* Model based A.K.A. Reactive (the content and logic is in the component)

## Creating a form
* You need the `FormModule` from "@angular/forms" to be imported in the module where you will be using forms.
* When you import that module, it adds an NgForm directive to all form tags.
The directive instance can be accessed as described below.
* In the component with the form, on the form element, set a template reference variable like so: `<form #someNiceNameForForm="ngForm"></form>`. 
This provides additional functionality as [described in the docs](https://angular.io/guide/template-reference-variables#using-ngform-with-template-variables).
* For each input element whose value you want, set the `name` attribute. This will be the key to access its value later on.
* For the submit action, prefer to bind to ngSubmit instead of using the submit button, like so: `<form #formTempVar="ngForm" (ngSubmit)="handlerFunc(formTempVar.value)"></form>`
* The `value` prop. on the temp-ref-var is an object where the value for each input field with a name attr. is available.
* You can also bind `ngModel` (which is a directive) on each input element to bind it to values 
on the component, like so: `<input (ngModel)="compoProp" id="foo" type="text">`. 
To achieve **two way data binding** you can do this: `[(ngModel)]="compoProp`.

## Validation
Angular turns off the default behaviour of adding the "required" attribute on input elements. It does provide its own validation features:
* `formTempRefVar` has properties like `valid`, `invalid`, `touched`, `dirty` etc.
So does each input element, under `formTempRefVar.controls.inputElementName`.
* Those properties for individual elements can also be accessed via temp-ref-vars like so:
`<input type="text" name="woah" #niceHandle="ngModel">{{niceHandle.valid}}</input>`
* Angular also adds classes like `ng-valid`, `ng-touched`, and `ng-pristine` to elements.
It also sets `ng-submitted` on the `form` element when the form has been submitted.
These can be used for styling
* The form can be reset by calling `formTempRefVar.reset()`

{{< hint danger >}}
The element's property under `formTempRefVar.controls` may not be defined initially, therefore, if you want to check if the element is in an invalid state you should do it like this: `formTempRefVar.controls.inputElementName?.invalid`
{{< /hint >}}

## Reactive forms
Here, the structure of the form and its validation rules are defined in the component itself. It's more flexible and lets you unit test things like validation easily. Here's how you make such a form:
* You need the `ReactiveFormModule` like how template forms need `FormModule`
* The form is a property of the component. It is of the type `FormGroup`. You initialize it in `ngOnInit`, like so:
```ts
let username = new FormControl();
let password = new FormControl();
let day = new FormControl();
let month = new FormControl();
let year = new FormControl();
let birthday = new FormGroup({
    day: day,
    month: month,
    year: year
});
this.userLoginForm = new FormGroup({
    uname: username,
    paswd: password,
    dob: birthday
})
```
* The properties of the `FormGroup` are supposed to be the values you want from the form, or a group of values which are encased in a `FormGroup`s of their own.
* You must bind the HTML form and input elements like so: `<form [formGroup]="userLoginForm"></form>` and `<input formControlName="username"></input>`.
* In case of grouped values, the group must be bound `<div formGroupName="dob"></div>` and then the constituent elements like so: `<input formControlName="month"></input>`
* You can also add a `ngModelGroup` attribute so that the form value will be structured so that 
the input elements within the group will be nested inside an object. 
A structure like this `<div ngModelGroup="lol"><input name="hi"></div>` will give a value object like this:
```js
{   // other fields
    lol: {
        hi: "someval"
    }
}
```

## Validation (reactive form)
* The format for a new FormControl is: `new FormControl(value the field should have, validation for the field)` where both params are optional
* An example validation, where you can mark a field as required is: `FormControl(userName, Validators.required)` where `Validators` must be imported from "@angular/forms"
* The state of the form is accessible from the primary `FormGroup` like so: `this.userLoginForm.valid`
* To use multiple validators, pass them as an array: `[Validators.required, Validators.pattern('ab*')]`
* The results of each validator (pass / fail) can be obtained like `userLoginForm.controls.elementName.errors.whatEverValidation` where the validation is something like `required` or `pattern` or something else.

## Custom validator
A validator is a function. It returns an error object if the validation failed else it returns `null`. The error object looks like this: `{'errorName': errorValue}`. The error value can then be accessed like `elementName.errors.errorName`. The only argument that the function takes is a `FormControl` object (which will be the element on which the validator is set).

## Super custom validator
You can also use a directive as a validator. Since it is a class it needs to implement `Validator` from '@angular/forms' which means it needs to have a function `validate` that takes in a `FormGroup` (or `FormControl` depending on where the directive is used) and return `null` if there are no errors else an error object like before.

Since it is a directive it needs to be imported in the ngModule. To add it to Angular's list of validators (and therefore make it available for use) you can do this:
```ts
import { NG_VALIDATORS } from '@angular/forms'

@Directive({
    selector: 'wow-nice'
    providers: [{
        provides: NG_VALIDATORS,
        useExisting: NiceValidator,
        multi: true // this means add this validator as a provider to NG_VALIDATORS
    }]
})
export class NiceValidator {
    validate(fg: FormGroup) { /* validation logic */}
}
```

## Multi-field validation
You need to do a few weird things to validate multiple fields:
* To access sibling fields, use the root group like so `fg.root.controls.siblingElement`
* To actually validate elements with the directive validator you need a temp-ref-var like so: 
`<div ngModelGroup="niceGrp" validatorDirec #whyNeedIDK="ngModelGroup"></div>`
* The sibling needs to re-run the validator whenever it updates. Can do that with an event binding: `<div (change)="whyNeedIDK.control.controls.someField.upDateValueAndValidity()></div>`

This whole thing is just super complex and weird.

## `ngModel`
TODO: this is one fancy thing. [Check out the docs](https://angular.io/api/forms/NgModel) to understand it.
There's also this https://angular.io/api/core/ViewContainerRef.