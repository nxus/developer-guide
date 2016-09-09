# Creating a theme

Nxus Themes are collections of templates used to create the visual elements of a web interface.

Themes are mostly defined by a common convention described in this guide: Themes are just Nxus Modules that define templates using a common pattern.  There are no hard requirements for the structure of a theme, instead we recommend using the structure described below for the benefit of developers - everything should be defined in similar locations for all Nxus themes.

## Assumptions

All themes and partials in Nxus are built using the [Bootstrap 3.0](www.getbootstrap.com) HTML/CSS framework. To get the best results, and minimize the amount of new styling your theme as to do, build your theme CSS and HTML using Bootstrap as well.

You'll also want to make sure you've read the docs for [Modules](), [Templating]() and [Routing]() in Nxus.

## Anatomy of a Theme

Generally, a Nxus Theme is composed of four things: layouts, partials, assets and a Nxus Module to tie it all together.

* **layouts**: defines the structure of various pages.
* **partials**: the common elements of a page (like scripts, nav, head, etc).
* **assets**: the CSS, JS and Images that make up the visual style of the theme.
* **theme module**: takes care of registering the templates and asset routes.

### Default Theme

The `nxus-web` module defines a default theme which includes boilerplate versions of the layouts and partials described below.  Because these default templates exist, you only need to define the specific files you'd like to customize.  If you don't define any of the base templates, the defaults will be used automatically.

You can browse the source of the default theme, and use it as an example for your theme.

https://github.com/nxus/web/tree/master/src/modules/web-theme-default

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

The `default.ejs` layout generally looks something like this:

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

### page.ejs

The `page.ejs` extends the default layout, but adds a title and body section, as well as renders the `flash` template for flash messages.

```ejs
<div class="container">
  <div class="row">
    <div class="col-lg-12">
      <%- render('flash', {req}) %>
      <% if(typeof title != "undefined") { %>
      <h1 class="page-header"><%- title %></h1>
      <% } %>
      <%- content %>
    </div>
  </div>
</div>
```

### bare.ejs

The bare template doesn't include navs, footers, etc:

```ejs
<!DOCTYPE html>
<html>
  <%- include('../partials/head.ejs') %>
  <body>
    <%- content %>
    <%- include('../partials/scripts.ejs') %>
  </body>
</html>
```

## Partials

There are four common partials you will always want to define for your theme.

### head.ejs

This contains the `<head>` portion of your page, including any scripts, styles and metadata.  Generally, the `head` partial will also render the title.  The best practice is to use the page title and the siteName `app.config` variable:

```
<title><%- typeof title != 'undefined' ? title+" | " : "" %> <%- typeof siteName != 'undefined' ? siteName : 'Nxus' %></title>
```

### nav.ejs

This will contain the main navigation for your app.  Generally this is included at the top of the page.

### scripts.ejs

The `scripts` partial should include all your theme-defined scripts, as well as render the `included-scripts` partial. `included-scripts` injects other modules' client facing JS into the page.

```
   <script src="/assets/js/my-script.js"></script>
   <%- render('included-scripts') %>
```

### footer.ejs

The `footer` partial contains the common page footer that will appear on every page.

## Assets
Generally, assets fall into three folders:

* **images**: your theme image assets
* **js**: your theme javascript assets
* **css**: your theme stylesheets

However, you can add additional asset folders as required by your theme (a `/assets/fonts` folder for example).

## Theme Module

The theme module register the layouts, partials and assets with Nxus, making them accessible to other modules.  Generally your theme module will look like:

```javascript
import {NxusModule} from 'nxus-core'

import {templater} from 'nxus-templater'
import {router} from 'nxus-router'

class Theme extends NxusModule {
  constructor(app) {
    super(app)

    // Add the assets folder as a static route
    router.staticRoute('/assets', __dirname+'/assets')

    // Add the partials (Note: partials should be defined with no wrapping template)
    templater.replace().templateDir(__dirname+'/partials')
    
    // Add the root layouts: default and bare
    templater.replace().template(__dirname+'/layouts/default.ejs')
    templater.replace().template(__dirname+'/layouts/bare.ejs')
    
    // Add the page layout, which inherits the default layout (is wrapped in on render)
    templater.replace().template(__dirname+'/layouts/page.ejs', 'default')

    // Add in any template variables for all templates.
    templater.on('renderContext', () => {
      return {siteName: app.config.siteName}
    })
  }
}

var theme = Theme.getProxy()
export {Theme as default, theme}
```