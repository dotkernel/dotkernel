# File structure

- [File structure](#file-structure)
    - [Main directories](#main-directories)
        - [The `src` directory](#the-src-directory)
        - [The `templates` directory](#the-templates-directory)
        - [The `data` directory](#the-data-directory)

It is a good practice to standardize the file structure of projects.
This way it's easier to keep a clean overview of multiple projects, and less time is wasted trying to find the correct class.

When using DotKernel 3 the following structure is recommended:

## Main directories

- `src` - should contain the source code files
- `templates` - should contain the page templates and layouts
- `data` - should contain project-related data (**AVOID storing sensitive data on [VCS](https://en.wikipedia.org/wiki/Version_control)**)
- `docs` - should contain project-related documentation

These directories reside in one of the following directories:

- if the Module is a composer package where the directories above are stored in the package's root path, eg.: `/vendor/my-name/my-project-name/`
- if the Module is  an extension/component for the project, eg.: `/src/MyProjectName`

### The `src` directory

This directory contains all source code related to the Module. It should contain following directories, if they're not empty:

- `Action` - Action classes (similar to `Controllers` but can only perform one action)
- `Authentication` - For authentication related classes
- `Controller` - For controllers
- `Entity` - For entities
- `Factory` - Factories ([what is a Factory?](Factory.md))
- `Mapper` - Mappers ([What is a Mapper?](Mapper.md))
- `Service` - Service classes ([what is a Service?](Service.md))

The above example is just some of the directories a project may include, but these should give you an idea of how the structure should look like.

> Note: Other classes in the `src` directory may include `InputFilter`, `Validator`, `Decorator`, `Wrapper`, `Helper`, `Hydrator`.

Classes can be distributed by their entity too. Eg.: `User` - user related classes.

This is useful when working on very small projects which have only one class of each kind. Eg.: `UserMapper`, `UserValidator`, `UserHydrator`, etc.

### The `templates` directory

This directory contains the templates.
> Note: DotKernel3 uses twig as `Templating Engine`. All template files have the extension `.html.twig`

- `app` - application-specific templates
- `error` - error message templates
- `layout` - page layout (page structure)
- `page` - static page templates
- `partial` - "page components" - blocks that can be included on any page (such as alerts, form display, navigation, side menus, etc)

 > Note: Unlike some templating engines, Twig will not parse PHP in the template, should you wish to use PHP, you should instead create a [Twig extension](../Tutorials/Create-a-Twig-Extension.md), or do the PHP relating actions in beforehand, and include the variable when displaying the template.

### The `data` directory

This directory contains project-related data (such as cache, file uploads)

We recommend using the following directory structure:

- `data` - general project data or project initial data like database structure
- `data/cache` - for caching purposes
- `data/uploads` - for file uploads
- `data/proxies` - for class proxies
