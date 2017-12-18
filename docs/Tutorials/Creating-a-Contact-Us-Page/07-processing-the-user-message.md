# Processing the user message

- [Processing the user message](#processing-the-user-message)
    - [The POST processing outlined](#the-post-processing-outlined)
        - [ContactController.php](#contactcontrollerphp)
    - [Handling invalid input data](#handling-invalid-input-data)
    - [Handling valid data](#handling-valid-data)
        - [Creating a UserMessageServiceInterface](#creating-a-usermessageserviceinterface)
            - [UserMessageServiceInterface.php](#usermessageserviceinterfacephp)
        - [Inject a UserMessageServiceInterface into the ContactController](#inject-a-usermessageserviceinterface-into-the-contactcontroller)
    - [Previous | Next](#previous-next)
        - [Tutorial index](#tutorial-index)
        - [All tutorials](#all-tutorials)


The next step is to process the POST request, that includes getting the data, validating it and saving it to the database or display error messages in case of invalid data. We like to process the POST request in the same controller action as the template response. In case of error, we also like to use the PRG (POST-REDIRECT-GET) pattern. This means that after a validation error occurs the users will be redirected back to the same page, hence the GET, to retrieve the same page. The errors are sent between requests by using session messages. Also the forms state can be saved in session in order to display the errors. DotKernel allows you to save a form-state and access this in the template, to check for errors. This PRG pattern is brilliant at eliminating the annoying confirm form re-submit alert and the possibility of double submitting the form.

## The POST processing outlined

Start with the following piece of code, to outline the basic request processing workflow. Your `indexAction` method should look like this

### ContactController.php

```php
public function indexAction(): ResponseInterface
{
    $form = $this->forms('ContactForm');
    $request = $this->getRequest();

    if ($request->getMethod() === RequestMethodInterface::METHOD_POST) {
        $data = $request->getParsedBody();

        $form->setData($data);
        if ($form->isValid()) {
            // save to backend, etc.
        } else {
            // set error messages, PRG redirect
        }
    }

    return new HtmlResponse($this->template('app::contact', [
        'form' => $form
    ]));
}
```

What is happening here

- We get the current request object, which is set on the controller, using the `getRequest()` method
- We check if it is a POST request. All the processing happens in this conditional statement.
- We fetch the request POST data using the `getParsedBody()` method of the request object which retrieves a PHP associative array
- We set this data on the form, in order to be validated
- Following, we have to code paths: one for valid data and the other one in case the input contains invalid data.

This is the basic structure of a POST request processing that you will find using over and over again in your controllers.

## Handling invalid input data

We'll start with the error handling, as it is easier and pretty much repetetive. When calling the form's `isValid()` method, the data that was set from the request is passed to the underlying form's input filter.

There is more than one way to set the input filter on the form. You can define an input filter class on its own an set it on the form by using a factory or a delegator factory. In our case, if you remember, we have implemented the `InputFilterProviderInterface` interface on the fieldset. In this case, if no other input filters are set on the form, the form will have a default input filter and at the key specified by the fieldset's name (`userMessage` in our case), we'll have the input filter specified by the fieldset. So the input filters will be nested. Also, some form elements never provide default filters and validators, such as captcha and csrf, that's why we don't define anything for those elements.

If you have followed our tutorial, this input filter setup is handled automatically, you just have to call the `isValid()` method.

If the form data is not valid, `false` will be returned, and the error messages will be set on the form objects respective element.

> ContactController.php

```php
public function indexAction(): ResponseInterface
{
    // ...

    if ($form->isValid()) {
        // save user message etc.
    } else {
        $this->messenger()->addError($this->forms()->getMessages($form));
        $this->forms()->saveState($form);
        return new RedirectResponse($request->getUri(), 303);
    }

    //...
}
```

What is happening here:

- In case of invalid form data, we use the controller `flash messenger plugin` to set add the error messages to the session, so we can display them on the next request. The form error messages can be extracted in a string list format using the `forms()->getMessages($form)` plugin method.
- We save the form state in the session using the `forms` plugin `saveState` method, passing the form object as a parameter.
- We return a redirect response to the same page (PRG pattern). The status code 303 is more appropriate for PRG redirects.

If you have put a `{{ messagesPartial('partial::alerts') }}` in your twig template, the error messages will be displayed for you automatically in that place. Because the form's state was saved, the form inputs that have errors will be outlined with red color(if you use our default provided partial templates) and the inputs will retain their values.

You can test if this is working by going to the contact page in your browser and trying to submit an empty form. You should get a list of error messages according to our validation setup.

## Handling valid data

W'll focus on saving the user message to the database. We've used our [dot-mapper](https://github.com/dotkernel/dot-mapper) package for the database communication, but you may use whatever you feel works for you.

> You should never put business logic directly in the controller.

The act of saving the data should be wrapped in a class that's outside the scope of the controller. We like to call these classes, `Service` classes. These classes will be responsible for all actions that can be done on a specific Entity.

### Creating a UserMessageServiceInterface

We like to create interfaces for our public service classes and define the allowed methods on the UserMessageEntity.

You should create another folder in the `App` module, call it `Service`, and add the following interface.

#### UserMessageServiceInterface.php

```php
declare(strict_types=1);

namespace Frontend\App\Service;

use Frontend\App\Entity\UserMessageEntity;

interface UserMessageServiceInterface
{
    /**
     * @param UserMessageEntity $message
     * @param array $options
     */
    public function save(UserMessageEntity $message, array $options = []);
}
```

We have only defined the save method for now, as it is the only method we need to finish our contact page. In this class you should put all your UserMessageEntity-related bussiness logic, so define it according to your needs.

We'll leave the actual implementation for the next lesson, but we can already put the code in place, and we can do this because we have the interface. This is another benefit of using interfaces, you can code before having the implementation, as we will use the interface as the type-hint in our controller.

### Inject a UserMessageServiceInterface into the ContactController

> ContactController.php

```php
//...
class ContactController extends AbstractActionController
{
    /** @var  UserMessageServiceInterface */
    protected $userMessageService;

    /**
     * ContactController constructor.
     * @param UserMessageServiceInterface $userMessageService
     *
     * @Inject({UserMessageServiceInterface::class})
     */
    public function __construct(UserMessageServiceInterface $userMessageService)
    {
        $this->userMessageService = $userMessageService;
    }
}
```

We rely on our annotation services package again, in order to inject our first controller dependency. Otherwise you should have created a controller factory in order to inject it. We use `UserMessageServiceInterface::class` as our dependency name because that's the FQCN of the Interface that we will be registering.

Be sure to define what the UserMessageService's `save` method should return on success or error.

We like the idea of creating a ServiceResultInterface which wrapps the service's response (be it error or not), and contains all the necessary data. We actually use this in our dot-user package. This way you have a consistent return type which you can even type-hint in PHP 7.
Mixed results are possible in php if you don't type-hint the return type. This way you can return the saved Entity for example, which could have its `id` filled in or false if there was an error, or an array that contains both data and errors, it's up to you.

For this lesson we'll use the mixed method, which should be good in our case. We'll leave the result class implementation up to you if you feel it would be better. Because the `save` method will simply be a proxy to the underlying mapper's save method, we can simply pass on the mappers return values. If it was a successful write, it will return the Entity object, and will return false if it encountered any errors.

Lets add more code to the `indexAction` method

> ContactController.php

```php
public function indexAction(): ResponseInterface
{
    //...
    if ($request->getMethod() === RequestMethodInterface::METHOD_POST) {
       //...
        if ($form->isValid()) {
            $message = $form->getData();
            $result = $this->userMessageService->save($message);
            if ($result) {
                return new RedirectResponse($this->url('contact', ['action' => 'thank-you']));
            } else {
                $this->messenger()->addError('Error saving message. Please try again');
                return new RedirectResponse($request->getUri(), 303);
            }
        } else {
            //...
        }
    }
    //...
}
```

Here is what happens:

- If the form is valid, we get the validated data back. In this case, it is a hydrated UserMessageEntity, because we have set that object prototype and hydrator in the UserMessageFieldset, AND we have set this fieldset as the base fieldset for the form. This way, when using form's `getData`, the validated array will be automatically hydrated into an object.
- We can directly send this message object to our service class to save it to the backend, as we know the data is filtered and validated.
- We check the service's result for further validation. If a `false` value was returned, there was an error while saving the Entity.
- Based on the result, we redirect to a "thank you page", or back the the contact page with a descriptive error message.

This is the code needed in the controller, We don't have the service implementation yet, and you'll get and error if you try to run the code now. We'll cover the service implementation in the next lesson.

## Previous | Next

**[Prev: Display the contact form](06-display-the-contact-form.md)** | **[Next: Implementing the service class](08-implementing-the-service-class.md)**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
