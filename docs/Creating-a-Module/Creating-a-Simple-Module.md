# Creating a new module

This article will cover the module creation steps in DotKernel 3.

Notes:
* The `dotkernel/dot-controller` package is used, make sure you read [its documentation](https://github.com/dotkernel/dot-controller) before continuing
* This tutorial is for illustration purposes, this means basic or no functionality may be implemented
* The focus on this tutorial is on the steps required to create a module


## File structure
It is a good practice to standardize the modules format.

#### Main directories
* `src` - may contain the source code files
* `templates` - may contain the page templates and layouts


## Create a simple controller

In your project's `src` folder, create a new directory.
This directory represents your "module".
Everything related to this will be added in that folder. 


### Controller Class

The controller can be created from scratch or extend DotKernel's `AbstractActionController`.
We recommend using `AbstractActionController` as the dispatching is already implemented.

The base name structure standard is `VendorName\ModuleOrPackageName`

The base namespace used will be:
`Frontend\MyModule`

Since we are a creating a controller it is strongly recommended to group controllers in a namespace.
`Frontend\MyModule\Controller\MyModuleController`

The class should look like this

```php
<?php

namespace Frontend\Blog\Controller;

use Dot\Controller\AbstractActionController;
use Zend\Diactoros\Response\HtmlResponse;

class MyModule extends AbstractActionController
{
    public function indexAction() : ResponseInterface
    {
        return new HtmlResponse('ok');
    }
}
```

## Route 


`$route->add([ClassName], [Methods], 'routeName');`