# DotKernel3 Simple

## Creating a basic controller
Package: `dotkernel/dot-controller` is required before starting.

Run: `composer require dotkernel/dot-controller:1.0`

Creating a Handler class for each `Action` is redundant because of the duplicate classes and factories. A `Controller` Handler class.
The controller handler requires only one factory regardless the actions that will be called.
This article shows how to create a basic controller with external dependencies.

### The main structure
* create directory `src/Simple`
  * the folder should contain the file structure related to a composer package
* create folder `src/Simple/src`
* define a namespace for your newly created module (eg. `DotKernel\Simple`)
* map the namespace in the main `composer.json`

```json
...
  "autoload" : {
    "psr-4": {
      "Example\\OtherAutoloads\\": "src/OtherAutoloads",
      ...
      "DotKernel\\Simple\\": "src/Simple/src"
    }
  }
...
```

* create a `ConfigProvider` class (eg. `DotKernel\Simple\ConfigProvider` in `src/Simple/src/ConfigProvider.php`
* Create an `__invoke()` method, make sure it returns an array (`:array`)
* This will contain all default configuration for the module
* dump the autoload using `composer dump-autoload` or `composer dump`

src/Simple/src/ConfigProvider.php
```
<?php

declare(strict_types=1);

namespace DotKernel\Simple;

class ConfigProvider
{
    /**
     * Returns the configuration array
     *
     * To add a bit of a structure, each section is defined in a separate
     * method which returns an array with its configuration.
     *
     */
    public function __invoke() : array
    {
        return [
            
        ];
    }
}
```
* Register your newly created ConfigProvider in the `config/config.php` file

### Creating the Handler
* Create the basic handler class in `src/Simple/src/Controller/SimpleController.php`

```
<?php

declare(strict_types=1);

namespace DotKernel\Simple\Controller;

use Dot\Controller\AbstractActionController;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\RequestHandlerInterface;
use Zend\Diactoros\Response\HtmlResponse;
use Zend\Expressive\Template\TemplateRendererInterface;

class SimpleController extends AbstractActionController
{
    public function indexAction()
    {
        return new HtmlResponse('Hello world!');
    }

    public function aboutUsAction()
    {
        return new HtmlResponse('About us page!');
    }
}
```

* Assign your handler to route `/sc`

config/routes.php
```php
return function (Application $app, MiddlewareFactory $factory, ContainerInterface $container) : void {
    $app->route('/sc[/{action}]', DotKernel\Simple\Controller\SimpleController::class, ['GET'], 'simple.controller');
};
```

* The `[{/action}]` fragment tells the application an optional parameter with name `action` can be received
* Test the handler
* `sc`, `sc` or `sc/hello` will output `Hello world!`
* `sc/about-us`, `sc/aboutus` or `sc/aboutUs` will output `About Us Page!`

> Note: for more details about action handling check `AbstractController::getMethodFromAction` from package `dotkernel/dot-controller`


### Templating
* Create the template directory `src/Simple/templates/simple`
* Create file `src/Simple/templates/simple/hello-world.twig`

src/Simple/templates/simple/hello-world.twig
```twig
{# extend main layout#}
{% extends '@layout/default.html.twig' %}

{# page title #}
{% block title %}Hello World{% endblock %}

{# the page content #}
{% block content %}
    <h1>{{ myMessage }}</h1>
{% endblock %}
```

src/Simple/templates/simple/about-us.twig
```twig
{# extend main layout#}
{% extends '@layout/default.html.twig' %}

{# page title #}
{% block title %}About us{% endblock %}

{# the page content #}
{% block content %}
    <h1>About us</h1>
    <p>{{ aboutUsParagraph }}</p>
{% endblock %}
```

* Register the template namespace in `ConfigProvider`
  * the template namespace will be used as the following example `simple::page-name`, eg.: `$template->render('simple::about-us', $data);`

src/Simple/src/ConfigProvider.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple;

class ConfigProvider
{
    /**
     * Returns the configuration array
     *
     * To add a bit of a structure, each section is defined in a separate
     * method which returns an array with its configuration.
     *
     */
    public function __invoke() : array
    {
        return [
            'templates'    => $this->getTemplates(),
        ];
    }

    /**
     * Returns the templates configuration
     */
    public function getTemplates() : array
    {
        return [
            'paths' => [
                'simple'    => [__DIR__ . '/../templates/simple'],
            ],
        ];
    }
}
```
> We recommend grouping configurations in methods

### Adding Template system to the Controller
* Create a private `template` member in your Handler class
* Create the class construct with the templating parameter required `__construct(TemplareRendererInterface $template)`

```
<?php

declare(strict_types=1);

namespace DotKernel\Simple\Action;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\RequestHandlerInterface;
use Zend\Diactoros\Response\HtmlResponse;
use Zend\Expressive\Template\TemplateRendererInterface;

class SimplePageHandler implements RequestHandlerInterface
{
    private $template;
    
    public function __construct(TemplateRendererInterface $template = null)
    {
        $this->template = $template;
    }
    
    public function handle(ServerRequestInterface $request) : ResponseInterface
    {
        $data = [
            'myMessage' => 'Hello world!'
        ];
        return new HtmlResponse($this->template->render('simple::hello-world', $data));
    }
}
```
* Your new handler version requires a Template Renderer that must be provided
* For this a Factory will be built

### Dependencies

* In order to work with templates you will need the template renderer injected in your handler
  * this can be done by requiring the template renderer (interface) in your controller
  * the handler will need a provider for your required dependency, a factory will be created

#### Step 1: requirement of dependencies
  * Create property `template` (recommended)
  * Create getters and setters for `template` (optional, but recommended)
  * Create constructor with the following signature `public function __construct(TemplateRendererInterface $template)`
  * **Important Note** don't forget to add the required use statement `use Zend\Expressive\Template\TemplateRendererInterface`

The class should look like the example below

src/Simple/src/Controller/SimpleController.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple\Controller;

use Dot\Controller\AbstractActionController;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\RequestHandlerInterface;
use Zend\Diactoros\Response\HtmlResponse;
use Zend\Expressive\Template\TemplateRendererInterface;

class SimpleController extends AbstractActionController
{
    /**
     * @var TemplateRendererInterface
     */
    protected $template;

    /**
     * @return TemplateRendererInterface
     */
    public function getTemplate(): TemplateRendererInterface
    {
        return $this->template;
    }

    /**
     * @param TemplateRendererInterface $template
     */
    public function setTemplate(TemplateRendererInterface $template)
    {
        $this->template = $template;
    }

    public function __construct(TemplateRendererInterface $template)
    {
        $this->template = $template;
    }

    public function indexAction()
    {
        return new HtmlResponse('Hello world!');
    }

    public function aboutUsAction()
    {
        return new HtmlResponse('About us page!');
    }
}
```

#### Step 2: creating the factory

* Create `src/Simple/src/Controller/SimpleControllerFactory.php`
* The namespace for this factory is `DotKernel\Simple\Controller`
* The class name for this factory is `SimpleControllerFactory`
 
* Create the `invoke` magic method with the following signature `public function __invoke(ContainerInterface $container) : MiddlewareInterface`, fully qualified name: `Psr\Http\Server\MiddlewareInterface`
* Make sure this method returns `SimpleController`

So far the class should look as in the example below:

src/Simple/src/Controller/SimpleControllerFactory.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple\Controller;

use Psr\Container\ContainerInterface;
use Psr\Http\Server\MiddlewareInterface;

class SimpleControllerFactory
{
    public function __invoke(ContainerInterface $container) : MiddlewareInterface
    {
        return new SimpleController();
    }
}

```

* You will notice `SimpleController` requires exactly one parameter and won't instantiate
* If you work with a basic **DotKernel** or **Zend Expressive** project the `TemplateRendererInterface` is already defined in the dependencies and can be retrieved easily from the container
* The following code can be added in the invoke method.
```php
$template = $container->get(TemplateRendererInterface::class);
return new SimpleController($template);
```

#### Step 3: Let "others" know about your dependencies

* Register the handler and its dependencies in your `ConfigProvider.php`
* Add a dependencies key in the main config array and a method getDependencies
  * getDependencies should return an array with dependencies
  * create a `factories` key in which your handler will point to its factory (dependency provider)
  * `SimpleController::class => SimpleControllerFactory::class`

* Final ConfigProvider should look as in the example below

src/Simple/src/ConfigProvider.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple;

use DotKernel\Simple\Action\SimplePageHandler;
use DotKernel\Simple\Action\SimplePageHandlerFactory;
use DotKernel\Simple\Controller\SimpleController;
use DotKernel\Simple\Controller\SimpleControllerFactory;

class ConfigProvider
{
    /**
     * Returns the configuration array
     *
     * To add a bit of a structure, each section is defined in a separate
     * method which returns an array with its configuration.
     *
     */
    public function __invoke(): array
    {
        return [
            'templates' => $this->getTemplates(),
            'dependencies' => $this->getDependencies()
        ];
    }

    /**
     * Returns the container dependencies
     */
    public function getDependencies(): array
    {
        return [
            'factories' => [
                SimplePageHandler::class => SimplePageHandlerFactory::class,
                SimpleController::class => SimpleControllerFactory::class
            ]
        ];
    }

    /**
     * Returns the templates configuration
     */
    public function getTemplates(): array
    {
        return [
            'paths' => [
                'simple' => [__DIR__ . '/../templates/simple'],
            ],
        ];
    }
}

```

### Using the templating system
The final step to using template in your system.

#### Data mapping
Remember `{{ myMessage }}` and `{{ aboutUsParagraph }}` ?

These are twig variables that will be populated with data you provide.

* Make sure all action methods have an array `$data = []`
* This array will be passed to the rendering system so it populates the page with data
* Make sure key arrays have the exact naming of their corespondends in twig template variables.
 

* The data array for `indexAction` will be:
```
$data = [
    'myMessage' => 'Hello world, now templated!'
];
```

* The data array for `aboutUsAction` will be:
```
$data = [
    'aboutUsParagraph' => 'About US, now templated!'
];
```

#### The template usage
The newly created controller has a property named `$template` which is a `TemplateRendererInterface`

Based on `TemplateRendererInterface` four methods are available:
* `render(string $name, $params = []) : string` - Render a template, optionally with parameters.
* `addPath(string $path, string $namespace = null) : void;` - Add a template path to the engine.
* `public function getPaths() : array;` - Retrieve configured paths from the engine.
* `addDefaultParam(string $templateName, string $param, $value) : void;` - Add a default parameter to use with a template.

To display a page `render()` will be used. Inside action methods:
* Get the template object in `$template`
  * `$template = $this->getTemplate()`
* Render the page
  * `$renderedResponse = $template->render('simple::hello-world', $data);`
* Return the rendered response (string) as a `ResponseInterface`
  * Zend Diactoros `HtmlResponse` can be used
  * return new HtmlResponse($renderedResponse);
    * don't forget the use statement for `Zend\Diactoros\Response\HtmlResponse`

The final Controller should look like this:

src/Simple/src/Controller/SimpleController.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple\Controller;

use Dot\Controller\AbstractActionController;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\RequestHandlerInterface;
use Zend\Diactoros\Response\HtmlResponse;
use Zend\Expressive\Template\TemplateRendererInterface;

class SimpleController extends AbstractActionController
{
    /**
     * @var TemplateRendererInterface
     */
    protected $template;

    /**
     * @return TemplateRendererInterface
     */
    public function getTemplate(): TemplateRendererInterface
    {
        return $this->template;
    }

    /**
     * @param TemplateRendererInterface $template
     */
    public function setTemplate(TemplateRendererInterface $template)
    {
        $this->template = $template;
    }

    public function __construct(TemplateRendererInterface $template)
    {
        $this->template = $template;
    }


    public function indexAction()
    {
        $data = [
            'myMessage' => 'Hello world, now templated!'
        ];
        // the template object
        $template = $this->getTemplate();
        $renderedResponse = $template->render('simple::hello-world', $data);
        return new HtmlResponse($renderedResponse);
    }

    public function aboutUsAction()
    {
        $data = [
            'aboutUsParagraph ' => 'About US, now templated!'
        ];
        // short version for the code in `indexAction`
        return new HtmlResponse($this->getTemplate()->render('simple::about-us', $data));
    }
}

```
