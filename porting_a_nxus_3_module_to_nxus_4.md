# Porting a Nxus 3 module to Nxus 4

## NxusModule

The biggest change in Nxus 4.0 for module development is the new base `NxusModule` class.  All modules should extend this class.

```javascript
import {NxusModule} from 'nxus-core'

class MyModule extends NxusModule {
  constructor() {
    super()
  }
}
```

### Exporting your Module and Singleton

In Nxsu 4, you need to export both the module class as well as a singleton of your module.  The semantics are to use a lowercase name for your module instance.  The `NxusModule` class includes a static helper method for creating and registering the singleton correctly.

```javascript
var myModule = MyModule.getProxy()
export {MyModule as default, myModule}
```

## Application and other Modules

The second big change in Nxus 4.0 is that all other modules and the application are directly imported into your source files.  You no longer need to use the `app.get()` syntax for getting module references.  Instead, you use a special syntax to `import` a singleton of the Module you want to interact with.

```javascript
import {router} from 'nxus-router'
```

Similarly, you access the application using the same syntax:

```javascript
import {application} from 'nxus-core'
```

or short-hand to continue using `app`

```javascript
import {application as app} from 'nxus-core'
```

## Configuration
Module configuration is now exposed through the `NxusModule.config` property.  Where in Nxus 3 you would get your module config from `app.config.myModule`, you can now refer simply to `this.config` in your module.

```javascript
import {application as app, NxusModule} from 'nxus-core'

class MyModule extends NxusModule {
  constructor() {
    super()
    console.log(app.config['my-module'] == this.config) //true
  }
}
```

### Default Configuration

If you want to set a default configuration object, override the `NxusModule._defaultConfig()` private method.

```javascript
import {application as app, NxusModule} from 'nxus-core'

class MyModule extends NxusModule {
  constructor() {
    super()
    console.log(this.config) // {some: 'option'}
  }
  
  _defaultConfig() {
    return {some: 'option'}
  }
}
```

### User Configuration

If you want to use and write a user visible configuration option to the `.nxusrc` file , override the `NxusModule._userConfig()` private method.

```javascript
import {application as app, NxusModule} from 'nxus-core'

class MyModule extends NxusModule {
  constructor() {
    super()
    console.log(this.config) // {some: 'option'}
  }
  
  _userConfig() {
    return {some: 'option'} // in .nxusrc, shows up as {'my-module': {'some': 'option'}}
  }
}
```

## Logging

Instead of an application level logger, each module has its own logger in a separate output namespace.  Use `this.log` instead of `app.log`. The same log-level methods are also avaialble (`debug`, `info`, etc).

To view the logs, or filter, you can set the `DEBUG` environment variable.  We use the npm `debug-logger` library, so [read about all the options](https://www.npmjs.com/package/debug-logger).

## Testing

Testing modules in Nxus 4 is much simpler than in previous versions.  In order to test cross module functionality, you can use the built-in TestApp and Sinon to create mocks and spys.

### Application
To test `application` calls, all you need to do is import the application as usual.

```javascript
import {application} from 'nxus-core'
```

When the `NODE_ENV` is set to `test`, the application is the familiar `TestApp` instance.

```javascript
application.on.calledWith('myEvent').should.be.true
```

### Modules
To test modules, you can import and replace methods with Sinon spys.  For example, to test a route was called:

```javascript
import {myModule} from '../'
import {router} from 'nxus-router'
import sinon from 'sinon'

describe("Setup", () => {
  var instance
  
  before(() => {
    router.route = sinon.spy()
    instance = new MyModule()
  })
  
  it("should call the router", () => {
    router.route.calledWith("/my-route").should.be.true
  })
})
```

