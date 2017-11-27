# Deploying

- [Deploying](#deploying)
    - [Installation](#installation)
    - [Deploying updates](#deploying-updates)

One of the most crucial parts of a platform, is how to seamlessly and without worry, deploy new updates to the platform.
It's important that the deployment doesn't show any errors while it's deploying, or expose any security risks.

A `deployment script` is utilized to ensure that the exact same steps are taken during every single deployment, and that no "human-error" may cause any inconsistencies.

We assume that you're deploying from `git`, but any VCS would work, you just have to adapt the commands to use your preferred VCS.

## Installation

The first installation of the platform is fairly simple.

1) SSH onto the server
1) Figure out where to put your site, it could be in /home/web/example.com
1) Run `git clone git@github.com:repo /home/web/example.com`
1) cd `/home/web/example.com`
1) `composer install --no-interaction --prefer-dist --optimize-autoloader`
1) `php dot migrate --force`

> In Apache and Nginx config, you can also make sure to exclude all dot-files from web access, to ensure that no git files will be touched

Now your site is actually installed on the webserver, albeit it can't be accessed from the outside yet. Next up is the Apache/Nginx configuration.
It won't be too detailed here, just make sure to point the web-root to `/home/web/example.com/public`. This will also ensure that no git folders are accessible from the outside.

Restart Nginx/apache, and you're good to go.

> SSL would also be a good idea, and can be installed via `LetsEncrypt`

## Deploying updates

Deplying updates requires a bit of a different script.
> Please note that this should run as `root`, as you need to restart php-fpm

```bash
cd /home/web/example.com
git pull origin master
composer install --no-interaction --prefer-dist --optimize-autoloader
echo "" | sudo -S service php7.1-fpm reload #Or your equivalent PHP Version

php dot migrate --force

# If using queues
php dot queue:restart
```

This script can be saved as `deploy-example-com.sh`, and simply run as a GitHook whenever there's been a push to master, or run manually by SSHing into the server.
