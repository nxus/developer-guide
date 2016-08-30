# Creating a Nxus Module

Nxus organizes functionality into modules, allowing reuse and logical packaging of related code.

Nxus Modules are ES6 [classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes), usually extending the core `NxusModule` class.

Every module in Nxus is a singleton, meaning there is only ever one running instance of the module in the application at a time.  Modules in Nxus generally extend the `NxusModule` base class from the `nxus-core` package.

You don't have to do any explicit instantiate your module. When the Nxus application is started with `application.start()`, all modules are automatically imported and instantiated.

## Module Types
There are two types of modules in Nxus: core modules and application modules.

**Core Modules** are installed via npm and located in `./node_modules`. Core modules are always prefixed with `nxus-`.

**Application Modules** are usually the modules you will create.  These are specific to your application, and located in the `./modules` directory.

## Accessing Modules in your code

All modules export themselves and are accessible via `import`.

For `nxus-` core modules, these can be accessed via the package name

```javascript
import {otherModule} from 'nxus-other-module'
```

Application modules can be accessed via the relative file path to that module:

```javascript
import {moduleName} from './modules/ModuleName'
```

or 

```javascript
import {moduleName} from '../ModuleName'
```

As discussed in [Nxus Core Concepts](), everything in Nxus returns a [Promise](http://bluebirdjs.com/docs/api/new-promise.html).  When accessing or calling other modules, you can always assume that return values are wrapped in a promise and will resolve or throw a catchable error.

Likewise, because Modules are managed by the Application, you don't need to worry about loading order or race conditions when calling methods on other modules: all methods are guaranteed to be called at the correct time in the application boot cycle.

> Read more about the [Nxus dispatching system]() and [application boot cycle]() to see how it works under the hood.

## Anatomy of a Module
The base `NxusModule` class provides a few utilities that will help to interact with the core Nxus `application` instance and other modules.

### File structure

Be sure to follow the Nxus [naming conventions]() for creating your module folder.  For example, for a Module named `MyModule`, you will have a folder structure similar to:

```
./modules
  |- /my-module
    |- index.js
```

If you are using [templates]() or [models]() in your module, you will want to create folders named `templates` and `models` respectively.

```
./modules
  |- /my-module
    |- index.js
    |- /models
    |- /templates
```

### Configuration
The Nxus Application [configuration system]() is automatically made available to your module via the `config` property and `_defaultConfig()` private method.

Define any default configuration, and use `config` to extract user-overridable behavior:

```javascript
class MyModule extends NxusModule {
  constructor() {
    super()
    
    console.log(this.config.key) // 'value'
  }
  
  _defaultConfig() {
    return {key: "Value"}
  }
}
```

### Logging

All modules have a local instance of the [Application Logger]() available via `this.log`. This includes the standard log levels like `debug`, `info` and `error`.

Use `this.log()` (and `this.log.debug()`, `this.log.info()` for outputting useful messages about your modules startup and behavior).

### Functionality

Any public methods on your module will be available to other modules to call.

> Private methods are denoted with a leading `_`, public methods are missing the underscore. `_private()` `public()`)

For example, if my module defines a method `doSomething()`, other modules can call that method as expected.

```javascript
import {NxusModule} from 'nxus-core'

class MyModule extends NxusModule {
  constructor() {
    super()
  }
  
  doSomething() {
    return 'hello'
  }
}
```

Then in the calling module

```javascript
import {NxusModule} from 'nxus-core'
import {myModule} from './modules/my-module'

class AnotherModule extends NxusModule {
  constructor() {
    super()
    myModule.doSomething().then((ret) => {
      console.log(ret) // 'hello'
    })
  }
}
```

### Exports

Because all modules are singleton instances, you **always** need to export both your module class and a module proxy instance. NxusModule includes a helper static method for ensuring your singleton instance is created and exported correctly:

```javascript
let myModule = MyModule.getProxy()
export {MyModule as default, myModule}
```

## Writing a Module

### Step 1: Import and Extend NxusModule
```javascript
import {NxusModule} from 'nxus-core'

class MyModule extends NxusModule {
  constructor() {
    super()
  }
}
```

### Step 2: Setup your class

Perform module in constructor, after calling super(). 

As discussed above, you can interact with any modules to perform setup in your constructor.

```javascript
import {application, NxusModule} from 'nxus-core'
import {templater} from 'nxus-templater'

class MyModule extends NxusModule {
  constructor() {
    super()
    this._foo = null
    templater.templateDir(__dirname+"/templates")
  }
}
```

### Step 3: Create your methods

```javascript
import {application, NxusModule} from 'nxus-core'
import {templater} from 'nxus-templater'
import {router} from 'nxus-router'

class MyModule extends NxusModule {
  constructor() {
    super()
    this._foo = null
    router.route('/this/route', ::this._thisRoute)
  }
  
  _thisRoute(req, res) { //private method, not available to other modules
    ...
  }
  
  doSomething() { //public method, available to other modules
    return 'hello'
  }
}
```


### Step 4: Export your module

Export your module class and a module proxy instance:
```javascript
let myModule = MyModule.getProxy()
export {MyModule as default, myModule}
```

## Best Practices for Modules

1. Document your overall module, any config and events, and all public methods.
2. Use `this.log()` (and `this.log.debug()`, `this.log.info()` for outputting useful messages about your modules startup and behavior.
2. Prefix private methods with underscore (`_name`) so they are not exposed by the proxy object.
3. Put models in a sub-folder `models`, templates in a sub-folder `templates`, and tests in a sub-folder `tests`.