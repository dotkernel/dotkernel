# DotKernel3 Simple

## Adding Dependencies to a Middleware or Handler
This article will guide through adding new dependencies to an existing middleware or handler.

### What is Dependency Injection
> In software engineering, dependency injection is a technique whereby one object (or static method) supplies
the dependencies of another object. A dependency is an object that can be used (a service). An injection is the
passing of a dependency to a dependent object (a client) that would use it.

Definition  source: [Wikipedia - Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection)

### Shortest possible version
Using the `Dependency Injection` tehnique your dependencies will be added through the **Handler/Middleware**'s
`__construct()` method. 

The basic page handler in the [first tutorial](./001-Simple-Page.md)  uses a dependency, the `TemplateRendererInterface`.

### Starting Point
The following resources are the final results from the [first tutorial](./001-Simple-Page.md). For a deeper
understanding  we recommend reading it before continuing.


#### The Simple Page Handler
src/Simple/src/Action/SimpleHandler.php
```php
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

#### The Simple Page Handler Factory
src/Simple/src/Action/SimplePageHandler.php
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

#### The Config Provider

src/Simple/src/ConfigProvider.php
```php
<?php

declare(strict_types=1);

namespace DotKernel\Simple;

use DotKernel\Simple\Action\SimplePageHandler;
use DotKernel\Simple\Action\SimplePageHandlerFactory;
use DotKernel\Simple\Controller\SimpleController;
use DotKernel\Simple\Controller\SimpleControllerFactory;
use DotKernel\Simple\Logger\LoggingErrorListenerFactory;
use Zend\Stratigility\Middleware\ErrorHandler;

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
            ],
            'delegators' => [
                ErrorHandler::class => [
                    LoggingErrorListenerFactory::class,
                ],
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

### Before adding the dependency

As per first tutorial:
> The factory's purpose is to get the configuration, build the dependencies pass dependencies to the requested object `__construct()`

Before adding the dependency into your handler/middleware the `Dependency Injection Container`, in our case
Zend Service Manager.

#### What is the Zend Service Manager?
According to the Zend documentation:

> The Service Manager is a modern, fast, and easy-to-use implementation of the Service Locator design pattern.
The implementation implements the `Container Interop` interfaces, providing interoperability with other
implementations.

Definition Source: 
[Quick Start - zend-servicemanager](https://docs.zendframework.com/zend-servicemanager/quick-start/)

For more information read about  
[`how the Service manager works`](https://docs.zendframework.com/zend-servicemanager/quick-start/)

### Adding the dependency
Assuming we have a `Me\MyDependency\MyDependency` class which implements `Me\MyDependency\MyDependencyInterface` 
the following changes need to be made for each of the files.

#### Register the dependency in the DI Container
Most modules have a ConfigProvider written so you are not required to add it in your ConfigProvider.

If custom classes are used or if there is no Config Provider in the dependency, it should be declared your module's ConfigProvider.

Have a look at the following `dependency configuration`
```php
[
    'aliases' => [
        // a generic example
        MyDependencyInterface::class => MyDependency::class,
        // a real example
        RandomNumberGeneratorInterface::class => MyRandomNumberGenerator::class,
    ],
    'invokables' => [
        // the generic example
        MyDependency::class => MyDependency::class,
        // the real example
        MyRandomNumberGenerator::class => MyRandomNumberGenerator::class,
    ],
    'factories' => [
        // A Random number generator with dependencies provided by a factory
        MyAlternateRandomNumberGenerator::class => MyAlternateRandomNumberGeneratorFactory::class,
    ]
]
```

##### Why use aliases?
Aliases can help if you have more versions of a module or class.

If using aliases `RandomGeneratorInterface::class` can point to any of
`MyRandomNumberGenerator::class` and `MyAlternateRandomNumberGenerator::class`

Both classes implement `RandomGeneratorInterface` which means they have the same result, but obtained through a different behaviour.

The `RandomGeneratorInterface::class` entry can be overriden by the configs in `config/autoload/`.

The following code will map `RandomGeneratorInterface::class` to the **alternate** generator

configs/autoload/number-generator.local.php
```php
<?php

// use statements

return [
    'dependencies' => [
        'aliases' => [
            RandomGeneratorInterface::class => MyAlternateRandomNumberGenerator::class,
        ]
    ]
];
```



<!--
In the config provider for your handler/middleware the dependencies must be added.

If no config provider is present, a config can be made in `config/autoload/*.global.php` 
-->