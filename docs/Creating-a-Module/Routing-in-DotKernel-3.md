# Routing in DotKernel 3
---

DotKernel 3 based on [Zend Expressive](https://docs.zendframework.com/zend-expressive/). 

The routing signature is the following:

```php
public function route(string $path, array|callable $middleware = null, string|array $methods = null, string $name = null);
```

To add a route edit the "route list" defined in the `config/router.php` file:

```php
$app->route(
    '/myModule/',  // route path
    [Frontend\MyModule\Controller\MyModuleController::class], // middleware stack
    ['GET', 'POST'], // HTTP methods
    'app.mymodule' // route name
);
```

Or the short version:

```php
$app->route('/myModule/', [Frontend\MyModule\Controller\MyModuleController::class], ['GET', 'POST'], 'app.mymodule');
```

Parameters:
* `$path` - string path to match against (can also be a regex pattern)
* `$middleware` - `callable` or `array` of callables, the middlewares to execute on specified path
* `$methods` - HTTP methods to respond for (usually GET & POST), but can also respond to `PUT`, `PATCH` and `DELETE`
* `$name` - route name, can be used for internal communication when reffering to the specified route

# Examples

All the classes and routes below are fictional and 

## Simple Route

* No parameters
* Only one middleware
* Responding exclusively to `GET` HTTP method


```php
$app->route(
    '/product/list',  // route path
    Frontend\MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods, only get
    'shop.product' // route name
);
```

## Route with parameter

* One (required) parameter
  * `categoryId` is required, if no other route is defined a 404 message will be returned.
* Only one middleware
* Responding exclusively to `GET` HTTP method

```php
$app->route(
    '/product/list/{categoryId}',  // route path
    Frontend\MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods
    'shop.list-category' // route name
);
```

## Route with optional parameter

* One (optional) parameter
  * if `categoryId` is defined show products with given category id, else show all product 
* Only one middleware
* Responding exclusively to `GET` HTTP method

```php
$app->route(
    '/product/list[/{categoryId}]',  // route path
    Frontend\MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods
    'shop.list-category-optional' // route name
);
```

## Route with required parameter and optional parameter

* two parameters
  * `categoryId` is required, if no other route is defined a 404 message will be returned.
  * `sort` type is optional, sort by relevance
* Only one middleware
* Responding exclusively to `GET` HTTP method

```php
$app->route(
    '/product/list/{categoryId}/[/{sort}]',  // route path
    Frontend\MyModule\Controller\ProductListController::class, // only one middleware
    ['GET'], // HTTP methods
    'shop.list-category-optional' // route name
);
```

## Route allowing GET & POST and conditional paramter with multiple middleware 

 * one parameter
   * `productId` is required and must be a digit (`\d+` is the regex pattern for digits)
 * two middleware
 * responds to GET & POST
   * `GET` - display the product data (before editing)
   * `POST` - change the product data


```php
$app->route(
    '/product/edit/{productId:\d+}',  // route path
    [
        Frontend\MyModule\Validator\ProductDataValidator::class,
        Frontend\MyModule\Controller\ProductListController::class,
    ], // only one middleware
    ['GET', 'POST'], // HTTP methods
    'shop.list-category-optional' // route name
);
```
