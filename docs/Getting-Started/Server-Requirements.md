## Server Requirements 

For production we highly recommend a *nix based system. 

### Webserver 
- Apache >= 2.2
  - mod_rewrite
  - .htaccess support (`AllowOverride All`)
- Nginx


### PHP >= 5.6
Both **mod_php** and **FCGI (FPM)** are supported.  

#### Required Settings and Modules & Extensions
- `memory_limit` >= 128M
- `upload_max_filesize` and `post_max_size` >= 100M (depending on your data) 
- [pdo_mysql](http://php.net/pdo-mysql) or [mysqli](http://php.net/mysqli)
- [iconv](http://php.net/iconv)
- [dom](http://php.net/dom)
- [simplexml](http://php.net/simplexml)
- [gd](http://php.net/gd)
- [exif](http://php.net/exif)
- [file_info](http://php.net/fileinfo) 
- [mbstring](http://php.net/mbstring)
- [zlib](http://php.net/zlib)
- [zip](http://php.net/zip)
- [bz2](http://php.net/bzip2)
- [openssl](http://php.net/openssl)
- [opcache](http://php.net/opcache)
- CLI SAPI (for Cron Jobs)
- [Composer](https://getcomposer.org/) (added to `$PATH`)

#### Recommended Modules & Extensions 
- [imagick](http://php.net/imagick) (if not installed *gd* is used instead, but with less supported image types)
- [curl](http://php.net/curl) (required if Google APIs are used)
- [phpredis](https://github.com/phpredis/phpredis) (recommended cache backend adapter)

### RDBMS
- MySQL / MariaDB >= 5.5.3 
