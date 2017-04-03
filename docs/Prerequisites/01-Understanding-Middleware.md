# Understanding Middleware

For a better understanding this will be a step by step tutorial.

##### Definition
Middleware is code that exists between the request and response, and which can take the incoming request, perform actions based on it, and either complete the response or pass delegation on to the next middleware in the queue.

##### The purpose
Middleware makes it easier for software developers to implement communication and input/output, so they can focus on the specific purpose of their application.


In web services the `Input` represents the `Request` received and `Output` represents the `Response` to be sent.

How using middleware in applications actually looks like.

### Middleware use 

Middleware can be used but not limited to the following purposes:

* A/B Testing
* Debugging
* Caching
* CORS
* Authentication (HTTP Basic Auth, OAuth 2.0, OpenID)
* CSRF Protection
* Rate Limiting
* Referrals
* IP Restriction
[https://philsturgeon.uk/php/2016/05/31/why-care-about-php-middleware/]


#### Why Middleware?
# INCOMPLETE STATEMENT !!!
As seen before, the middleware is called somewhere between

##### Usage

According to `http-interop-middleware`, any callable with the parameters `$request, $response, $next` in that order is a middleware

| Name        | Type                                       | Description                  |
|-------------|--------------------------------------------|------------------------------|
| `$request`  |  \Psr\Http\Message\ServerRequestInterface  | The PSR7 request object      |
| `$response` |  \Psr\Http\Message\ResponseInterface       | The PSR7 response object     |
| `$next`     |  callable                                  | The next middleware callable |



#### As a function 

```php
<?php

$middlewareFunction = function ($request, $response, $next) {
    // ...
};
```

#### As an invokable class 

```php
<?php

class MyMiddleware
{
    public function __invoke()
    {
        //
    }
}
```

#### Which one to use?
The function definition version can be used when working with simple examples / very small projects for a better understanding.
Depending on the programming principles you use and what's more convenient you can also use function definitions instead of invokable classes.
We strongly recommend that you use the invokable class version.

#### Why Invokable Classses
Where is the catch? Why use Invokable classes if the functions do the same thing. 
It all narrows to the point where external resources are involved.

What is the difference between a class  and a function?
* has access to other methods
* has access to dependencies

##### How does it have access to external resources

The middleware has access to `injected` external resources if created using the `factory` design pattern.

Not all middleware needs a factory.

# *** FACTORY *** 
# *** DI ***  

#### How is a middleware called?
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
// function 1 - displays one message
$middleware[] = function($request, $response, $next) {
    // do something
    
    // pass the control to next middleware
};
```

## Middleware in practice

An application will handle the middleware 

