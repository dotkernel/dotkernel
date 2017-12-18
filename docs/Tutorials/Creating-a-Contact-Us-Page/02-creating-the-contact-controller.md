# Creating the contact controller

- [Creating the contact controller](#creating-the-contact-controller)
    - [Create the controller file](#create-the-controller-file)
    - [Register the controller as routed middleware](#register-the-controller-as-routed-middleware)
        - [contact.html.twig](#contacthtmltwig)
        - [thank-you.html.twig](#thank-youhtmltwig)
    - [Display a contact us menu item](#display-a-contact-us-menu-item)
    - [Previous | Next](#previous-next)
        - [Tutorial index](#tutorial-index)
        - [All tutorials](#all-tutorials)

We refer here to DotKernel controllers, which are middleware that can handle requests based on an action parameter.
One method per action is used. Controllers help organize code by keeping related code together.
We recommend you to check the documentation for [dot-controller](https://github.com/dotkernel/dot-controller)

## Create the controller file

- create a new file in `/src/App/src/Controller` and call it `ContactController.php`. Make sure the namespace is `Frontend\App\Controller`. This is how the autoloading is configured in composer.json for this sub-module.
- next, create an `indexAction` that will handle the `/contact` request and let's also make a thank you page, after the contact form is submitted. So, create a `thankYouAction` method also. This will handle requests to path `/contact/thank-you`.

> Controllers must extend `Dot\Controller\AbstractActionController` class

```php
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

- make sure you import/use the right packages. We recommend you to use an IDE to help you out.
- the PHP docblock has 2 functions here. The `@method` definitions are used to tell the IDE(if supported) what unkown methods we'll be using in this class. This way, if we use controller plugins, they will not show up as warnings. You can copy paste this docblock in all your controller files. Make sure you import the classes as use statements. These method definitions should get rid of all the default DotKernel controller plugins warnings.
- The second annotation, `@Service` is used to mark the class to be handled by the [dot-annotated-services](https://github.com/dotkernel/dot-annotated-services) package. See the documentation for more information. Shortly, this allows you to get the class from the service manager, without the need to manually create and register a factory class. It can also inject dependencies, by annotating the constructor or a setter method.

## Register the controller as routed middleware

- in your **routes.php** (`config/routes.php`) file, register this controller, the same way as a regular middleware. The requirement is that you add an optional `action` route parameter.

```php
$app->route('/contact[/{action}]', [ContactController::class], ['GET', 'POST'], 'contact');
```

At this point, it should be possible to access the `/contact` and `/contact/thank-you` pages. But we do not return a ResponseInterface yet, therefore you should get an error if you defined the methods as above, with return types (as of PHP 7.1). Lets solve this right now.

We have 2 templates, one for the contact form and one for the thank-you page. Lets create them, even if we don't include any content yet. Because we work in the App module, create the templates in the `/templates` folder in this module. You can create a new folder inside, or use one of the existing folders as you see appropriate. We'll define the templates in the `/templates/app` folder, so when referring to the template, you must prefix it with the `app` namespace, as `app::template_name`.

### contact.html.twig

```html
{ % extends '@layout/default.html.twig' %}

{ % block title %}Contact Us{ % endblock %}

{ % block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3 col-lg-6 col-lg-offset-3 no-padding forms">
                <h1>Contact Us</h1>
                <div class="form-content">

                </div>
            </div>
        </div>
    </div>
{ % endblock %}
```

### thank-you.html.twig

```html
{ % extends '@layout/default.html.twig' %}

{ % block title %}Contact Us{ % endblock %}

{ % block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3 col-lg-6 col-lg-offset-3 no-padding forms">
                <h1>Thank you!</h1>

            </div>
        </div>
    </div>
{ % endblock %}
```

> Note: due to technical restrictions the `{` and `%` have to be separated in the documentation. Please remove the space between them in your code.

- Now go back to the contact controller and return the parsed templates

```php
class ContactController extends AbstractActionController
{
    public function indexAction(): ResponseInterface
    {
        return new HtmlResponse($this->template('app::contact'));
    }

    public function thankYouAction(): ResponseInterface
    {
        return new HtmlResponse($this->template('app::thank-you'));
    }
}
```

## Display a contact us menu item

We will use the already configured navigation service. Menu items are defined in the configuration file `config/autoload/navigation.global.php`. Add a new entry in the main_menu navigation container. The array order is reflected in the menu items parsing order.

```php
return [
    'dot_navigation' => [
        'containers' => [
            'main_menu' => [
                //...

                [
                    'options' => [
                        'label' => 'Contact',
                        'route' => [
                            'route_name' => 'contact',
                            'options' => [
                                'reuse_result_params' => false,
                            ]
                        ],
                        'icon' => '',
                    ]
                ],
            ]
        ]
    ]
]
```

The route option `reuse_result_params` needs a bit of explanation here. It is an option supported by the Zend Expressive UrlHelper class. It is a new addition, in order to tell the url generator helper to NOT merge the matched route params in the generated url.

In our example, we need this because our route is defined as `/contact[/{action}]`. If the above option would have been set to `true`(as it is by default), if we would have been on a page like `/contact/thank-you`, the menu item will point to the same URI, because the matched `action` parameter would have been merged into the undefined route parameters. This is not the desired behavior, so anytime you encounter a problem like this, remember to disable this option as described above.

## Previous | Next

**[Prev: Introduction](01-introduction.md)** | **[Next: Planning the contact form implementation](03-planning-the-contact-form-implementation.md)**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
