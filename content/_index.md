---
title: Introduction
type: docs
bookTOC: false
---

# A field manual for work
Helps you masquerade as a software developer. Use at your own risk. 🍊

## Fun facts
Idk what else to put on this page so here are some random nice things:
* You can hit `.` when you're on a GitHub repo to bring it an online VSCode instance (on [github.dev](https://github.dev)) to edit the files. You can't run anything tho cuz that's a paid feature.
* [Stackblitz](https://stackblitz.com) is building a really cool developer experience where you can do local web development without installing node and stuff on your local system. You can even share these instances!
* [Prettier](https://prettier.io/) is an auto code-formatter for web-dev
* [ESLint](https://eslint.org/) is a linter that you can run (via the terminal or editor) on your code to catch possible bugs
* [npx](https://nodejs.dev/learn/the-npx-nodejs-package-runner/) is really cool! You can run the angular cli without installing it like so: `npx @angular/cli new facebook --skip-git`.
* An easy way to locally deploy the built application is to run: `python -m http.server --bind localhost 8080` which serves the current directory at `http://localhost:8080`
* You can add [graphs and diagrams to markdown files in GitHub](https://github.blog/2022-02-14-include-diagrams-markdown-files-mermaid/) with [Mermaid!](https://mermaid-js.github.io/mermaid/#/)
* npm is not an acronym (it once was, not anymore) and must be written down in all lowercase :P
* Want a notepad in your browser? Paste this in the address bar of a new tab:  
`data:text/html, <body style="margin: 0;"><textarea style="font-size: 3rem; width: 100%; height: 100%; margin: 0; padding: 1rem; color: white; background-color: rgb(32, 32, 32); border: none; outline: none;" autofocus>`
* Confused about what units to use in CSS? [Check out this video by Kevin Powell](https://www.youtube.com/watch?v=N5wpD9Ov_To)
    * Use `rem` for fonts and most other things. It's relative to the `font-size` of `<html>` (which is 16px by default)
    * `em` is relative to the `font-size`of the element it is on or the closest parent which has defined a font size. Can be useful when you want margins and paddings to change with font size.
    * Use percentages for `width` (prefer to use `max-width`) or `ch` (equal to width of a char) when operating on a text container
    * Think twice before setting `height`. Try to set `min-height` if it is really needed.
* Try to keep text confined to 60 chars a row.
* [There's a standard for dates](https://www.iso.org/iso-8601-date-and-time-format.html): YYYY-MM-DD
* [Python has a good set of builtin modules](https://docs.python.org/3/py-modindex.html)
* Whenever you contribute to a repo on GitHub your contributions fall under the same license as the repo.
[As described in GitHub's TOS](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service#6-contributions-under-repository-license) 
* There's an **open source** design tool: [Penpot](https://penpot.app) which focuses on web standards
* Google drive has a good range of keyboard shortcuts. You can view them by presing
`Shift`+`\` or [from this nice support link](https://support.google.com/drive/answer/2563044).

{{< hint "info" >}}
## Note
This manual thing is pretty unrefined. It's like v0.1.2 rn.  
P.S. ([Check out semver](https://semver.org/))
{{< /hint >}}