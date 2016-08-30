# Creating a Nxus Module

Nxus organizes functionality into modules, allowing reuse and logical packaging of related code.

## How Nxus finds Modules

Your application can include modules in two ways:

 * Dependencies in package.json that start with `nxus-`
 * Custom modules in your application within a top-level `modules/` directory.

The application automatically instantiates singletons of any modules found in these locations.

## Accessing Modules in your code

All modules should provide an instance of the proxy object for other modules to use directly. This proxy allows other modules to call methods of the module, properly delaying resolution of returned promises until the appropriate boot stage. By convention, this instance is exported as the lower-cased name of the module.

e.g. `import {moduleName} from 'ModuleName'`, `import {otherModule} from 'nxus-other-module'`.

## Writing a Module

Extend NxusModule:
```
import {NxusModule} from 'nxus-core'

class MyModule extends NxusModule {}
```
Perform setup in constructor, after calling super(). You should register any routes or templates in the constructor too:
```
import {application, NxusModule} from 'nxus-core'
import {templater} from 'nxus-templater'
import {router} from 'nxus-router'

class MyModule extends NxusModule {
  constructor() {
    super()
    this._foo = null
    templater.templateDir(__dirname+"/templates")
    router.route("/my-module", ::this.my_route)
  }
}
```
Define any default configuration (which will be written to user's application config file), and use config to extract user-overridable behavior:
```
class MyModule extends NxusModule {
  _defaultConfig() {
    return {key: "Value"}
  }
  my_route(req, res) {
  }
```


## Best Practices for Modules

1. Document your overall module, any config and events, and all public methods.
2. Use `this.log()` (and `this.log.debug()`, `this.log.info()` for outputting useful messages about your modules startup and behavior.
2. Use underscores for private methods `_name` so they are not exposed to the proxy object.