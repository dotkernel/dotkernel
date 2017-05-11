# Processing the user message

The next step is to process the POST request, that includes getting the data, validating it and save it to the database or display error messages in case of invalid data. We like to process the POST request in the same controller action. In case of error, we also like to use the PRG(POST-REDIRECT-GET) pattern in case of errors. That means that after a validation error occurs the users will be redirected back to the same page, hence the GET, to retrieve the same page. The errors are sent between requests by using session messages. Also the forms state can be saved in session in order to display the errors. This could be a tedious pattern to implement for all your actions where this is necesary, so DotKernel setup is ready for this pattern which make this a piece of cake. This PRG pattern is good to eliminate the annoying confirm form re-submit and the possible double submit of the form.

## The POST processing outlined

Start with the following piece of code, to outline the basic request processing workflow. Your `indexAction` method should look like this

##### ContactController.php
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
- we get the current request object, which is set on the controller, using the `getRequest()` method
- we check if it is a POST request. All the processing happens under this conditional statement.
- we fetch the request POST data using the `getParsedBody()` method of the request object which retrieves a PHP associative array
- we set this data on the form, in order to be validated
- following, we have to code paths: one for valid data and the other one in case the input contains invalid data.

This is the basic structure of a POST request processing that you will find using over and over again in your controllers.

## Handling invalid input data

We'll start with the error handling, as it is easier and pretty much repetetive. When calling the form's `isValid()` method, the data that was set from the request is passed to the underlying form's input filter. You may wonder where this input filter was created.

Well, there is more than one way to set the input filter on the form. You can define an input filter class on its own an set it on the form by using a factory or a delegator factory. In our case, if you remember, we have implemented the `InputFilterProviderInterface` interface on the fieldset. In this case, if no other input filters are set on the form, the form will have a default input filter and at the key specified by the fieldset's name(`userMessage` in our case) we'll have the input filter specified by the fieldset. So the input filters will be nested. Also, some form elements arealy provide default filters and validators(captcha, csrf etc.) that's why we don't define anything for those elements.

If you have followed our tutorial, this input filter setup is handled automatically, you just have to call the `isValid()` method.

If the form data is not valid, `false` will be returned, and some error messages will be set on the form object for each element, messages that we can display to the user. In order to do that all you have to do is as follows

##### ContactController.php
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
- in case of invalid form data, we use the controller flash messenger plugin to set session error messages that will be displayed on the next request. The form error messages can be extracted in a string list format using the `forms()->getMessages($form)` plugin method.
- we save the form state in the session using the `forms` plugin `saveState` method, passing the form object as a parameter.
- we return a redirect response to the same page(hence the PRG pattern). The status code 303 is more appropriate for PRG redirects.

This is all you have to do, and you will find yourself using this pattern alot in your controllers. 

If you have put a `{{ messagesPartial('partial::alerts') }}` in your twig template, the error messages will be displayed for you automatically in that place. Also, because the form's state was saved, the form inputs that have errors will be outlined with red color(if you use our default provided partial templates) and the inputs will retain their values.

You can test if this is working by going now to the contact page in your browser and trying to send the form empty. You should get a list of error messages according to our validation setup.

## Handling valid data

This is specific to your project. You will usually save the data to the backend, send e-mails, trigger events, logging and so on.
For now, we'll focus on saving the user message to the database. We'll cover here our [dot-mapper](https://github.com/dotkernel/dot-mapper) package for backend communication, but you may use whatever you feel it is better for you.

Anyway, lets establish what should we do for this. We'll tell you from the beginning that you should not put the bussiness logic directly in the controller. The saving process should be wrapped in a class. We like to call these classes `Service` classes. These classes will be responsible for all actions that can be done on a specific entity.

### Creating a UserMessageServiceInterface

You can probably get away with this and directly implement the service class, but we like to be good citizens and create interfaces for our public service classes to define the allowed methods on the user message entity.

You should create another folder in the `App` module, call it `Service`. Define here the following interface.
##### UserMessageServiceInterface.php
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

We have defined just the save method for now, as it is all that we need to finish our contact page. Other methods could be futher defined, to find one or a list of user messages, deletion and other more specific actions that can be applied on the user messages. This is the class in which you should put all the user messages bussiness logic, so define it according to your needs.

We'll leave the actual implementation for the next lesson, but we can already put the code in place, and we can do this because we have the interface. This is another benefit of using interfaces, you can code before having the implementation, as we will use the interface as the type-hint in our controller.

### Inject a UserMessageServiceInterface into the ContactController

##### ContactController.php
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

We rely on our annotation services package again, in order to inject our first controller dependency. Otherwise you should have had created a controller factory to inject it. We use `UserMessageServiceInterface::class` as our dependency name because we know beforehand that we will be registering the service in the container using the interface's FQN as the service name(as a good practice).

Another question you should ask is what should the UserMessageService `save` method return on success or error. 
Boolean values, mixed values, a specific result object?...
All can be used and are more or less good. We like the idea to create a service ResultInterface which wrapps the service's response(be it error or not) and contains all data necessary. We actually use this in our dot-user package. This way you have a consistent return type which you can event type-hint in php 7. 
Mixed results are possible in php if you don't type-hint the return type. This way you can return the saved entity for example, which could have its id filled in or false if there was an error, or an array that contains both data and errors, is up to you. 
The all boolean return type is not very good, as it doesn't tell much about the result.

For this lesson we'll use the mixed method, which should be good in our case. We'll leave the result class implementation up to you if you feel it would be better. As the `save` method will be just a proxy to the underlying mapper's save method, we can return directly what the mapper returns. That is, if it was a successful write, it will return the entity object that was saved having the generated id filled in. It will return false on any unexpected save error.

Lets add more code to the `indexAction` method
##### ContactController.php
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
- if form was valid, we get the validated data back. In this case, it will be a hydrated UserMessageEntity object because we have set that object prototype and hydrator in the UserMessageFieldset AND we have set this fieldset as the base fieldset of the form. This way, when using form's `getData`, the validated array will be automatically hydrated into an object. This can be disabled by a flag if needed, not in our case.
- next, we can directly send this message object to our service class to save to the backend, as we know the data is filtered and validated.
- we check the service's result for further validation. If a `false` value was returned, there was an unexpected save error for whatever reasons(it could be logged exactly later). Otherwise, the original entity is returned back with the id field filled in.
- based on the result, we redirect to a thank you page or back the the contact page with a generic error message.

This is the code needed in the controller, We don't have the service implementation yet, and you'll get and error if you try to run the code now. We'll cover the service implementation in the next lesson.

### [Prev: Display the contact form](https://github.com/dotkernel/dotkernel/blob/master/tutorials/creating-a-contact-us-page/06-display-the-contact-form.md) | [Next: Implementing the service class](https://github.com/dotkernel/dotkernel/blob/master/tutorials/creating-a-contact-us-page/08-implementing-the-service-class.md)
