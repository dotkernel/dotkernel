# Planning the contact form implementation

- [Planning the contact form implementation](#planning-the-contact-form-implementation)
    - [Creating forms](#creating-forms)
    - [Plan ahead](#plan-ahead)
    - [Previous | Next](#previous-next)
        - [Tutorial index](#tutorial-index)
        - [All tutorials](#all-tutorials)

For form creation, input filtering and validation, we'll use the following packages

- [dot-form](https://github.com/dotkernel/dot-form)
- [dot-inputfilter](https://github.com/dotkernel/dot-inputfilter)
  - [dot-filter](https://github.com/dotkernel/dot-filter)
  - [dot-validator](https://github.com/dotkernel/dot-validator)

The dot-filter and dot-validator are direct dependencies to dot-inputfilter. These DotKernel packages are just extension of the respective zend framework packages. Apart from defining some custom validators, they overwrite the factories and abstract factories of the original zend packages in order to customize the configuration keys.

You should check out the zend documentation for the following packages for more details.
- [zend-form](https://github.com/zendframework/zend-form)
- [zend-filter](https://github.com/zendframework/zend-filter)
- [zend-validator](https://github.com/zendframework/zend-validator)
- [zend-inputfilter](https://github.com/zendframework/zend-inputfilter)

For now, keep in mind that working with forms, validation and filtering of input data is actually working with zend framework. A lot of DotKernel packages are based on zend framework packages. Installing the DotKernel package in this case will install the respective zend framework package it is based on.

## Creating forms

Zend Framework's forms package allows you to create forms in multiple ways like: pure configuration, a mix of classes and configuration or pure programmatic style. You can also instantiate forms directly or use the provided form element manager to create the forms or form elements.

We don't force a specific way to create forms, but in order to keep things under control, we recommend using one of two methods. DotKernel web apps are using the convention of creating all forms through the [FormManager](https://github.com/dotkernel/dot-form). This way we can use the `init()` method of the form to initialize the form with elements, and the FormManager will call that method automatically. This has also the benefit of easy access to the forms, inside DotKernel controller, through the `forms` plugin.

## Plan ahead

Before the actual implementation, you should establish the requirements and make a plan on how to implement it. In order to cover as much as we can, in regards to DotKernel and Zend Framework packages, we will complicate things a bit.

We took the following decisions

- Save the user message in the backend - this way we will cover the [dot-mapper](https://github.com/dotkernel/dot-mapper) package
- The user message will be represented in code by an entity class. Lets call it `UserMessageEntity`.
- Next, you should think about the form. Our convention is to create one `Zend\Form\Fieldset` per entity class. This allows you to reuse the fieldset in different forms. A fieldset is a collection of form elements. The entity fieldset should match the entity properties and the entity properties should match the database columns. We recommend you to stay on this convention to make your life easier.
- Create the actual form to be displayed. This will use the above fieldset as the base fieldset. It can also add other form elements, specific to the form(create form, edit form etc.). One element that is suitable to add in the form, not in the fieldset, is the submit button, csrf token, captcha etc.
- An input filter for input validation and filtering. This is used by the form object to validate incoming data. We recommend using one of the following two methods: create a separate input filter class, that you can set as the form's input filter on form creation(in the form factory for example). This is useful if you need to reuse the input filter. Or you can make your fieldset implement the `InputFilterProviderInterface` interface and configure the input filter in the fieldset itself. This act as a default input filter, you can still set another input filter at runtime if needed. Throughout DotKernel frontend application, we mainly use the second approach, as it is clearer and more manageable.

Lets proceed to implement this step by step, in the logical order.

## Previous | Next

**[Prev: Creating the contact controller](02-creating-the-contact-controller.md)** | **[Next: Backend setup, entities and hydrators](04-backend-setup-entity-and-hydrators.md)**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
