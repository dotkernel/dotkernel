# Zend Stratigility

- [Zend Stratigility](#zend-stratigility)
    - [Example](#example)
    - [Initializing Application (Stratigility\MiddlewarePipe)](#initializing-application-stratigilitymiddlewarepipe)
    - [Piping middleware to routes](#piping-middleware-to-routes)
        - [Piping to route "/" (also applies to all routes)](#piping-to-route-also-applies-to-all-routes)
        - [Routes to route "/about"](#routes-to-route-about)
    - [Running the application](#running-the-application)
    - [Emitting the response](#emitting-the-response)
    - [Limitations](#limitations)

PSR-7 middleware foundation for building and dispatching middleware pipelines.

This package relies on `zendframework/zend-diactoros`, which is Zend's implementation of PSR-7 interfaces.
Using Stratigility, we can build applications out of middleware.

## Example

As mentioned in the [Understanding Middleware](Understanding-Middleware.md) article, Zend Stratigility is an application
used to handle middleware.

Below is an example of piping middlewares to different routes.

> Note: Multiple middleware can be piped to routes.

## Initializing Application (Stratigility\MiddlewarePipe)

```php
<?php
//declare the fully qualified class names that we use at the beginning of the file
//we'll add more as we progress
use Zend\Stratigility\MiddlewarePipe;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Message\ResponseInterface;
use Zend\Diactoros\Response\SapiEmitter;

//change the current directory to the parent of /public
chdir(dirname(__DIR__));

//we load the file by using its relative path(to the current dir we have set above)
require 'vendor/autoload.php';

// generate the request and response
$request = ServerRequestFactory::fromGlobals($_SERVER, $_GET, $_POST, $_COOKIE, $_FILES);
$response = new Response();

// initializing Zend\Stratigility\MidlewarePipe application
$application = new MiddlewarePipe();
```

## Piping middleware to routes

### Piping to route "/" (also applies to all routes)

```php
$application->pipe('/',
    function(ServerRequestInterface $request, ResponseInterface $response, callable $next = null)
    {
        //check the uri path to see if we requested the home page or not
        if(! in_array($request->getUri()->getPath(), ['', '/'])) {
            //if path not empty, we have nothing to do here, call next middleware to display the requested page
            return $next($request, $response);
        }

        //display the html home page, we use the zend-diactoros HtmlResponse
        //HtmlResponse accepts the html string as parameter. It sets code 200 by default and add the Content-Type header text/html
        return new HtmlResponse("<h1>Home page</h1>");
    }
);
```

### Routes to route "/about"

```php
$application->pipe('/about',
    function(ServerRequestInterface $request, ResponseInterface $response, callable $next = null) {
        //directly return a response
        return new HtmlResponse("<h1>About Us</h1>");
    }
);
```

## Running the application


```php
//run the application with the initial request/response object created earlier
$response = $application($request, $response);
```

## Emitting the response

```php
//we need to send the response to the browser, we use diactoros emitter class that do the conversion for us
$emitter = new SapiEmitter();
$emitter->emit($response);
```

## Limitations

Zend Stratigility is easy to work with but there are some constraints:

- routing is static (routes like `user/id/20`) cannot be automatically parsed
- there is no template engine
- there is no container for dependencies, they must be created manually

The solution for these limitations is using [Zend Expressive](Zend-Expressive.md).

Sources:
[zend-diactoros](https://zendframework.github.io/zend-diactoros/)
[zend-stratigility](https://zendframework.github.io/zend-stratigility/)
