# Controllers

- [Controllers](#controllers)
    - [MVC Pattern](#mvc-pattern)
    - [Accessing the controller](#accessing-the-controller)
        - [Single method controllers](#single-method-controllers)
        - [Multiple method controllers](#multiple-method-controllers)
    - [Business logic](#business-logic)
    - [The catch](#the-catch)

## MVC Pattern

Controllers play an intricate part of the MVC pattern that DotKernel has adopted. The MVC pattern means that the application is made up of three core parts.

- Models (Also called entities)
- Views (Also called templates)
- Controllers

Controllers act as an intermediary between the request, the model and the view. The controller is responsible for calling all the business logic, albeit through other classes or [services](Services.md).

The controller receives the current request and can fetch variables and data from the request.

## Accessing the controller

In your routes config, when you specify routes, you also specify which Controller to route the request to.

### Single method controllers

```php
$app->get('/ping', PingAction::class, 'app.ping');
```

> When we hit '/ping', it'll route the request to the `PingAction` controller

```php
<?php
declare(strict_types=1);

namespace Frontend\App\Action;

use Dot\AnnotatedServices\Annotation\Inject;
use Dot\AnnotatedServices\Annotation\Service;
use Interop\Http\ServerMiddleware\DelegateInterface;
use Interop\Http\ServerMiddleware\MiddlewareInterface;
use Psr\Http\Message\ServerRequestInterface;
use Zend\Diactoros\Response\JsonResponse;
use Zend\Expressive\Helper\UrlHelper;

/**
 * Class PingAction
 * @package Frontend\App\Controller
 *
 * @Service()
 */
class PingAction implements MiddlewareInterface
{
    /** @var UrlHelper */
    protected $urlHelper;

    /**
     * PingAction constructor.
     * @param UrlHelper $urlHelper
     *
     * @Inject({UrlHelper::class})
     */
    public function __construct(UrlHelper $urlHelper)
    {
        $this->urlHelper = $urlHelper;
    }

    /**
     * @param ServerRequestInterface $request
     * @param DelegateInterface $delegate
     * @return JsonResponse
     */
    public function process(ServerRequestInterface $request)
    {
        return new JsonResponse([
            'ack' => time()
        ]);
    }
}

```

The process method is the default method that's being called. This will however only allow the controller to perform a single action. A controller per action is way overkill, so we usually have another way of creating the routes, where we include a dynamic field called `action`, and use this to select which action to call on the respective controller.

### Multiple method controllers

```php
$app->route('/page[/{action}]', PageController::class, ['GET', 'POST'], 'page');
```

The `[/{action}]` part says that we can optionally include an action.
> If no action is provided, `indexAction` is the default action in a multi-action controller

If we hit `/page/about` the `aboutAction` in the `PageController` class would be responsible for showing the correct data.

```php
<?php

namespace uDare\Frontend\Page\Controller;

use Dot\AnnotatedServices\Annotation\Service;
use Dot\Controller\AbstractActionController;
use Dot\Controller\Plugin\TemplatePlugin;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\UriInterface;
use Zend\Diactoros\Response\HtmlResponse;
use Zend\Form\Form;

/**
 * Class PageController
 * @package Frontend\App\Controller
 *
 * @Service()
 */
class PageController extends AbstractActionController
{
    /**
     * @return ResponseInterface
     */
    public function indexAction(): ResponseInterface
    {
        $data = [];

        $data['routerName'] = 'FastRoute';
        $data['routerDocs'] = 'https://github.com/nikic/FastRoute';

        $data['templateName'] = 'Twig';
        $data['templateDocs'] = 'http://twig.sensiolabs.org/documentation';

        return new HtmlResponse($this->template('app::home', $data));
    }

    /**
     * @return ResponseInterface
     */
    public function aboutAction(): ResponseInterface
    {
        return new HtmlResponse($this->template('page::about-us'));
    }
}
```

The PageController is responsible for showing the correct page or throw the correct error. When page/non-existant is tried, it should show the correct 404 error as well.

> You may inject services etc. through the constructor, if it's necessary

## Business logic

Business logic should be abstracted away from the controllers, and instead be injected through an interface-based service. This decouples the code, and makes it possible to change services and implementations easily.

## The catch

Controllers are difficult to get right, as the amount of code in a controller ranges a lot from developer to developer. Some developers see Controllers as something that lives mostly in the ether, and contains the absolute minimum amount of code, where as others are more liberal in their use of code in a controller.

Finding the right approach comes down to team coordination, skill and preference.
A large controller can be very difficult to debug, as you can't really find your way around it, but the same goes for very spaghetti-like code.
