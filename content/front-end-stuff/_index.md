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
* While writing the CSS for a site, it's a good idea to write it mobile first and then add
media queries for the larger screen sizes. It leads to less CSS. Even if the design was desktop based.
* JS has so many inbuilt things. [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) is one of them.
* Want to know if you've covered all best-practices and stuff for your web app?
There's a nice [checklist](https://frontendchecklist.io)
* A book about building [web apps](https://basecamp.com/gettingreal) by Basecamp. It's good apparently.
[PDF](https://basecamp.com/gettingreal/getting-real.pdf) also available.