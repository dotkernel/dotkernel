# Implementing the service class

- [Implementing the service class](#implementing-the-service-class)
    - [UserMessageService.php](#usermessageservicephp)
    - [UserMessageEntity.php](#usermessageentityphp)
    - [ConfigProvider.php](#configproviderphp)
    - [The entity mapper](#the-entity-mapper)
    - [UserMessageDbMapper.php](#usermessagedbmapperphp)
        - [The thank you page](#the-thank-you-page)
            - [thank-you.html.twig](#thank-youhtmltwig)
    - [Previous | Next](#previous-next)
        - [Tutorial index](#tutorial-index)
        - [All tutorials](#all-tutorials)

The service class will be responsible for the user message's bussiness logic. For our simple case, this will mean saving the user message to the backend.

To give you some details about how to use mappers

- You'll need access to the mapper manager
- There should be a mapper associated with the entity
- The entity must extend the `Dot\Mapper\Entity\Entity` class
- Get the mapper via the mapper manager. The base abstract mapper that we provide already have basic CRUD functions implemented

> We cannot go into full details about the mappers here, so make sure you check out our official package documentation.

Create a new class in the `Service` folder of the `App` module.

## UserMessageService.php

```php
declare(strict_types=1);

namespace Frontend\App\Service;

use Dot\Mapper\Mapper\MapperInterface;
use Dot\Mapper\Mapper\MapperManagerAwareInterface;
use Dot\Mapper\Mapper\MapperManagerAwareTrait;
use Frontend\App\Entity\UserMessageEntity;

class UserMessageService implements UserMessageServiceInterface, MapperManagerAwareInterface
{
    use MapperManagerAwareTrait;

    /**
     * @param UserMessageEntity $message
     * @param array $options
     * @return mixed
     */
    public function save(UserMessageEntity $message, array $options = [])
    {
        /** @var MapperInterface $mapper */
        $mapper = $this->getMapperManager()->get(UserMessageEntity::class);
        return $mapper->save($message, $options);
    }
}
```

- Implement the `MapperManagerAwareInterface` and use the `MapperManagerAwareTrait` in order to automatically have the mapper manager injected into the class. This is done by a default initializer registered in the service container that we provide.
- Implement the `save` function. Use the entity class name to get its associated mapper
- Use the mapper to save the entity to the database

Also make sure to change your entity in order to extend the `Entity` class defined in the dot-mapper package

## UserMessageEntity.php

```php
//...
use Dot\Mapper\Entity\Entity;
//...

class UserMessageEntity extends Entity
{
    //...
}
```

Next, go the the `App` module's `ConfigProvider` in order to register our service class.
## ConfigProvider.php

```php
public function getDependencies(): array
{
    return [
        'factories' => [
            UserMessageService::class => InvokableFactory::class,
            //...
        ],
        'aliases' => [
            UserMessageServiceInterface::class => UserMessageService::class,
            'UserMessageService' => UserMessageServiceInterface::class,
            //...
        ]
    ];
}
```

- As you can see, we have used InvokableFactory, as our service does not have a direct dependency to be injected. The mapper manager is injected automatically by an initializer.
- To set the service name as the interface's FQCN, we have used one of the zend service manager features called aliases.

At this point the UserMessageService class should be available to get through the service container using the interface name. The contact controller will be injected with an instance of this class.

## The entity mapper

The last thing to do is create a mapper and register it in the mapper manager using the entity class name as its identifier. The mapper manager is an instance of the zend service manager's `AbstractPluginManager`.

The mapper should implement `MapperInterface` or extend one of the provided abstract mapper classes. At the moment we provide an abstract SQL database mapper that you can extend, this offers the basic CRUD operations. In our case, this will be enough, so we'll just use it as-is.

- Create a new folder, call it `Mapper`
- Create a new class in this folder with the following content

## UserMessageDbMapper.php

```php
declare(strict_types=1);

namespace Frontend\App\Mapper;

use Dot\Mapper\Mapper\AbstractDbMapper;

class UserMessageDbMapper extends AbstractDbMapper
{

}
```

This mapper knows CRUD operations on the associated entity.
Next register this mapper in the mapper manager and associate an entity to it.
Do this by going to the `ConfigProvider`, create a new config key called `dot_mapper`, and in this key, create another key called `mapper_manager`.
In the `mapper_manager` key, register the mapper as you would do it in the service manager. Note that we use a specialy defined factory `DbMapperFactory` which you should use for all your mappers. This makes sure the mapper is properly initialized. You can also extend or rewrite this mapper factory if you need special initialization of the mapper.

> ConfigProvider.php

```php
//...
class ConfigProvider
{
    public function __invoke(): array
    {
        return [
            //...
            'dot_mapper' => $this->getMappers(),
        ];
    }

    //...
    public function getMappers(): array
    {
        return [
            'mapper_manager' => [
                'factories' => [
                    UserMessageDbMapper::class => DbMapperFactory::class,
                ],
                'aliases' => [
                    UserMessageEntity::class => UserMessageDbMapper::class,
                ]
            ]
        ];
    }
}
```

As you can see, the association between the mapper and the entity is done using the entity FQCN as the alias of the mapper.

The code is ready to be tested. If a valid input is provided and you have setup your backend correctly, the user message should now be stored in the database.

### The thank you page

You should seen the thank you page, albeit empty at the moment, after a successful form submit.
Let's put some basic information on the page, but feel free to customize it to your desire.

#### thank-you.html.twig

```html
{ % extends '@layout/default.html.twig' %}

{ % block title %}Contact Us{ % endblock %}

{ % block content %}
    <div class="container">
        <div class="row">
            <div class="col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3 col-lg-6 col-lg-offset-3 no-padding forms">
                <h1 class="page-header">Thank you!</h1>
                <div class="info-content">
                    <p>
                        You message was successfully sent. <br />We will get in touch with you as soon as possible.
                    </p>
                    <p>
                        <a href="{{ path('home') }}">Go to Home page</a>
                    </p>
                </div>
            </div>
        </div>
    </div>
{ % endblock %}
```

## Previous | Next

**[Prev: Processing the user message](07-processing-the-user-message.md)[Next: Sending notification emails to a list](09-sending-notification-emails-to-a-list.md)**

### Tutorial index

Go to the **[tutorial page](README.md)**

### All tutorials

View all tutorials by going to the [tutorials list](../README.md)
