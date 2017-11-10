# Implementing a contact form in DotKernel Frontend application

- [Implementing a contact form in DotKernel Frontend application](#implementing-a-contact-form-in-dotkernel-frontend-application)
    - [Frontend application structure](#frontend-application-structure)
        - [Source code files](#source-code-files)
        - [Next: Creating the contact controller](#next-creating-the-contact-controller)
        - [Tutorial index](#tutorial-index)
        - [All tutorials](#all-tutorials)

In this tutorial, we'll implement the contact page in the frontend application, as it is missing at the time this tutorial is made. We'll cover various topics, which hopefully will get you more accustomed to DotKernel, Zend Expressive and Zend Framework.

## Frontend application structure

The application follows the modular architecture suggested by Zend Expressive. Global configuration or application wide configuration should be put in a file in `config/autoload`. The extension is important: `.global.php` is for configuration that will be used for both development and production uses. It usually holds configuration that does not change in an application. You should not put credentials, server configuration, accounts etc. in global config files.

`.local.php` files are for configuration used by the current machine on which the application runs. These are not included into the repository, they should be created after deployment, on the machine that is used. You will usually put here the database credentials, account data, api keys etc. DotKernel frontend application provides distribution local configuration files with the extension `.local.php.dist`. These serve as templates for what needs to be configured.

Configuration files are merged on application runtime and cached(in production environment). The order in which they are merged is global files and after that local files. So local files can override global configuration files. This is customizable though.

### Source code files

The source code is in the `/src` folder. Inside, you'll see another set of folders, with specific names. These are modules that group together related code and functionality. These modules have almost the same structure as the frontend application, having at least a `src` folder, holding the module's code, but can also include assets and templates. Modules should hold code that is as decoupled as possible. Think of them as composer packages that are included in the application as specific modules. Actually, a well written module could be generalized and moved outside the project, in order to be reused.

The frontend application comes with 2 default modules: the `App` module and the `User` module. The App module should serve to hold application wide code and configuration, or the stuff that is not worth to modularize yet. It also holds the application templates, like the layout, error templates and so on.

In the user module, you'll find code and templates that extend the functionality provided by the [dotkernel/dot-user](https://github.com/dotkernel/dot-user) module. This was made to provide additional functionality for the user module, and at the same time, a proof of concept or demo of how you should work and extend that module. Among provided functionality, it is worth mentioning the mailing functionality for user-related events, the authentication customization and validation. For now, we'll not dig deeper into this. It is the subject of another tutorial.

Please note that these modules also provide module specific configuration in the `ModuleName/src/ConfigProvider` class, which is merged in the final configuration.

Next, we'll focus on creating a simple contact us page, and we will cover forms, input filters, content validation, events, e-mail composition, and how to combine all this into a web page.

### Next: Creating the contact controller

Go to the next tutorial: **[Creating the contact controller]((02-creating-the-contact-controller.md))**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
