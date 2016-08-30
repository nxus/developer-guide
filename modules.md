# Modules

Nxus Modules are ES6 [classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes), usually extending the core `NxusModule` class.

Every module in Nxus is a singleton, meaning there is only ever one running instance of the module in the application at a time.  Modules in Nxus generally extend the `NxusModule` base class from the `nxus-core` package.

You don't have to do any explicit instantiate your module. When the Nxus application is started with `application.start()`, all modules are automatically imported and instantiated.

## Module Types
There are two types of modules in Nxus: core modules and application modules.

**Core Modules** are installed via npm and located in `./node_modules`. Core modules are always prefixed with `nxus-`.

**Application Modules** are usually the modules you will create.  These are specific to your application, and located in the `./modules` directory.

## File structure

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