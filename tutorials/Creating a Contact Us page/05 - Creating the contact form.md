# Creating the contact form

To keep to our convention, we'll create one fieldset per entity class. A fieldset is a collection of form elements, or group of elements, even other nested fieldsets for a hierarchical structure of entities.

In our case, we we'll need just one fieldset, that matches one to one the entity class.
Lets create a new folder in `src/App/src` and call it `Form`. We'll keep here all our fieldsets and forms. You can go further and create a Fieldset directory instead, to keep things even more organized, just make sure to use the right namespace when defining the class.

Create a new php class file in the newly created directory. The class needs to extend the `Zend\Form\Fieldset` class.
##### UserMessageFieldset.php
```php
declare(strict_types=1);

namespace Frontend\App\Form;

use Zend\Form\Fieldset;

class UserMessageFieldset extends Fieldset
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
}
```

