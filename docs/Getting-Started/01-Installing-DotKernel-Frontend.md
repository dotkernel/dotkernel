# Installing DotKernel3 (Dot Frontend / DotKernel Frontend)

### Installation

Composer is required to install DotKernel Frontend. 

##### [Download Composer](https://getcomposer.org)

Instructions for installing:
* [Composer Installation -  Linux/Unix/OSX](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx)
* [Composer Installation - Windows](https://getcomposer.org/doc/00-intro.md#installation-windows)

> If you never used composer before make sure you read the [`Composer Basic Usage`](https://getcomposer.org/doc/01-basic-usage.md) section in Composer's documentation

#### Choose a destination path for DotKernel 3 / Dot Frontend installation
Example:
* absolute path `/var/www/dk3`
* or relative path `dk3` (equivalent with `./dk3`)

#### Installing the `dot-frontend` Composer Package

Depending on what the purpose of your project is, one of the following packages must be installed
 * `dot-frontend` - for API's and web applications
 * `dot-admin` - for administration platforms 
 
> Note: There is a possibility that you need both versions, in which case you must install them as different projects

##### Installing Dot-Frontend

After we chose the path for DotKernel3 (`dk` for this example) and which base package to use (`dot-frontend` for this example) it must be installed. Therefore to install DotKernel3 (frontend) run the following command:

`composer create-project dotkernel/dot-frontend -s dev /path/to/dk3`

This command will begin by downloading the `dot-frontend` package, after successfully downloading it the `dependencies` will be installed.

The setup script will prompt for some custom settings.

```shell
Created project in ./dk3

Please select which config file you wish to inject 'Zend\Session\ConfigProvider' into:
  [0] Do not inject
  [1] config/config.php
  Make your selection (default is 0):
```

For this option select `[0] Do not inject` because DotKernel3 already has an injected config provider which already contains the prompted configurations.
If you choose `[1] config/config.php` Zend's `ConfigProvider` from `session` will be injected.

`Remember this option for other packages of the same type? (y/N)`
`YES`
The `ConfigProvider`'s can be left un-injected as the requested configurations are already loaded.


### Configuration - First Run

* Import `data/dot-frontend.sql` in your database.
* Copy `config/autoload/local.php.dist` to `config/autoload/local.php`
  * Fill the database configuration
* (development mode) Copy `config/development.config.php.dist` to `config/development.config.php`
* (optional) `config/autoload/errorhandler.local.php.dist` to `config/autoload/errorhandler.local.php`
* remove `data/config-cache.php` if present
> Charset recommendation: utf8mb4
> If `config-cache.php` is present that config will be loaded regardless of the `ConfigAggregator::ENABLE_CACHE` in `config/autoload/zend-expressive.global.php`

### Testing (Running)

Composer's `serve` script can be used to start the PHP's built-in webserver.
In `dot-frontend`'s root folder run `composer serve`
> `composer serve` will run `> php -S 0.0.0.0:8080 -t public/ public/index.php`

`0.0.0.0` means server is open to all incoming connections
`127.0.0.1` means server can only be accessed locally 
`8080` the port on which the server is started

Open a browser and access `http://localhost:8080/`

You should see `DotKernel Frontend` welcome page.

