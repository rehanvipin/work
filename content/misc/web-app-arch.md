---
weight: 5
title: "Web App Architecture"
# bookFlatSection: true
# bookToc: false
bookCollapseSection: false
---
# Overall design between browser & server
How do you serve content to a user in 2024?
Well, the ways I know of are ...
1. Django, or Flask (what architectres do these promote? MVC?)
2. Spring Boot & React (Single Page Apps. MVVM apparently??)
3. Next.js (and other SSR)
4. Hugo (Static Site Gen)
5. Rails (MVC)
6. Cloudflare web workers (the "serverless" design)

## Architectures

### MVC
Model View Controller.
Lots of strong opinions about this.
Apparently, there are no MVC frameworks, but just apps which implement the MVC architecture pattern.
That might be an over-simplification. There could be frameworks which make it easy to do MVC.

MVC is split into the model section and the presentation section (hence the Separation Of Concerns). Or for simplicity, like this:
* Model layer contains business logic. It also handles the data persistence.
* View layer presents the UI, given the data to present. They are not meant for doing processing.
* Controller passes data between Model & View, and also performs auth, handles request-response, and displaying correct View to the user. It does not handle the routing of requests i.e., matching.

The model layer is an important one. It can be thought of as containing:
* Domain entities, corresponding to business objects. These should contain the business logic for that entity.
* Data mappers, which handle conversion between domain entities & persistence storage.
* Services, responsible for application logic, including interactions between domain entities.

View layer can directly read from Model layer, just be careful so that Models are not be dependent on the UI.  
The Model & View layer don't have knowledge of the Controller.  

### MVP
Model View Presenter.
Pretty much same as MVC, but all interactions between Model & View happen through Presenter layer.

View & Presenter could be aware of each other.

### Model View ViewModel
The aim of this approach was to simplify the View layer. Instead of having coding logic,
it just has simple bindings to the properties & methods in the ViewModel layer.

The View definition could be written in a simple language e.g., XML.  
The ViewModel is itself not aware of the View, and all data transfer is done through the bindings.
The ViewModel layer interacts with the Model layer only.

## Rendering Patterns

### SPA
Single Page Apps. Built with help of libraries like React.
They ship JavaScript to the client which creates the HTML pages on the fly,
helping in reducing time for changes in the page.

Pros and Cons below:

#### Pros
* Quick page-updates on user interactions. REACTIVNESS. The whole reason this became popular.
* Can easily work with client-side data which may change (e.g., location).
* Context of current state of app is easily available.
* Server not always required. Something like Firebase could be enough if data persistence is the only concern.

#### Cons
* First load could be slow. May be alivieated with lazy loading.
But not as fast as getting HTML from server.
* Lots of JavaScript needs to be executed, requires some processing on the client.
* Systems not able to get the HTML that would be displayed to the users (e.g., robot crawler)

### SSR
Server Side Rendering? Isn't that just the classical multi-page-applications?
Which MVC apps would provide? By directly sending HTML to the client?

Yes, it includes that, but it's a mix of SPA too.
So that the reactivness of the app is still there, without full page loads.

How?
The server sends a full HTML page to the client.
But it also then sends a `main.js` to hydrate the client with reactiveness (meaning any action they perform now will not require a full page load).

If the same logic is applied for each component,
i.e., the server sends the entire HTML for any component, and then provides hydration later on,
that's called Streaming SSR.

The good part is, client side JS need not worry about what all data is required / conditions need to be applied on the HTML of a component.
The server can do all the data fetches, apply conditions, and pass the end-result HTML to the client.

With React Server Components, it can also send a "suspend" state of the component
(basically the loading indicators the component should show before the actual state is ready).
Or, if that's not preferred, it can block till the comp is ready.

### SSG
Pre-render all possible pages of the app in HTML.
Minimal JS for client interaction. There is no server here, just a CDN.

For large sites with lots of pages, it would not be practical to build everything.
Instead, there is **Incremental Static Regeneration (ISR)**, which will create a static site for a page
at runtime, and keep that page cached for a while. Kind of a lazy-loading SSG, but with a server.

### Islands
Split page into components. Only the ones which require interactivity get JS,
the rest are plain HTML blocks sent from server.
Used by Astro & Fresh.

## Notes
Apparently, MVC of Rails is not the "original" MVC,
but rather a version 2 designed to suit web apps.
The OG one was for n-tier web apps where these could be different systems.