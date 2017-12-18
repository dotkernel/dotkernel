# Creating a new Module

- [Creating a new Module](#creating-a-new-module)
    - [File structure](#file-structure)
        - [Main directories](#main-directories)
    - [Create a simple controller](#create-a-simple-controller)
        - [Controller Class](#controller-class)
    - [Routing](#routing)

This article will cover the Module creation steps in DotKernel 3.

Notes:

- The `dotkernel/dot-controller` Module is used, so make sure you read [its documentation](https://github.com/dotkernel/dot-controller) before continuing.
- This tutorial is for illustration purposes on, which means that no, or basic, functoinaly may be implemented.
- This tutorial focuses on the steps required in order to create a Module.

## File structure

It is a good practice to standardize the Module's format.

### Main directories

- `src` - may contain the source code files.
- `templates` - may contain the page templates and layouts.

## Create a simple controller

In your projects's `src` folder, create a new directory.
This directory represents your "Module".
Everything related to the Module should be in that folder.

### Controller Class

The controller can be created from scratch or extend DotKernel's `AbstractActionController`.
We recommend using `AbstractActionController` as the dispatching is already implemented.

The nameing standard is `VendorName\ModuleName`

The namespace should be:
`Frontend\MyModule`

Since we are a creating a controller it is strongly recommended to group controllers in the same namespace.
`Frontend\MyModule\Controller\MyModuleController`

The class should look like this

```php
<?php

namespace Frontend\Blog\Controller;

use Dot\Controller\AbstractActionController;
use Zend\Diactoros\Response\HtmlResponse;

class MyModuleController extends AbstractActionController
{
    public function indexAction() : ResponseInterface
    {
        return new HtmlResponse('ok');
    }
}
```

> You may have noticed the `Action` part on the function. Every method that's accessible through an endpoint will have to be suffixed with `Action`, as this is what the dispatching controller will request.

## Routing

Routes are the backbones of the application, and will be how the outer world communicated with the Module.
> Routes can be added and removed from the same global config file. `config/routes.php`, but may also be declared on a module-level through the ConfigProvider of your Module.

The skeleton of a route looks like this,
`$app->route(Url[/action], [Class], [Methods], 'routeName');`

An actual route could look like this:

`$app->route('/contact[/{action}]', [ContactController::class], ['GET', 'POST'], 'contact');`

Accessing `/contact/show`, would then invoke the `ContactControllers showAction method`.
