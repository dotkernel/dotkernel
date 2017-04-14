# Creating and setting up a contact form

For form creation, input filtering and validation we'll use the following packages
* [dot-form](https://github.com/dotkernel/dot-form)
* [dot-inputfilter](https://github.com/dotkernel/dot-inputfilter)
    * [dot-filter](https://github.com/dotkernel/dot-filter)
    * [dot-validator](https://github/dotkernel/dot-validator)

The dot-filter and dot-validator are direct dependencies to dot-inputfilter. These DotKernel packages are just extension of the respective zend framework packages. Apart from defining some custom validators, it overwrites the factories and abstract factories of the original zend packages, in order to customize the configuration keys.

You should check out the zend documentation for the following packages for more details.
* [zend-form](https://github.com/zendframework/zend-form)
* [zend-filter](https://github.com/zendframework/zend-filter)
* [zend-validator](https://github.com/zendframework/zend-validator)
* [zend-inputfilter](https://github.com/zendframework/zend-inputfilter)

For now, keep in mind that working with forms, validation and filtering of input data is actually working with zend framework. A lot of DotKernel packages are based on zend framework packages. Installing the DotKernel package in this case will install the respective zend framework package that is based on.

## Creating forms

Zend Framework's forms package allows you to create forms in multiple ways like: pure configuration, a mix of classes and configuration or pure programmatic style. You can also instantiate forms directly or use the provided form element manager to create the forms or form elements.

We don't force a specific way to create forms, but in order to keep things under control, we recommend using one or two methods. DotKernel web apps are using the convention of creating all forms through the form manager. This way we can use the `init()` method of the form to initialize the form with elements, and the form manager will call that method automatically. This has also the benefit of easy access to the forms inside DotKernel controller forms plugin.

This is a large subject to treat here. We'll leave you with the independent DotKernel packages documentation and Zend framework documentation. Just visit the links for more on the subject. Hopefully, the example that will give in this tutorial will be enough to get you started with zend forms and the way we use them inside dotkernel.

## Creating the contact form class

Create a new folder `src/App/src/Form` if not already there. Here, create a new php class file

##### ContactForm.php
```php
<?php

declare(strict_types=1);

namespace Frontend\App\Form;

use Zend\Form\Form;

class ContactForm extends Form
{
    /**
     * ContactForm constructor.
     * @param string $name
     * @param array $options
     */
    public function __construct($name = 'contactForm', array $options = [])
    {
        parent::__construct($name, $options);
    }

    public function init()
    {
        // TODO: add elements
    }
}

```
