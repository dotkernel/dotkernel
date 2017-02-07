# First Draft


# Installing DotKernel3 (Dot Frontend/ DotKernel Frontend)

### Installation

Composer is required to install DotKernel Frontend. Download 

Download and extract the [latest pre-built release](https://github.com/joemccann/dillinger/releases).

Composer
Create project
`composer create-project dotkernel/dot-frontend -s dev /path/to/dk3`

```shell
Created project in 

Please select which config file you wish to inject 'Zend\Session\ConfigProvider' into:
  [0] Do not inject
  [1] config/config.php
  Make your selection (default is 0):
```

For this option select `[0] Do not inject` because DotKernel3 already has an injected config provider.
If you choose `[1] config/config.php` Zend's `ConfigProvider` from `session` will be injected.

`Remember this option for other packages of the same type? (y/N)`
`YES`

### Configuration
Import `data/dot-frontend.sql` in your database.

In `config/autoload`:
* Copy `local.php.dist` to `local.php`
* Fill the database configuration
* remove `data/config-cache.php`
> Charset recommendation: utf8mb4
> If `config-cache.php` is present that config will be loaded regardless of the `ConfigAggregator::ENABLE_CACHE` in `config/autoload/zend-expressive.global.php`

### Testing

Composer's `serve` script can be used to start the PHP's built-in webserver.
In `dot-frontend`'s root folder run `composer serve`
> `composer serve` will run `> php -S 0.0.0.0:8080 -t public/ public/index.php`

`0.0.0.0` means server is open to all incoming connections
`127.0.0.1` means server can only be accessed locally 
`8080` the port on which the server is started

Open a browser and access `http://localhost:8080/`

You should see `DotKernel Frontend` welcome page.

