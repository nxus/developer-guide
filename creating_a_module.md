# Creating a Module

Nxus organizes functionality into modules, allowing reuse and logical packaging of related code.

## How Nxus finds Modules

Your application can include modules in two ways:

 * Dependencies in package.json that start with `nxus-`
 * Custom modules in your application's file structure, in directories under a top-level `modules/` directory.

The application instantiates singletons of any modules found and creates a ModuleProxy

## Accessing Modules in your code

All modules should provide 


## Writing a Module


## Best Practices for Modules