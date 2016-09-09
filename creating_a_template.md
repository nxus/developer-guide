# Creating a theme

Nxus Themes are collections of templates used to create the visual elements of a web interface.

Themes are mostly defined by a common convention described in this guide: Themes are just Nxus Modules that define templates using a common pattern.

## Assumptions

All themes and partials in Nxus are built using the [Bootstrap 3.0](www.getbootstrap.com) HTML/CSS framework. To get the best results, and minimize the amount of new styling your theme as to do, build your theme CSS and HTML using Bootstrap as well.

## Anatomy of a Theme

Generally, a Nxus Theme is composed of four things: layouts, partials, assets and a Nxus Module to tie it all together.

* **layouts**: defines the structure of various pages.
* **partials**: the common elements of a page (like scripts, nav, head, etc).
* **assets**: the CSS, JS and Images that make up the visual style of the theme.
* **theme module**: takes care of registering the templates and asset routes.

### Default Theme

The `nxus-web` module defines a default theme which includes boilerplate versions of the layouts and partials described below.  Because these default templates exist, you only need to define the specific files you'd like to customize.  If you don't define any of the base templates, the defaults will be used automatically.

### Templating Engine

As with any other Nxus template, you can use/install whatever templating engine you like.  This guide assumes your theme uses the Nxus default EJS templates.

### File Structure

It is a good practice to organize your theme files using the following structure. This ensures consistency for end users.

```
|- ./modules/myTheme
  |- index.js
  |- /layouts
    |- default.ejs
    |- bare.ejs
    |- page.ejs
  |- /partials
    |- head.ejs
    |- scripts.ejs
    |- footer.ejs
    |- nav.ejs
  |- /assets
    |- /css
    |- /js
    |- /images
```

## Layouts

Layouts define the structure of a type of page. All Nxus themes will generally want to provide three layouts for use by the other modules:

* **default.ejs**: this is the base layout and should include the head, nav, scripts, flash messages and footer partials.
* **page.ejs**: a generic page, which includes a title and body.
* **bare.ejs**: a bare page with no elements (footer, nav, etc). Used for embedding pages in iFrames.

### default.ejs

The `default.ejs` generally looks like this:

```ejs
<!DOCTYPE html>
<html>
  <%- include('../partials/head.ejs') %>
  <body>
    <%- include('../partials/nav.ejs') %>

    <%- content %>

    <%- include('../partials/footer.ejs') %>
    <%- include('../partials/scripts.ejs') %>
  </body>
</html>
```


## Partials

## Assets

## 
