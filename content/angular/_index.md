---
weight: 1
bookFlatSection: true
title: "Angular"
bookCollapseSection: true
---

# Angular, the fancy web framework
Angular lets you make dynamic web apps easily. This was written when Angular 14 came out.

## Base Knowledge
I know *nothing* about Angular. I know a bare minimum of HTML, CSS, and JS. Let's see how this goes.

## Course stuff
I'm following along [The Angular Fundamentals course on Pluralsight](https://www.pluralsight.com/courses/angular-fundamentals).
The authors have nice practice exercises [available on their website](https://jcoop.io/angular-practice-exercises/).
[Code for the course is available on GitHub](https://github.com/jmcooper/angular-fundamentals-files).

This [Crash Course by Traversy Media](https://www.youtube.com/watch?v=3dHNOWTI7H8) is also very good.

Finally, [The Angular docs](https://angular.io/docs) are very good.

What if you want to go even further and understand exactly what is happening?
E.g., stuff about zones and change detection. Check out the articles at [Angular in Depth](https://indepth.dev/angular)

Just to note, the content I put up here is whatever I understood out of the courses and many other sources off the internet. I haven't and I won't rip content off sources I haven't listed here. I write this stuff on my own, but when it comes to things like specs and such, they do need to be put in word for word.

## Fun stuff
Some cool things I learnt along the way:
* When you run `ng new coolstuff`, it generates a lot of files. [The file structure is explained here](https://angular.io/guide/file-structure).
* The JSON pipe is useful when you want to display an object in a template.
Use it like this: `{{ objkt | json }}`
* To see what happens when you run an `ng` command, suffix it with `--dry-run`
* There's the options to convert your components into web components.
This means they can be framework agnostic. [They're still working on making the output Angular free](https://angular.io/guide/elements) at the time of writing this post. Web components have [good browser support](https://caniuse.com/?search=web%20components).