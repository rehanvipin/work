---
weight: 1
bookFlatSection: true
title: "Front-End"
bookToc: false
bookCollapseSection: false
---

# Front-End stuff
Don't really have any plans for what this section should contain.
Here, have some fun facts while I figure out what should be on this page.

## Fun facts
* Variable hoisting was an unintended addition that came along with function hoisting when JS was
being developed. Hoisting is done by the two-scan interpreter. The first scan is to find declarations.
This [stackoverflow q&a](https://stackoverflow.com/questions/15005098/why-does-javascript-hoist-variables) has some good answers.
* There's no concept of "initialization" in JS.
There is declaration (`let a`) and there is assignment (`a = 2`).
"Definition" is just syntax sugar to do both declaration and assignment at once.
* `typeof null` evaluates to `'object'` in JS. [Which is a bug apparently](http://crockford.com/javascript/remedial.html).