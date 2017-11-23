# Testing

- [Testing](#testing)
    - [Types of testing](#types-of-testing)
    - [Getting started](#getting-started)
        - [Setup](#setup)
        - [Example](#example)
    - [Test driven development](#test-driven-development)
    - [Recap](#recap)

Testing in DotKernel 3 is incredible easy, as every necessary tool has been included in the standard bundle.

> Testing your application makes sure that everything works as it is supposed to, before the code is being deployed to a staging or production server.

## Types of testing

There's several types of tests, and a multitude of ways to write them. One of the most recognized ways is called TDD (Test driven Development), where you actually write the tests before the code.

- Unit tests
  - Unit testing is meant to only test a single "unit" of the code in each test. The "unit" could be a single function of an object or module. These tests are often small and secluded from any dependencies, which means they're quick to both write and run.
- Integration tests
  - Integration tests focus on piecing multiple functions together to make sure they integrate well into the main application and everything works as expected. This could be that the User Module is integrated correctly into your application.
- Acceptace tests
  - The last level of testing. This level of testing is responsible for make sure the entire app works as intended and can be accepted by the client. This is where you validate that the correct text is displayed on the pages, and that the pages exists.

## Getting started

Everything is setup in DotKernel, all you have to do is find the place where you want to write your tests, we recommend `/test/`, and create this directory in your project.

As you can see in `composer.json`, there's already an entry under `autoload-dev`, this assumes that you chose the `/test/` root directory for your tests, if you didn't please configure this path. The object-key is the namespace that the test needs to be under. All tests in `/test/App/` needs to have the root namespace `FrontendTest\App`

Create a directory for each type of test, `unit`, `integration` and `acceptance`, and start by creating a unit-test.

### Setup

A MYSQL database or the like should never be used in testing, as it's slow. Using an in-memory SQLite database will make your tests way more performant.

### Example

A unit test should look something like this:

```php
<?php

namespace FrontendTest\App\Unit;

use PHPUnit\Framework\TestCase;

class UserTest extends TestCase {

    /** @test **/
    public function newUsersPasswordIsHashed()
    {
        // Validate that new users passwords aren't stored
        // in plaintext

        // We assume that we create a new user in the $user variable
        // and that the password was set to "Password"

        // If this fails, the password was not hashed
        // More rigorous testing can be done by also making sure it was hashed
        // with the correct algorithm.

        $this->assetTrue($user->password !== 'Password');

    }
}
```

One file can contain multiple tests, just don't overdo it, it's important to keep an overview.

Any method with `/** @test **/` above it is considered a test, and any methods without that tag, is considered helper methods inside the test class.

## Test driven development

TDD is a way of developing that focuses heavily on always having tests for every bit of your code. It makes you write the test, before witing the code, and that way think about what exactly the code is gonna do before you write it, because you have to write a test to assert that it does exactly that.
At first it's a bit slower than "normal" development (Domain Driven Development etc.), but as you get better, it can be just as fast.

If you want to write rigorous tests for your application via TDD, we suggest picking up a book or video-course on the subject and start practicing it on your next project.

## Recap

Writing tests for your application can catch a lot of bugs in development, but it's of course no guarantee that all of them will be caught.

> More tests also doesn't mean more bugs caught, it's all about how well the tests are written.

Tests become especially important when you have to change something, because they make sure that whatever you changed didn't affect the rest of the app, and that everything still works as intended
