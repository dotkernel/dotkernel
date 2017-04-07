# Closures and Callables
---

## Anonymous function
Anonymous functions, as the name suggests, are functions without a name. 
Anonymous functions are functions defined on spot, they can be stored in variables.

Anonymous functions are also called PHP Lambdas.

A Lambda is an anonymous function that can be assigned to a variable or passed to another function as an argument. 
If you are familiar with other programming languages like Javascript or Ruby, you will be very familiar with anonymous functions.

```php
<?php
$mySumFunction = function($a,$b){
    return $a+$b;
}
echo $mySumFunction(5,8); // will output "13"
```

## Closures
In PHP a closure is actually a Class used to represent anonymous functions.
Besides the methods listed here, this class also has an `__invoke()` method. 
This is for consistency with other classes that implement calling magic, as this method is not used for calling the function.


The `__invoke()` method allows objects to be called as functions.

```php
<?php

class MsgClass
{
    public function __invoke($name, $salutation='Hello')
    {
        return $salutation . ', ' . $name . '!';
    }
}

$msg = new MsgClass();
echo $msg('World'); // will output "Hello, World!'
```

### Closure Example
```php
<?php

// Create a user
$user = "Philip";
 
// Create a Closure
$greeting = function() use ($user) {
  echo "Hello $user";
};
 
// Greet the user
$greeting(); // Returns "Hello Philip"
```

[What are PHP Lambdas and Closures?](http://culttt.com/2013/03/25/what-are-php-lambdas-and-closures/)


## Callables
Callbacks can be denoted by `callable` type hint as of PHP 5.4. 
PHP documentation used callback type information for the same purpose

Some functions like `call_user_func()` or `usort()` accept user-defined callback functions as a parameter. Callback functions can not only be simple functions, but also object methods, including static class methods.

A callable can be:
* a `string` containing an existing function name (eg.: `strpos()`)
* a `string` containing a class method name (eg.: `MyClass::MyObject()`
* an anoymous function (eg.: `$mySumFunction`, or the actual declaration)
* a closure
* an invokable object 



Sources:
[What are PHP Lambdas and Closures?](http://culttt.com/2013/03/25/what-are-php-lambdas-and-closures/)
[PHP: Closure - Manual](http://php.net/manual/en/class.closure.php)
[PHP: Callbacks / Callables](http://php.net/manual/ro/language.pseudo-types.php#language.types.callback)