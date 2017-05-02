# Sending notification e-mails to a list

We'll add one more feature in this lesson. We need to notify someone that a user has sent a message. Be it an admin or a list of admins.

We won't add the e-mail sending logic directly in the controller or service class. We want to do this in a more decoupled way. And by doing so we will also cover more of Zend Framework and DotKernel.

More exactly what we'll cover in this lesson are the mail services provided by our [dot-mail](https://github.com/dotkernel/dot-mail) package and listening to events triggered by the mappers.

We'll cover how to trigger your own events in another lesson. For our purpose, we will rely on already defined events that are triggered by any mapper when doing its operations. 

We'll use one of the mapper's save events in order to send a notification e-mail to a predefined list of e-mail addresses. Events are great to completely decouple code. They also provide an extension point and code can be added without changing the original code by injecting features that are sometimes not even related to the main functionality. Also the decoupling is good because the code that saves the user message to the database should not know about what happens on such an event. You can add anything, from logging to email notifications or additional custom code that should run before or after a particular operation, all through event listeners instead.

## Creating a notification e-mail service class

We will wrap the default mail service into a higher level service class which compose the mail message that needs to be sent on various notification ocasions. Create a new PHP class file in the `Service` folder

##### NotificationMailService.php
```php
declare(strict_types=1);

namespace Frontend\App\Service;

use Dot\AnnotatedServices\Annotation\Inject;
use Dot\AnnotatedServices\Annotation\Service;
use Dot\Mail\Service\MailService;
use Frontend\App\Entity\UserMessageEntity;

/**
 * Class NotificationMailService
 * @package Frontend\App\Service
 *
 * @Service
 */
class NotificationMailService
{
    /** @var  MailService */
    protected $mailService;

    /** @var array  */
    protected $notificationList = [];

    /**
     * NotificationMailService constructor.
     * @param MailService $mailService
     * @param array $notificationReceivers
     *
     * @Inject({"dot-mail.service.default", "config.contact.notification_receivers"})
     */
    public function __construct(MailService $mailService, array $notificationReceivers = [])
    {
        $this->mailService = $mailService;
        $this->notificationList = $notificationReceivers;
    }

    /**
     * @param UserMessageEntity $userMessage
     * @return bool
     */
    public function sendUserMessageNotificationEmail(UserMessageEntity $userMessage)
    {
        if (empty($this->notificationList)) {
            return true;
        }

        $message = $this->mailService->getMessage();
        $message->setFrom($userMessage->getEmail(), $userMessage->getName());
        $message->setTo($this->notificationList);

        $message->setSubject("DotKernel notification");
        $this->mailService->setBody(sprintf(
            "A new user message from %s was submitted!" .
            "<br><br><strong>Subject:</strong>" .
            "<br>%s" .
            "<br><br><strong>Message:</strong>" .
            "<br>%s",
            $userMessage->getName(),
            $userMessage->getSubject(),
            nl2br($userMessage->getMessage())
        ));

        $result = $this->mailService->send();
        return $result->isValid();
    }
}
```
The class is pretty much self-explanatory
A few things to note
- we use our annotation service component to automatically inject dependencies withouth a factory class
- we use the default mail service, which you already have configured if you are using the frontend application
- we inject a configuration key that we don't have it defined yet, which will be the receiver email addresses as an array

In your `local.php` file define the notification receiver list(the admins or someone responsible to manage user messages)
##### local.php
```php
return [
    'contact' => [
        'notification_receivers' => [
            'some@email.com',
            //...
        ]
    ]
];
```

We haven't defined an interface for this class. We could have done that but for now it doesn't worth it, as it is over-engineering. Ok, we are ready to listen to events and use this class to send notification emails.

## Listening to mapper events

DotKernel mappers are triggering various events during their lifetime. You can hook into these events to add aditional functionality.

For our case we are interested in the `onAfterSaveCommit` event, this is when we are sure that the entity was inserted into the database(and the transaction was successful).

Mapper event listeners must implement the `MapperEventListenerInterface` provided by the mapper package. We recommend you to extend the `AbstractMapperEventListener` or use the `MapperEventListenerTrait` to have a good starting point.
Create a new folder in the `App` module, call it `Listener`. In this folder, create a new PHP class file

##### UserMessageMapperEventListener.php
```php
declare(strict_types=1);

namespace Frontend\App\Listener;

use Dot\AnnotatedServices\Annotation\Inject;
use Dot\AnnotatedServices\Annotation\Service;
use Dot\Mapper\Event\AbstractMapperEventListener;
use Dot\Mapper\Event\MapperEvent;
use Frontend\App\Service\NotificationMailService;

/**
 * Class UserMessageMapperEventListener
 * @package Frontend\App\Listener
 *
 * @Service
 */
class UserMessageMapperEventListener extends AbstractMapperEventListener
{
    /** @var  NotificationMailService */
    protected $notificationMailService;

    /**
     * UserMessageMapperEventListener constructor.
     * @param NotificationMailService $notificationMailService
     *
     * @Inject({NotificationMailService::class})
     */
    public function __construct(NotificationMailService $notificationMailService)
    {
        $this->notificationMailService = $notificationMailService;
    }

    /**
     * @param MapperEvent $e
     */
    public function onAfterSaveCommit(MapperEvent $e)
    {
        $message = $e->getParam('entity');
        $this->notificationMailService->sendUserMessageNotificationEmail($message);
    }
}
```

This class is self-explanatory. We are using annotations to inject the one dependency needed - the notification mail service defined earlier. This class can listen to any mapper events. Just implement the ones you are interested in. In our case, in the save event, we get the saved entity(check the dot-mapper documentation) and call the send notification method.

We have one last thing to do, tell the user message mapper that this is a listener of its events. There are more than one possible solution. We'll mention the delegator factories as a good one. But the dot-mapper package provides an even easier method - configuration. Go to the `ConfigProvider` mapper section and add the following configuration

##### ConfigProvider.php
```php
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
        ],
        'options' => [
            UserMessageEntity::class => [
                'mapper' => [
                    'event_listeners' => [
                        UserMessageMapperEventListener::class,
                    ]
                ]
            ]
        ]
    ];
}
```

The lines that we have added are the ones from the `options` key. For a complete description of possible configuration options please visit the dot-mapper documentation. 

As you can see the options are defined per mapper/entity. The `event_listeners` key is used to attach your defined event listeners to the listener chain.

Test your code again. If you have configured the notification receivers, you should receive a notification when a contact form is successfully submitted.

### [Prev: Implementing the service class](https://github.com/dotkernel/dotkernel/blob/master/tutorials/creating-a-contact-us-page/08-implementing-the-service-class.md)
