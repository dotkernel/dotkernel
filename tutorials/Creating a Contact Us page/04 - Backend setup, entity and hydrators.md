# Backend setup, entity and hydrators

First of all, we should establish the database structure. We'll store contact messages in one table, lets call it `user_message`. We need at least he following columns: id, email, name, subject, message, created. Go ahead and create the table. We'll store the email, because the the contact form is public, users won't need to authenticate in order to send a message.

## User message entity class

An entity class is a class that represents a model in our code. Others prefer to call it a model too. DotKernel uses the entity label to refer to a model. Entities should be modeled around the database. Usually an entity is models one row from a table, in the simplest case, but can also compose other entities, or have propeties that come from other tables.

In our case, we fall on the simple side of the problem. Our object is pretty simple. We'll use an entity class to model the database row without any relationships. So, lets create the entity class, based on the user_message table. Please make sure to name the properties the same way as the column names. This will make things easier, as you won't need to use mapping.

Create a new folder inside `src/App/src` and call it `Entity`, in case it does not already exists. We'll use this folder to store all our entity classes defined in this module.

In this folder, create the `UserMessageEntity` class
##### UserMessageEntity.php
```php
declare(strict_types=1);

namespace Frontend\App\Entity;

class UserMessageEntity
{
    /** @var  int */
    protected $id;

    /** @var  string */
    protected $email;

    /** @var  string */
    protected $name;

    /** @var  string */
    protected $subject;

    /** @var  string */
    protected $message;

    public function getId()
    {
        return $this->id;
    }

    public function setId($id)
    {
        $this->id = $id;
    }

    public function getEmail(): string
    {
        return $this->email ?? '';
    }

    public function setEmail(string $email)
    {
        $this->email = $email;
    }

    public function getName(): string
    {
        return $this->name ?? '';
    }

    public function setName(string $name)
    {
        $this->name = $name;
    }

    public function getSubject(): string
    {
        return $this->subject ?? '';
    }

    public function setSubject(string $subject)
    {
        $this->subject = $subject;
    }
    
    public function getMessage(): string
    {
        return $this->message ?? '';
    }

    public function setMessage(string $message)
    {
        $this->message = $message;
    }
}
```

Our convention, when creating the entity, is to declare its properties as protected and generate getter setters pair for each one. This may seem a lot, but we recommend using and IDE that supports getter/setter generation.

## Entity hydrators

DotKernel uses its [dot-hydrator](https://github.com/dotkernel/dot-hydrator) package to define hydrators, which is entirely based on zend framework's [zend-hydrator](https://github.com/zendframework/zend-hydrator). Please make sure to check both links for documentation and understand what an hydrator is.

Our dot-hydrator package is just an extension to the zend framework hydrator package. In addition it provides 2 custom hydrators that extends zend framework's `ClassMethods` hydrator. One is `ClassMethodsCamelCase` that we will use to hydrate our entity. Custom hydrators can be used where appropriate. If writing a custom hydrator, just make sure to register it in the hydrator manager service. See provided links on how to do this.

So what is a hydrator you may ask. An hydrator must implement zend's `HydratorInterface` which defines 2 methods: `extract($object)` and `hydrate($data, $prototype)`. An hydrator's role it to convert an object(entity) from object form to array form and vice-versa through the defined methods.

* when we want to save an entity to the database, we'll extract the data and send it to the database abstraction layer.
* when fetching an entity, it will come in array form which will be hydrated into an entity object.

Hydrators can be used on their own, but Zend Framework and consequently DotKernel provide hydrator integration with forms and fieldset, to automatically to the job of hydration. Also, DotKernel packages that deal with entity manipulation, often rely on configured hydrators, in order to seemlessly extract and hydrate objects.

Returning to our case, we said that for this simple case, and the way the entity class is defined, the `ClassMethodsCamelCase` is perfect for our needs. Keep in mind we'll use this. This hydrator takes the array keys and call the appropriate setters to hydrate the object.

Having these classes defined, we are ready to implement our contact fieldset and form. Go ahead to the next lesson.
