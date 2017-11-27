# Configuration

- [Configuration](#configuration)
    - [Your own config files](#your-own-config-files)

The config files in DotKernel are simple, they all return an array with the config values that they expose.

> In production, the config array is cached, for optimization purposes.

You can create your own config files and values without a problem, just create a new file in `/config/autoload` or `/config`.
Usually you want to use `/config/autoload`, as that's the place to put all the config values that should be automatically cached, anything outside the autoload folder is for other types of configuration, like a config file needed for a CLI script that is called manually etc.

```php
<?php

return [
    'dot_mail' => [
        //the key is the mail service name, this is the default one, which does not extends any configuration
        'default' => [
            //options that will be used only if Zend\Mail\Transport\Smtp adapter is used
            'smtp_options' => [
                'host' => '',
                'port' => 587,

                //connection class used for authentication
                //the calue can be one of smtp, plain, login or crammd5
                'connection_class' => 'login',

                'connection_config' => [
                    //'username' => '',
                    //'password' => '',

                    //the encryption type to be used, ssl or tls
                    //null should be used to disable SSL
                    'ssl' => 'tls',
                ]
            ],

        ],

        /**
         * You can define other mail services here, with the same structure as the defaul block
         * you can even extend from the default block, and overwrite only the differences
         */
    ],
];
```

The top level key `dot_mail` is the config identifier, this is the key you have to access in the Config service provider.
After that comes all the keys and values that you specify.

Accessing the keys in your application can be done in two ways, either through a **factory**:

```php
public function __invoke(ContainerInterface $container)
{
    $config = $container->get('config');
    $mailConfig = $config['dot_mail'];
}
```

Or through annotated services in a **controller**

```php
    /**
     * @Inject({config.dot_mail})
     */
    public function __construct(array $mailConfig)
    {
        $this->mailConfig = $mailConfig;
    }
```

## Your own config files

Whenver you create something that requires some sort of config file, simply add it to the correct `config` folder, and start using it either through a factory or the injected property.

> Remember to be in development-mode, otherwise your config file will be cached, and you won't propagate any changes you make to it.
