# File structure
---

It is a good practice to standardize the file structure of projects.
This way all projects are easier to work with and productivity level is increased.

This article describes the recommended file structure for DotKernel 3 projects.

When using DotKernel 3 the following structure is recommended:

## Main directories

* `src` - may contain the source code files
* `templates` - may contain the page templates and layouts
* `data` - may contain project-related data (**AVOID storing sensitive data on [VCS](https://en.wikipedia.org/wiki/Version_control)**)
* `docs` - may contain project-related documentation


These directories reside in one of the following directories:

* if the module is treated as a composer package where the directories above are stored in the package's root path
eg.: `/vendor/my-name/my-project-name/`

* if module is treated as an extension/component of the current project.
eg.: `/src/MyProjectName`

### The `src` directory

This directory contains all source code related to a module. By the class type or purpose it has the following directory convention:

* `Action` - Action classes (similar to `Controllers` but can only perform one action)
* `Authentication` - for authentication related classes
* `Controller` - for controller classes
* `Entity` - for entity definitions
* `Factory` - factory classes ([what is a factory?]())
* `Mapper` - data handlers (mappers and hydrators)
* `Service` - service classes ([what is a service?]())

These were just some examples, there are more kinds of classes such as `InputFilter`, `Validator`, `Decorator`, `Wrapper`, `Helper`, `Hydrator` etc.

Classes can be distributed by their entity too. Eg.: `User` - user related classes.
This is useful when working on very small projects which have only one class of each kind. Eg.: `UserMapper`, `UserValidator`, `UserHydrator`, etc.

### The `templates` directory

This directory contains the templates. 
> Note: DotKernel3 uses twig as `Template Engine`. All template files have the extension `.html.twig`
 * `app` - application-specific templates
 * `error` - error message templates
 * `layout` - page layout (page structure)
 * `page` - static page templates
 * `partial` - "page components" - blocks that can be included on any page (such as alerts, form display, navigation, side menus, etc)

### The `data` directory

This directory contains project-related data (such as cache, file uploads)

As a recommendation use the following directory structure:
* `data` - general project data or project initial data like database structure
* `data/cache` - for caching purposes
* `data/uploads` - for file uploads
* `data/proxies` - for class proxies

### The `docs` directory

* will be completed soon