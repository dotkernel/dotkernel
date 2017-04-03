Composer Quickstart (WIP)
---

Composer is a per-project dependency manager for PHP.

This means the `composer` command must be run in the project's folder.

Composer can be downloaded from getcomposer.org. 

##### [Composer Download](https://getcomposer.org/download/)


### The Composer JSON file
To start using Composer in your project, all you need is a `composer.json` file. This file describes the dependencies of your project and may contain other metadata as well.
Source: [Composer - Basic Usage](https://getcomposer.org/doc/01-basic-usage.md)

#### Sample Composer JSON

```json
{
    "name": "vendor-name/project-name",
    "description": "Project description",
    "version": "1.0.0",
    "license": "License type (eg. MIT)",
    "authors": [
        {
            "name": "John Doe",
            "email": "jdoe@example.com",
            "homepage": "http://www.example.dev",
            "role": "Developer"
        }
    ],
    "require": {
        "psr/http-message": "^1.0.1",
    },
    "require-dev": {
        "debug/dev-only": "1.0.*",
    },
    "autoload": {
        "psr-4": {
            "Project\\Namespace\\Prefix\\": "src/"
        }
    }
}
```

##### Name

Project name
Examples: `my-brand/my-project`, `psr/http-message`, etc.

> Note: if project is dependency of another project it will be stored in the `vendor/vendor-name/project-name` folder, eg.:
`vendor/my-brand/my-project` 

##### Description

Project description

##### Version

Project version

Examples: `1.0.1`, `v2.3`, `1.5.3-RC2`, `v1.5.3-RC2`, `e35fa0d` (for specific git commit) 

##### Authors
JSON array with authors. Each author is stored in a dictionary.

A `dictionary` is the representation of associative array in JSON.

##### Require & Require-dev
`require`: List of project dependencies for production use.
`require-dev`: List of project dependencies for development purposes.

##### Autoload & Autoload-dev
`autoload`: Autoloading for production use.
`autoload-dev`: Autoloading for development purposes.

> note: autoloading
Other explanations can be found [here](http://composer.json.jolicode.com/).

### Creating a composer Project
~~If starting from scratch the `composer json file[]`~~

#### Composer Commands
composer require `package-name`
composer dump-autoload
composer install
composer update
composer self-update

#### Versions
* caret, tilda, >=, <=, combinations, equivalences, etc. 

### Composer JSON
basic composer json

#### Writing the `composer.json` file
-- composer schema

### Creating a composer project
-- from scratch, no vendors

### Composer package simulation

- Useful when testing a new package with another package before publishing it
