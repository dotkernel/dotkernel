# Creating a contact controller

We refer here to DotKernel controllers, which are middleware that can handle requests based on an action parameter. 
One method per action is used. Controllers help organize code by keeping related code together. 
We recommend you to check the documentation for [dot-controller](https://github.com/dotkernel/dot-controller)

### Create the controller file
* create a new file in `/src/App/src/Controller` and call it `ContactController.php`. Make sure the namespace is `Frontend\App\Controller`. This is how the autoloading is configure in composer.json for this sub-module. A controller must extend `Dot\Controller\AbstractActionController` class.
* next, create an `indexAction` that will handle the `/contact` request and let's also make a thank you page, after the contact form is submitted. So, create a `thankYouAction` method also. This will handle requests to path `/contact/thank-you`.

```php
<?php
declare(strict_types=1);

namespace Frontend\App\Controller;

use Dot\AnnotatedServices\Annotation\Service;
use Dot\Controller\AbstractActionController;
use Dot\Controller\Plugin\Authentication\AuthenticationPlugin;
use Dot\Controller\Plugin\Authorization\AuthorizationPlugin;
use Dot\Controller\Plugin\FlashMessenger\FlashMessengerPlugin;
use Dot\Controller\Plugin\Forms\FormsPlugin;
use Dot\Controller\Plugin\TemplatePlugin;
use Dot\Controller\Plugin\UrlHelperPlugin;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\UriInterface;
use Zend\Diactoros\Response\HtmlResponse;
use Zend\Form\Form;
use Zend\Session\Container;

/**
 * Class ContactController
 * @package Frontend\App\Controller
 *
 * @method UrlHelperPlugin|UriInterface url(string $route = null, array $params = [])
 * @method FlashMessengerPlugin messenger()
 * @method FormsPlugin|Form forms(string $name = null)
 * @method TemplatePlugin|string template(string $template = null, array $params = [])
 * @method AuthenticationPlugin authentication()
 * @method AuthorizationPlugin isGranted(string $permission, array $roles = [], mixed $context = null)
 * @method Container session(string $namespace)
 *
 * @Service
 */
class ContactController extends AbstractActionController
{
    public function indexAction(): ResponseInterface
    {
        // ...
    }
    
    public function thankYouAction(): ResponseInterface
    {
        // ...
    }
}
```

Two things you should consider:
* make sure you import/use the right packages. We recommend you use an IDE to help you out.
* the PHP docblock has 2 functions here. The `@method` definistions are used to tell the IDE(if supported) what unkown methods we'll be using in this class. This way, if we use controller plugins, they will not show up us warnings. You can copy paste this docblock in all your controller files. Make sure you import the classes though as use statements. These method definitions are handles all the default DotKernel controller plugins.
* The second annotation, `@Service` is used to mark the class to be handled by the [dot-annotated-services](https://github.com/dotkernel/dot-annotated-services) package. See the documentation for more information. Shortly, this allows you to get the class from the service manager, without the need to manually create and register a factory class. It can also inject dependencies, by annotating the constructor or a setter method.

### Register the controller as routed middleware

* in your **route.php** file, register this controller, the same way as a regular middleware. The requirement is that you add an optional `action` route parameter.

```php
$app->route('/contact[/{action}]', [ContactController::class], ['GET', 'POST'], 'contact');
```

At this point, it should be possible to access the `/contact` and `/contact/thank-you` pages. But we do not return a ResponseInterface yet, in which case you should get an error, if you defined the methods as above, with return types(as of PHP 7.1). Lets solve this right now.

We know we'll have 2 templates, one for the contact form and one for the thanks you page. Lets create them, even if we don't include any content yet. Because we work in the App module, create the templates in the `/templates` folder of this module. You can create a new folder inside, or use one of the existing folder as appropriate. We'll define the templates in the `/temlates/app` folder, so when referring to the template, you must prefix it with the `app` namespace, as `app::template_name`.
