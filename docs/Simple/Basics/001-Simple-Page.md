# DotKernel3 Simple

## Displaying a simple page

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

* Register the template namespace in `ConfigProvider`

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

### Creating the Handler
* Create the basic handler class in `src/Simple/src/Action/SimplePageHandler.php`

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
    public function handle(ServerRequestInterface $request) : ResponseInterface
    {
        return new HtmlResponse('Hello world!');
    }
}
```

* Register the handler in your `ConfigProvider.php`

src/Simple/src/ConfigProvider.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple;

use DotKernel\Simple\Action\SimplePageHandler;

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
        ];
    }

    /**
     * Returns the container dependencies
     */
    public function getDependencies(): array
    {
        return [
            'invokables' => [
                SimplePageHandler::class => SimplePageHandler::class
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

* Assign your handler to route `/simple`

config/routes.php
```php
return function (Application $app, MiddlewareFactory $factory, ContainerInterface $container) : void {
    $app->route('/simple', DotKernel\Simple\Action\SimplePageHandler::class, ['GET'], 'simple.page');
};
```

### Using the Template system
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
  * The factory's purpose is to get the configuration, build the dependencies pass dependencies to the requested object `__construct()`

src/Simple/src/Action/SimplePageHandlerFactory.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple\Action;

use Psr\Container\ContainerInterface;
use Psr\Http\Server\RequestHandlerInterface;
use Zend\Expressive\Template\TemplateRendererInterface;

class SimplePageHandlerFactory
{
    public function __invoke(ContainerInterface $container) : RequestHandlerInterface
    {
        $template = $container->has(TemplateRendererInterface::class)
            ? $container->get(TemplateRendererInterface::class)
            : null;

        return new SimplePageHandler($template);
    }
}
```

* Register your new dependencies in the config provider

src/Simple/src/ConfigProvider.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple;

use DotKernel\Simple\Action\SimplePageHandler;
use DotKernel\Simple\Action\SimplePageHandlerFactory;

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
                SimplePageHandler::class => SimplePageHandlerFactory::class
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
