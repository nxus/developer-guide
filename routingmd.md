# Routing

## Introduction

Nxus uses the [express router](http://expressjs.com/en/starter/basic-routing.html) to manage routing URLs and URL processing to handler functions in your modules. You will access it using the `nxus-router` module.

## Setting up the router

### Installation

`npm install nxus-router --save`

### Configuration


## Setting up a route

By default, `router.route` will map a GET request to a URL to a handler function:

```
import {router} from 'nxus-router'

router.route("/path/:param", (req, res) => { })
```

You'll typically define routes in your module's constructor, mapped to methods of your module:

```
import {NxusModule} from 'nxus-core'
import {router} from 'nxus-router'

class MyModule extends NxusModule {
  constructor() {
    router.route("/path/:param", ::this.pathHandler)
  }
  pathHandler(req, res) {
    res.send("OK")
  }
}
```

### Routes with HTTP methods

You can specify other HTTP methods as the first argument to `router.route`, e.g. "POST", "DELETE", "PUT", "GET", etc.

### Static files

For serving static asset files that are part of your application, use `router.staticRoute(url, filePath)` to serve an entire directory (or single file) at a given url prefix.

## Adding Middleware

## Sessions

### Alternate Sessions stores