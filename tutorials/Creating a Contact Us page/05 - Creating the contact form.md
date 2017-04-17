# Creating the contact form

To keep to our convention, we'll create one fieldset per entity class. A fieldset is a collection of form elements, or group of elements, even other nested fieldsets for a hierarchical structure of entities.

In our case, we we'll need just one fieldset, that matches one to one the entity class.
Lets create a new folder in `src/App/src` and call it `Form`. We'll keep here all our fieldsets and forms. You can go further and create a Fieldset directory instead, to keep things even more organized, just make sure to use the right namespace when defining the class.

Create a new php class file in the newly created directory. The class needs to extend the `Zend\Form\Fieldset` class. It also implements the `Zend\InputFilter\InputFilterProviderInterface` so it can hint to a default input filter specification, which will be set on the form's input filter on form instantiation. Again, this must happen through the form manager service to happen automatically.
##### UserMessageFieldset.php
```php
declare(strict_types=1);

namespace Frontend\App\Form;

use Zend\Form\Fieldset;
use Zend\InputFilter\InputFilterProviderInterface;

class UserMessageFieldset extends Fieldset implements InputFilterProviderInterface
{
    public function __construct()
    {
        parent::__construct('userMessage');
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
                'placeholder' => 'Message...'
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

In the same directory create the contact form, that uses the above fieldset.
```php
declare(strict_types=1);

namespace Frontend\App\Form;

use Zend\Form\Form;

class ContactForm extends Form
{
    public function __construct($name = 'contactForm', array $options = [])
    {
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
                        'site_key' => 'recaptcha site key',
                        'secret_key' => 'recaptcha secret key',
                        'theme' => 'light',
                    ]
                ],
            ]
        ], ['priority' => -100]);
    }
}
```

The forms are created, the last things to do is register the form and fieldset in the form element manager and display the form in the contact controller.

### Register the forms in the service manager
Forms and fieldsets need to be registered in the form manager. This can be done using the configuration. We'll use the `ConfigProvider` class of the `App` module to register them.

The form related configuration must be placed under the `dot_form` configuration key. The form manager configuration key is `form_manager` under the dot_form key.

##### src/App/src/ConfigProvider.php
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
                    ContactForm::class => InvokableFactory::class,
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

At this point, you can access the form object from within the contact controller through the provided form manager controller plugin. You can use the FQN or the alias that was defined, as we recommend.

