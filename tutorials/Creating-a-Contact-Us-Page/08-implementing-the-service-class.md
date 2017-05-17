# Implementing the service class

The service class will be responsible for the bussiness logic of the user messages. For our simple case, this will mean saving the user message to the backend. The saving process will be done through our mapper pattern implementation provided by the dot-mapper package.

To give you some details about how to use mappers
- you'll need access to the mapper manager
- there should be a mapper associated with the entity
- the entity must extend the `Dot\Mapper\Entity\Entity` class
- get the mapper via the mapper manager. The base abstract mapper that we provide already have basic CRUD functions implemented

Of course, we cannot go into details here about mappers, make sure you check out our official package documentation and other mapper related articles.

Create a new class in the `Service` folder of the `App` module.
##### UserMessageService.php
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

- implement the `MapperManagerAwareInterface` and use the `MapperManagerAwareTrait` in order to automatically have the mapper manager injected into the class. This is done by a default initializer registered in the service container that we provide.
- implement the `save` function. Use the entity class name to get its associated mapper
- use the mapper to save the entity to the database

Also make sure to change your entity in order to extend the `Entity` class defined in the dot-mapper package
##### UserMessageEntity.php
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
##### ConfigProvider.php
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

- as you can see, we have used InvokableFactory, as our service does not have a direct dependency to be injected. The mapper manager is injected automatically by an initializer.
- to set the service name as the interface's FQN, we have used one of the zend service manager features called aliases.

At this point the UserMessageService class should be available to get through the service container using the interface name. The contact controller will be injected with an instance of this class.

## The entity mapper

The last thing to do is create a mapper and register it in the mapper manager using the entity class name as its identifier. The mapper manager is a zend service manager `AbstractPluginManager`.

The mapper should implement `MapperInterface` or extend one of the provided abstract mapper classes. At the moment we provide an abstract sql database mapper that you can extend which offers the basic CRUD operations. In our simple case, this will be enough so luckily we don't have to write any code.

- create a new folder, call it `Mapper`
- create a new class in this folder with the following content

##### UserMessageDbMapper.php
```php
declare(strict_types=1);

namespace Frontend\App\Mapper;

use Dot\Mapper\Mapper\AbstractDbMapper;

class UserMessageDbMapper extends AbstractDbMapper
{

}
```

This mapper knows CRUD operations on the associated entity.
Next register this mapper in the mapper manager and associate an entity to it. Do this by going to the `ConfigProvider`, create a new config key `dot_mapper` and under this key, create another key called `mapper_manager`. In the `mapper_manager` key, register the mapper as you would do it in the service manager. Note that we use a specialy defined factory `DbMapperFactory` which you should use for all your mappers. This makes sure the mapper is properly initialized. You can also extend or rewrite this mapper factory if you need special initialization of the mapper.

##### ConfigProvider.php
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

As you can see, the association between the mapper and the entity is done using the entity FQN as the alias of the mapper.

The code is ready to be tested. If a valid input is provided and you have corectly setup your backend, the user message should be stored in the database.

### The thank you page

If all good, you should have seen the thank you page which right now it is empty. We'll leave this up to you to change as desired. We will put some text and a link to go back to the home page for now.
##### thank-you.html.twig
```html
{% extends '@layout/default.html.twig' %}

{% block title %}Contact Us{% endblock %}

{% block content %}
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
{% endblock %}
```

### [Prev: Processing the user message](https://github.com/dotkernel/dotkernel/blob/master/tutorials/creating-a-contact-us-page/07-processing-the-user-message.md) | [Next: Sending notification emails to a list](https://github.com/dotkernel/dotkernel/blob/master/tutorials/creating-a-contact-us-page/09-sending-notification-emails-to-a-list.md)
