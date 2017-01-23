## Understanding Middleware

For a better understanding this will be a step by step tutorial.

##### Definition
Middleware is code that exists between the request and response, and which can take the incoming request, perform actions based on it, and either complete the response or pass delegation on to the next middleware in the queue.

##### The purpose
Middleware makes it easier for software developers to implement communication and input/output, so they can focus on the specific purpose of their application.

![alt text](00-Resources/00-01.svg "Middleware")
In web services the `Input` represents the `Request` received and `Output` represents the `Response` to be sent.


![alt text](00-Resources/00-01.svg "Middleware-Calls")
How using middleware in applications actually looks like.


#### Why Middleware?
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
    public function invoke()
    {
        // 
    }
}
```


#### How is a middleware called?
```php
<?php
// assuming the request, response and next variables already exist
$middleware($request, $response, $next);
```

# Scratch -----------


### Example
The following example will teach you how to process a string with different aproaches.
`Note: the purpose of this document is to explain the middleware principle, not to implement the best method. `

##### The requirements
We have the given string `HeLlo wOrlD!`, which is obviously not well formatted. 
The well-formatted string expected is `Hello World`
For the string to be well formatted,  it first needs to be converted to lowercase (using `strtolower()`) and have the each word's letter capitalized (using `ucfirst()`).

##### First approach - The simple procedural way
This is the simplest approach and the 100% working version.
```php
<?php

function wellFormat($inputString)
    $inputString = 'HeLlo wOrlD!';
    // first copy the string
    $outputString = $inputString;
    $outputString = strtolower($outputString);
    $outputString = ucwords($outputString);
    // return the modified string
    return $outputString;
}

$inputString = 'HeLlo wOrlD!';
// write the string
$outputString = wellFormat($inputString);
echo $outputString;
```

##### Second approach - Still procedural
The variable `$inputString` was copied in `$outputString` for a reason. As you might already noticed we only work on the `$outputString` variable. At this point we are getting closer to the middleware concept.

A `request` can be considered read-only because the script executes on a request-basis and each request is unique.
The request will never be received again in the same context sothere is no reason to change it.

```php
<?php

function wellFormat($inputString)
    $inputString = 'HeLlo wOrlD!';
    // first copy the string
    $outputString = $inputString;
    $outputString = strtolower($outputString);
    $outputString = ucwords($outputString);
    // return the modified string
    return $outputString;
}

$inputString = 'HeLlo wOrlD!';
// write the string
$outputString = wellFormat($inputString);
echo $outputString;
```





Note: the `$outputString` variable is not the same within and outside the function. [Why?]('http://php.net/manual/en/language.variables.scope.php' "PHP Variable Scopes")

```php
<?php

function wellFormat($inputString) {
    $outputString = $inputString;
    $outputString = strtolower($outputString);
    $outputString = ucwords($outputString);
    return $outputString;
}

$inputString = 'HeLlo wOrlD!';
// write the string
$outputString = wellFormat($inputString);
echo $outputString;
```



#### Third approach - Making it look like middleware
```php
<?php
function middlewareUcFirst($input, &$output, ) {
    $outputString = ucwords($input);
    // no return yet
}

function middlewareStrToLower($input, &$output) {
    $outputString = ucwords($input);
    // no return yet
}
$inputString = 'HeLlo wOrlD!';
$outputString = $inputString;
middlewareStrToLower($inputString, $outputString)
```
First the functions must be separated, as a function should have only one responsability.
// don't touch input .. 
`Note: as currently working with strings, the & operand is used to work with the variables by reference, if working with objects PHP will automatically work with reference`

## Recommended links
* [What is PSR-7?]('http://www.php-fig.org/psr/psr-7/')
* [Zend Stratigility - Middleware]('https://github.com/zendframework/zend-stratigility/blob/master/doc/book/middleware.md')
* [Zend Expressive - Features]('https://zendframework.github.io/zend-expressive/getting-started/features/')