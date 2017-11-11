# The DotKernel 3 ConfigProvider

- [The DotKernel 3 ConfigProvider](#the-dotkernel-3-configprovider)
    - [Show me some code](#show-me-some-code)
    - [How does it work](#how-does-it-work)

The ConfigProvider is what's responsible for making sure your Module will be loaded and the config will be added to the global array.

## Show me some code

Here is an example from [dot-queue](https://github.com/dotkernel/dot-queue).

```php
/**
 * Class ConfigProvider
 * @package Dot\Queue
 */
class ConfigProvider
{
    public function __invoke()
    {
        return [
            'dependencies' => $this->getDependencies(),
            'dot_console' => $this->getCommands(),
        ];
    }
    public function getDependencies()
    {
        return [
            'factories' => [
                QueueOptions::class => QueueOptionsFactory::class,
                AdapterManager::class => AdapterManagerFactory::class,

                // commands factories
                ConsumeCommand::class => ConsumeCommandFactory::class,
                RetryCommand::class => FailedCommandFactory::class,
            ]
        ];
    }
    public function getCommands()
    {
        return [
            'commands' => [
                [
                    'name' => 'queue:consume',
                    'route' => '[--queues=] [--all] [--sleep=] [--max-runtime=]' .
                        ' [--max-jobs=] [--memory-limit=] [--stop-on-error] [--stop-on-empty]',
                    'short_description' => 'Run the queue consumer loop',
                    'description' => 'Start the consumer worker loop, to consume jobs from specified queues',
                    'options_descriptions' => [
                        '--queues' => 'Comma separated list of queue names to run, defaults to the default queue',
                        '--all' => 'Run the consumer on all defined queues, round robin',
                        '--max-runtime' => 'Keep the consumer running a specified amount of time only',
                        '--max-jobs' => 'Specify how many jobs to run before closing the consumer',
                        '--memory-limit' => 'Set the memory limit allowed to be used by the consumer',
                        '--sleep' => 'Seconds to sleep the consumer worker if queue is empty',
                        '--stop-on-error' => 'Flag indicating the consumer to stop if an error has occurred',
                        '--stop-on-empty' => 'Flag indicating the consumer to stop if queues are empty',
                    ],
                    'handler' => ConsumeCommand::class,
                ],
                [
                    'name' => 'queue:retry',
                    'route' => '[<uuid>] [--queue=]',
                    'short_description' => 'Re-dispatch failed jobs back into the queue for retrying',
                    'options_descriptions' => [
                        '<uuid>' => 'Optionally, specify the job\'s UUID to retry',
                        '--queue' => 'Optionally, specify the queue name for which to retry jobs'
                    ],
                    'handler' => RetryCommand::class,
                ]
            ]
        ];
    }
}
```

## How does it work

We can see that in the ConfigProvider's `invoke` method, we register our dependencies, and other configuration setup.

- The dependencies include [factories]() and other settings that goes into the [service container]().
- The commands being registered here are console-commands, which can be run from the `php dot` command

Simply put, it's where you link your Module to the main application.
