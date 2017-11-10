# Understanding Middleware

- [Understanding Middleware](#understanding-middleware)
    - [Definition](#definition)
    - [The purpose](#the-purpose)
    - [Using middlewares](#using-middlewares)
    - [Why Middleware](#why-middleware)
    - [Usage](#usage)
    - [As a closure](#as-a-closure)
    - [As an invokable object](#as-an-invokable-object)
    - [Which one to use](#which-one-to-use)
        - [Why Invokable Objects](#why-invokable-objects)
    - [How is a middleware called](#how-is-a-middleware-called)
    - [Middleware in (simple) theory](#middleware-in-simple-theory)
    - [Middleware in practice](#middleware-in-practice)

## Definition

Middleware is code that exists between the request and response, and which can take the incoming request, perform actions based on it, and either complete the response or pass delegation on to the next middleware in the queue.

## The purpose

Middleware makes it easier for software developers to implement communication and input/output, so they can focus on the specific purpose of their application.


In web services the `Input` represents the `Request` received, and `Output` represents the `Response` to be sent.

## Using middlewares

Middleware can be used to, but is not limited to, the following purposes:

- A/B Testing
- Debugging
- Caching
- CORS
- Authentication (HTTP Basic Auth, OAuth 2.0, OpenID)
- CSRF Protection
- Rate Limiting
- Referrals
- IP Restriction

## Why Middleware

As seen before, the middleware is called somewhere between receiving the request and emmiting the response.

> Note: the response object is subject to changes until emitted

## Usage

According to `http-interop-middleware`, any callable with the parameters `$request, $response, $next` in that order is a middleware

| Name        | Type                                     | Description                  |
| ----------- | ---------------------------------------- | ---------------------------- |
| `$request`  | \Psr\Http\Message\ServerRequestInterface | The PSR7 request object      |
| `$response` | \Psr\Http\Message\ResponseInterface      | The PSR7 response object     |
| `$next`     | callable                                 | The next middleware callable |

Middleware returns an `\Psr\Http\Message\ResponseInterface` object.

## As a closure

```php
<?php

$middlewareFunction = function ($request, $response, $next) {
    // ...
};
```

## As an invokable object

```php
<?php

class MyMiddleware
{
    public function __construct(MyDependencyInterface $myDependency)
    {
        $this->myDependency = $myDependency;
    }

    public function __invoke()
    {
        //
    }
}
```

> An invokable object is an instance of a class with the `__invoke()` magic method declared

## Which one to use

The function definition version can be used when working with simple examples / very small projects for a better understanding.
Depending on the programming principles you use and what's more convenient you can also use function definitions instead of invokable classes.

> We strongly recommend that you use the invokable class option.

### Why Invokable Objects

Where is the catch? Why use Invokable classes if the functions do the same thing.
It all narrows to the point where external resources are involved.

What is the difference between a class and a function?

- A class has access to other methods
- a class has access to dependencies

How does it have access to external resources?

Dependencies are `injected` trough the class constructor. (as seen in `MyMiddleware` class)
For `decoupling` dependencies are represented in `Interfaces`, therefore custom implementations can be made if needed.

> Coupling is the degree of interdependence between software modules.
> Decoupling or loose coupling means components are independent and are not aware of each other.

The middleware has access to `injected` external resources if created using the `factory` design pattern.

## How is a middleware called

To call one of the above middleware you must either instatiate a new `MyMiddleware` object, or store a function definition in a variable.

```php
<?php
// assuming the request, response and next variables already exist
$middleware($request, $response, $next);
```

## Middleware in (simple) theory

The following example will illustrate how the middleware is called.

An array with functions with the same pattern will be built.
Each function will execute an operation.
In this example the `next` parameter won't be used because the calling order will be sequential.

```php
$middleware = [];
// function 1 - displays one message, function 2 - displays another message. etc.
$middleware[] = function($request, $response, $next) {
    // do something

    // pass the control to next middleware, can also be called before "doing something"
    // $response = $next($request, $response);
    return $response;
};
```

Once the `$middleware` array is complete with closures and/or invokable objects it can be iterated.

```php
foreach ($middleware as $callable) {
    $response = $middleware($request, $response);
}

echo $response->getBody(); // calls $response->getBody()->toArray() for displaying body contents
```

> Note: in this case $next is not needed as we call each $callable sequentially
> Note: The response must be manually converted to a string

## Middleware in practice

> Note: Middlewares won't be called just using a foreach statement.
Requests will be jandled by the application, which will call the correct middlewares.

Example dispatcher applications:
Zend Stratiglility, Zend Expressive, etc.

Sources:
- [Why Care About PHP Middleware?](https://philsturgeon.uk/php/2016/05/31/why-care-about-php-middleware/)
