# Display the contact form

- [Display the contact form](#display-the-contact-form)
    - [ContactController.php](#contactcontrollerphp)
        - [contact.html.twig](#contacthtmltwig)
    - [Previous | Next](#previous-next)
        - [Tutorial index](#tutorial-index)
        - [All tutorials](#all-tutorials)

Go back to the contact controller class. All we have to do here is fetch a contact form object from the form manager and send it to the template for displaying.

DotKernel controllers have already access to the form manager through a plugin called `forms`. To access the form do the following:

## ContactController.php

```php
//....

public function indexAction(): ResponseInterface
{
    $form = $this->forms('ContactForm');
    return new HtmlResponse($this->template('app::contact', [
        'form' => $form
    ]));
}
```

At this point, the form object is accessible in the template, so let's display it in the interface. There are more than one option to do this, each one is acceptable, depending on your needs.

The more tedious way is to fetch each element by its name, configure and display it using the form view helpers provided by Zend Framework. DotKernel made them available in Twig too, with the same name. We'll not use this method here, but if you are curious, the Frontend application uses this method for some of the forms. It offers the greatest form display control.

DotKernel frontend application provides 2 helper templates to automate the form display. You can find them in `src/App/templates/partials`. The `form-display.html.twig` can be used for any form that needs to be displayed entirely(all its defined elements). It will display the elements in regards to the priority they were given.

The `form-display-validationgroup.html.twig` is a variation of the first one, this one takes the validationgroup into consideration too.

Which one to choose? If you don't define a validation group that defines which elements needs filtering and validation, use the first one, otherwise use the second one.

In our case, the first one is fine. In order to display the form, it should first be prepared, open the form tags and display the elements.

### contact.html.twig

```html
{ % extends '@layout/default.html.twig' %}

{ % block title %}Contact Us{ % endblock %}

{ % block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3 col-lg-6 col-lg-offset-3 no-padding forms">
                <h1>Contact Us</h1>
                <div class="form-content">
                    {{ messagesPartial('partial::alerts') }}

                    { % set dummy = form.prepare() %}
                    {{ form().openTag(form) | raw }}

                    { % include '@partial/form-display.html.twig' with {'form': form, 'showLabels': true} %}

                    {{ form().closeTag() | raw }}

                </div>
            </div>
        </div>
    </div>
{ % endblock %}
```

> Note: due to technical restrictions the `{` and `%` are separated in the documentation. Please remove the space between them in your code.

The `{{ messagesPartial('partial::alerts') }}` is not form related, it is a twig extension defined by DotKernel to simplify the flash messenger parsing. We put this here in order to display the form errors if any, or other types of messages above the form.

Check the contact page in the browser, it should be displayed now.
Next, we will be processing the POST data and saving it to the backend, if valid.

## Previous | Next

**[Prev: Creating the contact form](05-creating-the-contact-form.md)** | **[Next: Processing the user message](07-processing-the-user-message.md)**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
