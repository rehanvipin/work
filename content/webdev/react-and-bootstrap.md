---
title: "React & Boostrap"
weight: 7
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---

# React
A library to build user interfaces

It uses JSX, an extension of HTML that allows embeddable JS.
JSX is transpiled by babel and put into React's representation of the DOM.
Like so:
```jsx
ReactDOM.render(<div>wow</div>, document.querySelector('#hah'));
```

## Components
* One way to create them is by returning JSX from functions (the function name should start with a capital letter)
* Multiple components can be used to make a web page
* The other way is creating a class that extends `React.Component`. This gives access to state and lifecycle hooks.
* The `render` method of that class can be used to return the JSX
* To include one component in another, just add a self closing tag with that component's name in the JSX of this one.
* To render components, do: `ReactDOM.render(<CompName/>, document.querySelector('#bingo'));`
* To pass properties to them, do: `<LolComp prop1='hah' prop2={['abc', 'def']} prop3={new Date()} />`
which can then be accessed from the single parameter as `param.prop1`, `param.prop2`, and so on.
* The default properties object can be set like so: `CompName.defaultProps = {}`
* For a class component, the properties can be accessed like so:
```js
class Hah implements React.Component {
    constructor(funObj) {
        super(funObj);
        // one of the things it does is set this.props = funObj
    }
    render() {return <div>{this.props.propName}</div>;}
}
```
* To add state to a component, just declare a `state` property in its constructor
* To set state later, call `this.setState(/* the new state object */)`
* `setState` performs operation asynchronously, so you cannot know for sure
what the value of the state will be at any point. To make sure you get the correct value,
you can also pass functions to setState like so:
```js
this.setState((state, props) => {
    prop1: state.prop1 + props.updaterStuff,
    prop2: props.newValueStuff
});
```
* Note that the object that's passed to `setState` doesn't have to represent the whole state.
It should be a subset of only keys whose values have been updated.
* There are lifecycle hooks like `componentDidMount()`
* In React, a uni-directional flow of data from parents to child is preferred. 

## Redux
State management for web apps. There's a single state store object for all pages of the app.
Updates to the state are done with "actions" which are just objects with a "type" property and other data.
Redux state is read-only, if you want to change some property, create a new state object (which is what the reducer does).

# Bootstrap
A responsive CSS framework. You can design your site in such a way that it will look similar on screens of different widths.

### Breakpoints
Also known as media query extends. A breakpoint is the conditions for a media query to be executed. For Bootstrap these are device widths (e.g. >=576px, >=768px etc). By default, the `xs` (width >= 0) breakpoint media queries are applied.  
Bootstrap uses `min-width` breakpoints since its meant for developers to develop for smaller devices first and then change accordingly as screen width increases.

### Containers
The basic unit in Bootstrap. They contain content and are responsive. The container classes are : 
* `container` which is a fixed width container. It has a specific `max-width` for each breakpoint.
* `container-fluid` has `width: 100%` for all breakpoints
* `container-{breakpoint_name}` has `width: 100%` till that breakpoint (inclusive) and then acts like `container`

### Grid
The Bootstrap grid is differnent from the CSS grid. This one is built upon flexboxes. It can contain rows, each of which are 12 columns wide. A grid is often placed in a container. You can have grids inside grids. The column elements, divs with the class `col` have equal widths but you can also set it for each one by changing the class to `col-{number_of_columns_to_use}` or even `col-auto` to fit the content.  
To create columns that start out stacked, occupying 100% row width and then fitting into a row you can use `col-{breakpoint}` which will go from stacked to fitting in a row once the breakpoint is crossed.  
You can control stacking at different device sizes by adding multiple classes, like this:
```html
  <!-- Columns start at 50% wide on mobile and bump up to 33.3% wide on desktop -->
  <div class="row">
    <div class="col-6 col-md-4">.col-6 .col-md-4</div>
    <div class="col-6 col-md-4">.col-6 .col-md-4</div>
    <div class="col-6 col-md-4">.col-6 .col-md-4</div>
  </div>
```  
The hierarchy of Bootstrap’s grid goes from container to row to column to your content.

There are also controls for columns and gutters which can help you implement the layout for your site in good detail. I understand it but I will "get it" only once I use it for something. Can't wait to do that!
