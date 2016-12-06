# Storage

## Introduction

Database storage in nxus is provided through the module `nxus-storage` that wraps the [Waterline ORM](http://waterlinejs.org/) to provide a common Model-based interface for querying and storing data in a variety of database backends.

## Setting up storage

### Installation

`npm install nxus-storage --save`

By default this includes the `waterline-sqlite3` adapter for local file/memory-based storage. You probably also want to install the adapter for your database (see the [list](https://github.com/balderdashy/waterline) here), e.g. `sails-mongo` or `sails-postgresql`.

### Configuration

You'll need to configure both adapters (what backends you want to use) and connections (that models register with and that define how to connect using a given adapter). These configuration keys are under the `storage` key:

```
  "storage": {
    "adapters": {
      "default": "waterline-sqlite3"
    },
    "connections": {
      "default: {
        "adapter": "default"
      }
  }
```

#### Adapters

This configuration defines adapter names and the npm module name to use for connections using the adapter.

#### Connections

Each connection has a name that you will use when defining a model, and a set of adapter-specific connection information, often the user, host, or connection url string to use.

## Creating a model

```
import {BaseModel} from 'nxus-storage'

export default BaseModel.extend({
  identity: 'mymodel',
  attributes: {
    name: 'string'
  }
})
```

Models should usually be defined one-per-file, the default export. If your module inherits from `nxus-storage.HasModels`, any files in a `./modules` directory will be automatically registered as models.

Models themselves should extend `nxus-storage.BaseModel`, a wrapper around Waterline's `Collection` object that sets up default attributes and properties as well as hooks for nxus events.

In your module, you should register the model (if not using HasModels):

```
import {storage} from 'nxus-storage'
import MyModel from './mymodel'

storage.model(MyModel)
```

## Retrieving a model

```
import {storage} from 'nxus-storage'

storage.getModel('mymodel').then((model) => {
  model.find().then((objects) => { })
}

```

## HasModels Modules