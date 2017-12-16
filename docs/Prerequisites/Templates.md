# Templates

- [Templates](#templates)
    - [Twig](#twig)
        - [Demo](#demo)
        - [Partials](#partials)
    - [Displaying the template](#displaying-the-template)
    - [Usage](#usage)
    - [Examples](#examples)
        - [For Loop](#for-loop)
        - [Auto escaping](#auto-escaping)
        - [Including pure html](#including-pure-html)
        - [More](#more)

## Twig

DotKernel comes bundled with the [Twig](https://twig.symfony.com/) templating engine, which allows you to create blocks, loops, layouts and more in your HTML.

### Demo

Here you can see how a `contact us` template could be built.

```html
{ % extends '@layout/default.html.twig' %}

{ % block title %}Contact Us{ % endblock %}

{ % block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3 col-lg-6 col-lg-offset-3 no-padding forms">
                <h1>Contact Us</h1>
                <div class="form-content">
                    { { messagesPartial('partial::alerts') }}

                    { % set dummy = form.prepare() %}
                    { { form().openTag(form) | raw }}

                    { % include '@partial/form-display.html.twig' with {'form': form, 'showLabels': true} %}

                    { { form().closeTag() | raw }}

                </div>
            </div>
        </div>
    </div>
{ % endblock %}
```

1) First, we start by selecting the base-layout that we want to extend.
1) Then we say that in the base-layouts' `title` block, we want to write "Contact Us"
1) and lastly, we add the content of our specific page to the base-layouts `content` block.

The Twig files requires a special, `.twig` extension, which is appended after the `.html` ending, and makes the filename look like this: `contact-us.html.twig`.

Twig uses the `{ %` syntax to open and close blocks, see more about the tags in the [usage](#usage) section.

### Partials

In the example above, you see something called a partial, `include '@partial/form-display.html.twig'`.
A partial is some HTML code that you want to be able to include in many different templates.

> Imagine you had five pages that all needed the ability to show alerts, then you could create a partial and include it in those five pages only.

Here we're displaying our form through the form-display partial, and passing it two `variables`, the `form` variable, and the `showLabels` variable.

## Displaying the template

In the controller, you need to return the correct template, like so:

```php
public function indexAction(): ResponseInterface
{
    $form = $this->forms('ContactForm');
    return new HtmlResponse($this->template('app::contact', [
        'form' => $form
    ]));
}
```

## Usage

| Symbol      | Usage                                                               |
| ----------- | ------------------------------------------------------------------- |
| { %         | A programmatic block, used for PHP commands like loops and includes |
| { {         | Display a variable                                                  |
| { {$var`|e` | Display an escaped variable, the `|e` means escape                  |
| { #         | Comment in the code, which won't be in the html                     |

> Remove the space after the `{`, We have to put it there for technical reasons.

## Examples

### For Loop

```twig
{ % for user in users %}
    * { { user.name }}
{ % else %}
    No users have been found.
{ % endfor %}
```

### Auto escaping

```twig
{ % autoescape "html" %}
    { { var }}
    { { var|raw }}     {# var won't be escaped #}
    { { var|escape }}  {# var won't be doubled-escaped #}
{ % endautoescape %}
```

### Including pure html

```twig
{ { include('page.html') }}
```

### More

Checkout the [Twig documentation](https://twig.symfony.com/doc/2.x/) for more examples and documentation
