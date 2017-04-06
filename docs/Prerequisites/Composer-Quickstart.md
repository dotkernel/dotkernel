# Composer Quickstart
---

Composer is a per-project dependency manager for PHP.

This means the `composer` command must be run in the project's folder.

Composer can be downloaded from getcomposer.org. 

### [Composer Download](https://getcomposer.org/download/)


# The Composer JSON file

To start using Composer in your project, all you need is a `composer.json` file. This file describes the dependencies of your project and may contain other metadata as well.
Source: [Composer - Basic Usage](https://getcomposer.org/doc/01-basic-usage.md)

## Sample Composer JSON

```json
{
    "name": "vendor-name/project-name",
    "description": "Project description",
    "version": "1.0.0",
    "license": "License type (eg. MIT)",
    "type": "project",
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

### Name

Project name
Examples: `my-brand/my-project`, `psr/http-message`, etc.

> Note: if project is dependency of another project it will be stored in the `vendor/vendor-name/project-name` folder, eg.:
`vendor/my-brand/my-project` 

### Description

Project description.

### Version

Project version.

Examples: `1.0.1`, `v2.3`, `1.5.3-RC2`, `v1.5.3-RC2`, `e35fa0d` (for specific git commit), 'feature-v.1.2' (version 1.2 for specific git branch)

[More about package versions](Composer-Versions.md)

### Authors

JSON array with authors. Each author is stored in a dictionary.

A `dictionary` is the representation of associative array in JSON.

### Type

Describes the package type. 

* library: This is the default. It will simply copy the files to `vendor`.
* project: This refers to a package such as a `skeleton app` or final product.
* metapackage: An empty package that contains requirements and will trigger their installation, but contains no files and will not write anything to the filesystem.
* composer-plugin: A package of type composer-plugin may provide an installer for other packages that have a custom type. Read more in the dedicated article.

### Require & Require-dev

`require`: List of project dependencies for production use.
`require-dev`: List of project dependencies for development purposes.

### Autoload & Autoload-dev

`autoload`: Autoloading for production use.
`autoload-dev`: Autoloading for development purposes (also includes autoloading for production use).


# Composer Commands

* `composer require` package-name:`version` - adds specified package to dependencies and installs it, the version is optional but recommended. eg.: `composer require zend-diactoros:1.2`
* `composer dump-autoload` - only generates the autoload files (`vendor/autoload.php` and files found in `vendor/composer`)
* `composer install` - installs all dependencies found in `require` and `require-dev` sections from `composer.lock` file, `--no dev` option is supported
* `composer update` - updates all dependencies to latest version
* `composer self-update` - updates composer
> Note: `composer.lock` is a file containing a list with all packages and versions. When running `composer update`, this file will be overwritten



# Creating a composer Package

## New composer package Scratch

When starting from scratch you can use an empty json file.

```json
{}
```

> Note: name, description, version, type and autoload are mandatory if publishing the project

From this point forward any dependencies can be required and the project to be build.

## New composer package based on another composer package

To create a new composer package based on an existing composer package simply run the following command
`composer create-project dotkernel/dot-frontend /path/to/create/project`

# File structure for a project
* `vendor` - dependencies folder
* `composer.json` - composer package file
* `src` - the source code for specified project
* `LICENSE.md` - License file (markdown format)
* `README.md` - Project's instructions (markdown format)

Other directory recommendations:
* `config` - for configuration files
* `docs` -  for doocumentation files
* `extra` - for project extras (such as database exports)
* `data` - for application content (such as cache, uploads, etc.)

# Composer package simulation

Package simulation is useful when testing a new package with an existing package before publishing it.
Package simulatoion is intended for local testing before publishing package anywhere, **do not use this in production**.

The new package (to be tested) will be named `the small package`.
The old package (to be tested on) will be named `the great package`.

Instructions:
* copy the small package files into the `vendor/me/my-project` folder of the great package (you must create it first)
* backup `composer.lock` or `vendor/composer/installed.json` files in the great package
* open the great package `composer.lock` file, add the `composer.json` contents in the small project in the `packages` section or add the contents in the `vendor/composer/installed.json` as the last array value
* run `composer dump-autoload`

> Note: Assuming the new package name is `me/my-project`. 


Sources: 
* [Composer Cheat Sheet for developers](http://composer.json.jolicode.com/)
* [The composer.json schema](https://getcomposer.org/doc/04-schema.md)