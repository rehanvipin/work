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
* Confused about what units to use in CSS? [Check out this video by Kevin Powell](https://www.youtube.com/watch?v=N5wpD9Ov_To)
    * Use `rem` (i.e., root el) for fonts and most other things. It's relative to the `font-size` of `<html>` (which is 16px by default)
    * `em` is relative to the `font-size` of the element it is on or the closest parent which has defined a font size. When it's used for some other property, it only references this element's font-size. It can be useful for padding and margins.
    * Use percentages for `width` (prefer to use `max-width`) or `ch` (equal to width of a char) when operating on a text container
    * Think twice before setting `height`. Try to set `min-height` if it is really needed.
* Try to keep text confined to 60 chars a row.
* There's an **open source** design tool: [Penpot](https://penpot.app) which focuses on web standards
* The word "JavaScript" is trademarked by Oracle (https://tinyclouds.org/trademark) as of Sept. 2022
* An easy way to locally deploy the built application is to run: `python -m http.server --bind localhost 8080` which serves the current directory at `http://localhost:8080`
* There's a slight imperfection in the microsoft logo on black backgrounds
where the red box seems to appear larger than the blue box below it. This is due to the way pixels
are arranged in a display. [See it in more detail here](https://www.avforums.com/threads/anyone-else-have-an-offset-blue-and-red-square-on-the-microsoft-logo-of-office-2019.2310709/)