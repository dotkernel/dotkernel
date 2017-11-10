# Creating the contact form

To keep to our convention, we'll create one fieldset per entity class. A fieldset is a collection of form elements, or group of elements, even other nested fieldsets for a hierarchical structure of entities.

In our case, we we'll need just a fieldset that matches one to one the entity class.
Lets create a new folder in `src/App/src` and call it `Form`. We'll keep all our fieldsets and forms here. You can go further and create a Fieldset directory too if you wish to separate the two for a more organised feel.

Create a new php class file in the newly created directory. The class needs to extend the `Zend\Form\Fieldset` class. It also implements the `Zend\InputFilter\InputFilterProviderInterface` so it can hint to a default input filter specification, which will be set on the form's input filter on form instantiation. Again, this must happen through the form manager service to happen automatically.

Another thing worth noticing is that we set the entity and hydrator classes in the fieldset. When validating and getting the data back from the form, the form will use the base fieldsets hydrator to hydrate an entity class instead of returning a plain array.

##### UserMessageFieldset.php

```php
declare(strict_types=1);

namespace Frontend\App\Form;

use Zend\Form\Fieldset;
use Zend\InputFilter\InputFilterProviderInterface;
use Dot\Hydrator\ClassMethodsCamelCase;
use Frontend\App\Entity\UserMessageEntity;

class UserMessageFieldset extends Fieldset implements InputFilterProviderInterface
{
    public function __construct()
    {
        parent::__construct('userMessage');

        $this->setObject(new UserMessageEntity());
        $this->setHydrator(new ClassMethodsCamelCase());
    }

    public function init()
    {
        $this->add([
            'name' => 'email',
            'type' => 'text',
            'options' => [
                'label' => 'E-mail'
            ],
            'attributes' => [
                'placeholder' => 'E-mail address...'
            ]
        ]);

        $this->add([
            'name' => 'name',
            'type' => 'text',
            'options' => [
                'label' => 'Name'
            ],
            'attributes' => [
                'placeholder' => 'Your name...'
            ]
        ]);

        $this->add([
            'name' => 'subject',
            'type' => 'text',
            'options' => [
                'label' => 'Subject'
            ],
            'attributes' => [
                'placeholder' => 'Subject...'
            ]
        ]);

        $this->add([
            'name' => 'message',
            'type' => 'textarea',
            'options' => [
                'label' => 'Message'
            ],
            'attributes' => [
                'id' => 'userMessage_textarea',
                'placeholder' => 'Message...',
                'rows' => 5,
            ]
        ]);
    }

    public function getInputFilterSpecification()
    {
        return [
            'email' => [
                'filters' => [
                    ['name' => 'StringTrim']
                ],
                'validators' => [
                    [
                        'name' => 'NotEmpty',
                        'break_chain_on_failure' => true,
                        'options' => [
                            'message' => '<b>E-mail</b> address is required and cannot be empty',
                        ]
                    ],
                    [
                        'name' => 'EmailAddress',
                        'options' => [
                            'message' => '<b>E-mail</b> address is invalid',
                        ]
                    ],
                ],
            ],
            'name' => [
                'filters' => [
                    ['name' => 'StringTrim']
                ],
                'validators' => [
                    [
                        'name' => 'NotEmpty',
                        'break_chain_on_failure' => true,
                        'options' => [
                            'message' => '<b>Name</b> is required and cannot be empty',
                        ]
                    ],
                    [
                        'name' => 'StringLength',
                        'options' => [
                            'max' => 500
                        ]
                    ]
                ],
            ],
            'subject' => [
                'filters' => [
                    ['name' => 'StringTrim']
                ],
                'validators' => [
                    [
                        'name' => 'NotEmpty',
                        'break_chain_on_failure' => true,
                        'options' => [
                            'message' => '<b>Subject</b> is required and cannot be empty',
                        ]
                    ],
                    [
                        'name' => 'StringLength',
                        'options' => [
                            'max' => 500
                        ]
                    ]
                ],
            ],
            'message' => [
                'filters' => [],
                'validators' => [
                    [
                        'name' => 'NotEmpty',
                        'break_chain_on_failure' => true,
                        'options' => [
                            'message' => '<b>Message</b> is required and cannot be empty',
                        ]
                    ],
                    [
                        'name' => 'StringLength',
                        'options' => [
                            'max' => 1000
                        ]
                    ]
                ],
            ],
        ];
    }
}
```

In the `Form` directory, create the contact form that uses the above fieldset. Since we use a recaptcha element on the form, we will inject the site key and secret key, from the configuration.

```php
declare(strict_types=1);

namespace Frontend\App\Form;

use Zend\Form\Form;

class ContactForm extends Form
{
    /** @var  array */
    protected $recaptchaOptions;

    /**
     * ContactForm constructor.
     * @param array $recaptchaOptions
     * @param string $name
     * @param array $options
     */
    public function __construct(array $recaptchaOptions, $name = 'contactForm', array $options = [])
    {
        $this->recaptchaOptions = $recaptchaOptions;
        parent::__construct($name, $options);
    }

    public function init()
    {
        $this->add([
            'type' => 'UserMessageFieldset',
            'options' => [
                'use_as_base_fieldset' => true,
            ]
        ]);

        $this->add([
            'type' => 'Csrf',
            'name' => 'contact_csrf',
            'options' => [
                'csrf_options' => [
                    'timeout' => 3600,
                    'message' => 'The form used to make the request has expired and was refreshed. Please try again.'
                ]
            ]
        ]);

        $this->add([
            'name' => 'captcha',
            'type' => 'Captcha',
            'options' => [
                'label' => 'Please verify you are human',
                'captcha' => [
                    'class' => 'recaptcha',
                    'options' => [
                        'site_key' => $this->recaptchaOptions['site_key'],
                        'secret_key' => $this->recaptchaOptions['secret_key'],
                        'theme' => 'light',
                    ]
                ],
            ]
        ], ['priority' => -100]);

        $this->add([
            'name' => 'submit',
            'type' => 'submit',
            'attributes' => [
                'type' => 'submit',
                'value' => 'Send message'
            ]
        ], ['priority' => -105]);
    }
}
```

In the `local.php` file, define the recaptcha options. These are already defined if you use the dot-user module, but as those are somewhat customary to the user module, we'll duplicate them in a global config key, so anytime a recaptcha element will be needed, we will take the keys from there.

##### local.php

```php
return [
    // ...

    'recaptcha' => [
        'site_key' => 'YOUR SITE KEY',
        'secret_key' => 'YOUR SECRET KEY'
    ]
];
```

We need to inject a dependency in the form class, and will need a factory class for this. We can't use the annotation service in this case, because form creation is handled by the form manager, which is a specialized service manager acting as a sub-container, and the annotation factory is defined only for the parent service manager. No problem, we don't need to get rid of factories altogether.

Create a new php class file in `src/App/src/Factory`.

##### ContactFormFactory.php

```php
declare(strict_types=1);

namespace Frontend\App\Factory;

use Frontend\App\Form\ContactForm;
use Psr\Container\ContainerInterface;

class ContactFormFactory
{
    /**
     * @param ContainerInterface $container
     * @return ContactForm
     */
    public function __invoke(ContainerInterface $container)
    {
        $recaptchaOptions = $container->get('config')['recaptcha'];
        return new ContactForm($recaptchaOptions);
    }
}
```

After the form has been created, we just need to register the form and fieldset in the form element manager and display the form in the `ContactController`.

### Register the form in the service manager

Forms and fieldsets need to be registered in the form manager. This can be done using the configuration. We'll use the `ConfigProvider` class of the `App` module to register them.

The form related configuration must be placed under the `dot_form` configuration key. The form manager configuration key is `form_manager` under the dot_form key.

#### src/App/src/ConfigProvider.php

```php
namespace Frontend\App;

use Frontend\App\Form\UserMessageFieldset;
use Frontend\App\Form\ContactForm;

class ConfigProvider
{
    public function __invoke()
    {
        return [
            //...
            'dot_form' => $this->getForms(),
            //...
        ];
    }

    public function getForms()
    {
        return [
            'form_manager' => [
                'factories' => [
                    //...
                    UserMessageFieldset::class => InvokableFactory::class,
                    ContactForm::class => ContactFormFactory::class,
                ],
                'aliases' => [
                    //...
                    'UserMessageFieldset' => UserMessageFieldset::class,
                    'ContactForm' => ContactForm::class,
                ],
            ]
        ];
    }
}
```

At this point, you can access the form object from within the contact controller through the provided form manager controller plugin. You can use the FQCN or the alias that was defined to access the form. We recommend using aliases in this case.

## Previous | Nex

**[Prev: Backend setup, entities and hydrators](04-backend-setup-entity-and-hydrators.md)** | **[Next: Display the contact form](06-display-the-contact-form.md)**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
