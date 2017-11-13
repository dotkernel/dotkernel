# Routing in DotKernel 3

- [Routing in DotKernel 3](#routing-in-dotkernel-3)
    - [What is Routing](#what-is-routing)
        - [Paths vs. queries](#paths-vs-queries)
            - [The DotKernel 3 routing signature](#the-dotkernel-3-routing-signature)
    - [Examples](#examples)
        - [Simple Route](#simple-route)
    - [Route with parameter](#route-with-parameter)
    - [Route with optional parameter](#route-with-optional-parameter)
    - [Route with required parameter and optional parameter](#route-with-required-parameter-and-optional-parameter)
    - [Route allowing GET & POST and conditional paramter with multiple middleware](#route-allowing-get-post-and-conditional-paramter-with-multiple-middleware)

DotKernel 3 is based on [Zend Expressive](https://docs.zendframework.com/zend-expressive/) and most parts of this article should be compatible with Zend Expressive as well.

## What is Routing

Routing is assigning a behaviour to a specific URL pattern.

According to the Zend Framework 1 documentation:
> Routing is the process of taking a URI endpoint (that part of the URI which comes after the base URL) and decomposing it into parameters to determine which Module, controller, and action of that controller should receive the request.

When using middleware the routing is made by pointing patterns to the desired middleware class(es).

### Paths vs. queries

Routing based on path:

- `http://example.com/product/list` - will display a product listing
- `http://example.com/product/list/electronics` - will display a product listing on electronics category
- `http://example.com/product/view/16` - will display details of product with `id=16`

Routing based on queries:

- `http://example.com/index.php?action=product-list` - will display a product list
- `http://example.com/index.php?action=product-list&category=electronics` - will display a product listing on electronics category
- `http://example.com/index.php?action=product&id=16` - will display details of product with `id=16`

#### The DotKernel 3 routing signature

```php
public function route(string $path, array|callable $middleware = null, string|array $methods = null, string $name = null);
```

To add a route edit the "route list" defined in the `config/router.php` file:

```php
$app->route(
    '/myModule/',  // route path
    [MyModule\Controller\MyModuleController::class], // middleware stack
    ['GET', 'POST'], // HTTP methods
    'app.mymodule' // route name
);
```

Or the short version:

```php
$app->route('/myModule/', [MyModule\Controller\MyModuleController::class], ['GET', 'POST'], 'app.mymodule');
```

Parameters:

- `$path` - string path to match against (can also be a regex pattern)
- `$middleware` - `callable` or `array` of callables, the middlewares to execute on specified path
- `$methods` - HTTP methods to respond for (usually GET & POST), but can also respond to `PUT`, `PATCH` and `DELETE`
- `$name` - a name for the route, used for URL generation, to make sure the route is always generated correctly, even if it changes

## Examples

> Note: All `My Module` related classes are assumed and may not exist in your project.

### Simple Route

- No parameters
- Only one middleware
- Responding exclusively to `GET` HTTP method

```php
$app->route(
    '/product/list',  // route path
    MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods, only get
    'shop.product' // route name
);
```

## Route with parameter

- One (required) parameter
  - `categoryId` is required, if no other route is defined a 404 message will be returned.
- Only one middleware
- Responding exclusively to `GET` HTTP method

```php
$app->route(
    '/product/list/{categoryId}',  // route path
    MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods
    'shop.list-category' // route name
);
```

## Route with optional parameter

- One (optional) parameter
  - if `categoryId` is defined show products with given category id, else show all product
- Only one middleware
- Responding exclusively to `GET` HTTP method

```php
$app->route(
    '/product/list[/{categoryId}]',  // route path
    MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods
    'shop.list-category-optional' // route name
);
```

## Route with required parameter and optional parameter

- two parameters
  - `categoryId` is required, if no other route is defined a 404 message will be returned.
  - `sort` type is optional, sort by relevance
- Only one middleware
- Responding exclusively to `GET` HTTP method

```php
$app->route(
    '/product/list/{categoryId}[/{sort}]',  // route path
    MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods
    'shop.list-category-sort-optional' // route name
);
```

## Route allowing GET & POST and conditional paramter with multiple middleware

- one parameter
  - `productId` is required and must be a digit (`\d+` is the regex pattern for digits)
- two middleware
- responds to GET & POST
  - `GET` - display the product data (before editing)
  - `POST` - change the product data

```php
$app->route(
    '/product/edit/{productId:\d+}',  // route path
    [
        MyModule\Validator\ProductDataValidator::class,
        MyModule\Controller\ProductListController::class,
    ], // only one middleware
    ['GET', 'POST'], // HTTP methods
    'shop.product-edit' // route name
);
```

Sources:

- [Router - ZF1 Documentation](https://framework.zend.com/manual/1.12/en/zend.controller.router.html)
