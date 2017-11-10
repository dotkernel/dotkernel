# Backend setup, Entity and hydrators

- [Backend setup, Entity and hydrators](#backend-setup-entity-and-hydrators)
    - [User message Entity class](#user-message-entity-class)
        - [UserMessageEntity.php](#usermessageentityphp)
    - [Entity hydrators](#entity-hydrators)
    - [Previous | Next](#previous-next)
        - [Tutorial index](#tutorial-index)
        - [All tutorials](#all-tutorials)

First of all, we should establish the database structure. We'll store contact messages in one table, lets call it `user_message`. We need at least the following columns: id, email, name, subject, message, created. Go ahead and create the table. We'll store the email, because the contact form is public, users won't need to authenticate in order to send a message.

They have the following definitions:

- id - Unsigned integer, autoincrement, primary key
- email - varchar(200)
- name - varchar(120)
- subject - varchar(120)
- message - text
- created - timestamp

## User message Entity class

An Entity class is a class that represents a model in our code. Others prefer to call it a Model class instead of Entity. DotKernel uses the Entity label to refer to a Model as a convention. Entities should be modeled around the database. Usually an Entity models one row from a table, in the simplest case, but can also include other Entities, or have propeties that come from other tables or providers.

Our object is pretty simple, we'll use an Entity class to model the database row without any relationships. Lets create the Entity class, based on the `user_message` table. Please make sure to name the properties the same way as the column names. This will make things easier, as you won't need to do any mapping.

Create a new folder inside `src/App/src` and call it `Entity`, in case it does not already exists. We'll use this folder to store all our Entity classes defined in this module.

In this folder, create the `UserMessageEntity` class

### UserMessageEntity.php

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

Our convention, when creating the Entity, is to declare its properties as protected and generate getters/setters pair for each one. This may seem a lot, but we recommend using and IDE that supports getter/setter generation.

## Entity hydrators

DotKernel uses its [dot-hydrator](https://github.com/dotkernel/dot-hydrator) package to define hydrators, which is entirely based on zend framework's [zend-hydrator](https://github.com/zendframework/zend-hydrator). Please make sure to check both links for documentation and and informatoin as to what a hydrator is.

Our dot-hydrator package is just an extension to the zend framework hydrator package. In addition, it provides 2 custom hydrators that extends zend framework's `ClassMethods` hydrator. One is `ClassMethodsCamelCase` that we will use to hydrate our Entity. Custom hydrators can be used where appropriate. If you choose to write a custom hydrator, just make sure to register it in the hydrator manager service. See provided links on how to do this.

A hydrator must implement Zends `HydratorInterface` which defines 2 methods: `extract($object)` and `hydrate($data, $prototype)`. A hydrators role is to convert an object(Entity) to a php array and vice-versa using the defined hydrator methods.

- When we want to save an Entity to the database, we'll extract the data and send it to the database abstraction layer.
- When fetching an Entity, it will come in array form which will be hydrated into an Entity object.

Hydrators can be used on their own, but Zend Framework, and consequently, DotKernel provide hydrator integration with forms and fieldsets, to automatically do the hydration. Also, DotKernel packages that deal with Entity manipulation, often rely on configured hydrators, in order to seemlessly extract and hydrate objects.

The way the Entity class is defined, the `ClassMethodsCamelCase` is perfect for our needs, as we want our class methods to be camel-cased. Keep in mind we'll use this. This hydrator takes the array keys and call the appropriate setters to hydrate the object.

Having these classes defined, we are ready to implement our contact fieldset and form. Go ahead to the next lesson.

## Previous | Next

 **[Prev: Planning the form implementation](03-planning-the-contact-form-implementation.md)** | **[Next: Creating the contact form](05-creating-the-contact-form.md)**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
