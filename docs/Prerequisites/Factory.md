# Factories in DotKernel 3

- [Factories in DotKernel 3](#factories-in-dotkernel-3)
    - [Factory Example](#factory-example)
    - [Factories can be a hassle](#factories-can-be-a-hassle)
        - [Injection example](#injection-example)

DotKernel is heavily reliant on the Factory design pattern, which means that to access anything outside the request in a controller,
you need to inject it through a Factory.

A Factory is a class that does some kind of preparation before a controller is initiated, because the controller requires the prepared object as a dependency.

> We recomment putting your factories in the `src/App/src/Factory` directory.

## Factory Example

This is a Factory, responsible for telling the controller whether the app is in `production` or `development` mode:

```php
<?php

declare(strict_types=1);
namespace App\Command\Factory;

use Interop\Container\ContainerInterface;

class CommandFactory
{
    public function __invoke(ContainerInterface $container, $requestedName)
    {
        // Get global config
        $config = $container->get('config');
        // Use the debug flag to validate environment
        $isDevelopment = $config['debug'];
        // Return the command
        return new $requestedName($isDevelopment);
    }
}
```

> $requestedName is the `controller` that we requested, and we're passing `$isProduction` into its constructor.

Factories have access to the `Service Container`, which controllers doesn't.
We resolve the required dependencies from the container, and pass them to the controller.

> DotKernel also relies upon [Services](Services.md) to decouple the business logic.
The Factory is also where you'd resolve the correct service to pass onto the controller for its use.

## Factories can be a hassle

That's why DotKernel has a package called [dot-annotated-services](https://github.com/dotkernel/dot-annotated-services),
which allows you to inject items from the Service Container, witout a factory.

> Remember to import the uses at the top of your document:
- `use Dot\AnnotatedServices\Annotation\Service;`
- `use Dot\AnnotatedServices\Annotation\Inject;`

It can be done either with a FQCN or the alias, which gives us the following two examples:

> This example showcases a controller which will receive the exact same item as the example above

### Injection example

```php
/**
 * Controller constructor
 * @param bool $isDevelopment
 * ...
 *
 * @Inject({"config.debug"})
 */
public function __construct(bool $isDevelopment)
{
    $this->isDevelopment = $isDevelopment;
}
```

> This example showcases how to inject a class via a FQCN

```php
/**
 * Controller constructor
 * @param Dependency1 $dep1
 * ...
 *
 * @Inject({Dependency1::class, Dependency2::class, ...})
 */
public function __construct(Dependency1 $dep1, Dependency2 $dep2, ...)
{
    $this->dep1 = $dep1;
    //...
}
```
