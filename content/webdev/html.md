---
title: "HTML"
weight: 1
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---

# HTML
Notes made while studying this
[freeCodeCamp certification for responsive web design](https://www.freecodecamp.org/learn/responsive-web-design/) and more.

## Basic HTML and HTML5
* It's a convention to write all HTML tags in lowercase.
* Fancy new HTML5 tags : `main` inside which you can put other elements. These are supposed to be the "main" elements from an SEO standpoint.
* All `img` tags must have the alt attribute. The `img` tag is a type of self-closing tag.
* Internal linking with `a`: set the `href` to `#<id-of-element>`. Add `target="_blank"` as an attribute so the link opens in a new tab.
* `input` tags can have placeholder text and `form` tags have an attribute "action" which is the end point where the form data is submitted. You can make it **required** by adding the `required` attribute.
* A button of type `submit` enclosed in a form tag will submit the data from all `input` tags to the action URL.
* Radio buttons are a type of `input` element. You can group them into a radio group by giving them all the same `name` attribute. A radio button can have associated data, e.g. text, with the `label` tag. You can either nest the `input` element inside the `label` element or you can keep them separate but link them by setting the `for` attribute of `label` to the `id` of the `input` element.
* Same thing with checkboxes but a user can select multiple of those at the same time.
* With both those tags, the data that is sent to the server is the `value` attribute of the `input` element(s) selected. The value is usually the same as the data in the corresponding `label` element. A complete input radio button might look like:

  ```html
  <label for="indoor">
    <input id="indoor" value="indoor" type="radio" name="indoor-outdoor">Indoor
  </label>
  ```
* `div` is a general purpose container element.

## Applied Visual Design
* The `<strong></strong>` tag applies `font-weight: bold;` to the element it is enclosing. The `<u></u>` tag applies `text-decoration: underline;` to the element it is enclosing. The `<em></em>` tag applies `font-style: italic;`. The `<s></s>` tag applies `text-decoration: line-through;` i.e. strikethrough.

## Applied Accessibility
* The heading tags should be used to show the hierarchical relationship between elements. There should be only one `<h1>` element in a page. There should also be only one `<main></main>` element in a page (often coupled with a "jump to main" link to it).
* `<div>` - groups content `<section>` - groups related content `<article>` - groups independent, self-contained content.
* The `<header>` tag is for introductory content / content that appears on multiple pages. For the navbar / navigation links you can use the `<nav>` tag. A counterpart to the `<header>` tag is the `<footer>` tag.
* The `<audio>` tag gives semantic meaning to audio (and can provide controls) when wrapped around a `source` tag with audio.
* Similarly the `<figure>` tag gives semantic meaning to `<img>` elements in it when combined along with `<figcaption>`.
* You can wrap radio buttons along with their labels with the `<fieldset>` tag to show that they are related. `<legend>` can provide a description for that fieldset.
* Wrap anchor tags around a whole description of the link, not just "click here" as that is what is read by screen readers.
* The "accesskey" attribute can be set to an element telling what key the user must press to bring that element into focus.
* Setting the "tabindex" attribute to an element means it must be focused upon when tabbing through the page.

## Responsive Web Design Principles
* The "viewport" is the part of the user's screen where the website is rendered. You can use media queries to access its attributes and apply CSS styles accordingly.
* Apple's retina displays have twice the number on each dimension which means images will appear smaller. To "fix" this, they scaled up everything by 2x. This means that your images are scaled by a factor of 2 in each dimension. One possible fix you could apply against this scaling is to provide you image with the dimensions halved so the scaling cancels out.
* Instead of mucking around with pixels or any such thing you could set dimensions in terms of "viewport units" which will then scale as your users' viewport changes.

### The meta viewport tag
You just need `<meta name="viewport" content="width=device-width, initial-scale=1">` for a responsive site. This is also required by Bootstrap. This is what it means:
* `initial-scale=1` sets the default zoom to 1, which means that each **CSS pixel** (the one devs use when they put x: 30px;) will be equal to a **viewport pixel** (controlled by the browser)
* `width=device-width` means the website width be equal to the device screen width. This option is basically telling the browser not to zoom around your website or feed it 
