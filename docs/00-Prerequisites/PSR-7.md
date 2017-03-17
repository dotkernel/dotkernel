# PSR-7 (WIP)

## What is PSR-7

PSR-7 is a set of common interfaces defined by PHP Framework Interop Group.
These interfaces are representing HTTP messages, and URIs for use with HTTP messages.

[What is an interface(Soon)](link/to/oop/tutorial)

> HTTP messages are the foundation of web development. Web browsers and HTTP clients such as cURL create HTTP request messages that are sent to a web server, which provides an HTTP response message. Server-side code receives an HTTP request message, and returns an HTTP response message.

> HTTP messages are typically abstracted from the end-user consumer, but as developers, we typically need to know how they are structured and how to access or manipulate them in order to perform our tasks, whether that might be making a request to an HTTP API, or handling an incoming request.

*source: [PSR-7: HTTP message interfaces](http://www.php-fig.org/psr/psr-7/)*

## Interfaces
The interfaces defined in PSR-7 are the following:

| Class Name | Description |
|---|---|
| [Psr\Http\Message\MessageInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessagemessageinterface) | Representation of a HTTP message |
| [Psr\Http\Message\RequestInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessagerequestinterface) | Representation of an outgoing, client-side request. |
| [Psr\Http\Message\ServerRequestInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageserverrequestinterface) | Representation of an incoming, server-side HTTP request. | 
| [Psr\Http\Message\ResponseInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageresponseinterface) | Representation of an outgoing, server-side response. |
| [Psr\Http\Message\StreamInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessagestreaminterface) | Describes a data stream |
| [Psr\Http\Message\UriInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageuriinterface) | Value object representing a URI. |
| [Psr\Http\Message\UploadedFileInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageuploadedfileinterface) | Value object representing a file uploaded through an HTTP request. |

#### `Psr\Http\Message\MessageInterface` Methods

| Method Name                        | Description | Notes |
|------------------------------------| ----------- | ----- |
| `getProtocolVersion()`             | Gets HTTP protocol version          |  1.0 or 1.1 |
| `withProtocolVersion($version)`    | Sets HTTP protocol version          |      |
| `getHeaders()`                     | Gets all HTTP Headers               | [Request Header List](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields), [Response Header List](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Response_fields)      |
| `hasHeader($name)`                 | Checks if HTTP Header with given name exists  | |
| `getHeader($name)`                 | Retrieves a array with the values for a single header | |
| `getHeaderLine($name)`             | Retrieves a comma-separated string of the values for a single header |  |
| `withHeader($name, $value)`        | Sets a HTTP Header | If header already exists, value will be overwritten |
| `withAddedHeader($name, $value)`   | Appends value to given header | If header already exists value will be appended, if not a new header will be created |
| `withoutHeader($name)`             | Removes HTTP Header with given name| |
| `getBody()`                        | Get the HTTP Message Body | Returns object implementing `StreamInterface`|
| `withBody(StreamInterface $body)`  | Sets the HTTP Message Body | |

#### `Psr\Http\Message\RequestInterface` Methods

| Method Name                        | Description | Notes |
|------------------------------------| ----------- | ----- |
| `getRequestTarget()`                | x           |      |
| `withRequestTarget($requestTarget)` | x          |      |
| `getMethod()`                       | x               |      |
| `withMethod($method)`               | x  | |
| `getUri()`                 | x | |
| `withUri(UriInterface $uri, $preserveHost = false) | x |  |
| `withHeader($name, $value)`        | Sets a HTTP Header | If header already exists, value will be overwritten |
| `withAddedHeader($name, $value)`   | Appends value to given header | If header already exists value will be appended, if not a new header will be created |
| `withoutHeader($name)`             | Removes HTTP Header with given name| |
| `getBody()`                        | Get the HTTP Message Body | Returns object implementing `StreamInterface`|
| `withBody(StreamInterface $body)`  | Sets the HTTP Message Body | |

### Interfaces used in Middleware

All PSR-7 applications comply with these interfaces 
They were created to establish a standard between middleware implementations.

The main interfaces used in `middleware` are:

* Psr\Http\Message\ServerRequestInterface
* Psr\Http\Message\ResponseInterface

`Psr\Http\Message\ServerRequestInterface` interface extends `Psr\Http\Message\RequestInterface`.
All interfaces extend `Psr\Http\Message\MessageInterface`.
When using `Psr\Http\Message\ServerRequestInterface`, both `Psr\Http\Message\RequestInterface` and `Psr\Http\Message\MessageInterface` methods are considered.

> Note: `Psr\Http\Message\MessageInterface` is used because `Request` and `Response` are `HTTP Messages`

Enough with the talking, let's put things in practice.
#### Examples

##### Using Zend Diactoros
Zend Diactoros is an implementation for PSR-7 interfaces. It will be used to illustrate these examples.
Installation guide for Zend Diactoros: [Zend Diactoros Documentation - Installation](https://zendframework.github.io/zend-diactoros/install/)


To use the `Zend Diactoros` classes add this at the beggining of the php file:
```php
<?php
use Zend\Diactoros\ServerRequestFactory;
use Zend\Diactoros\Response;

$request = ServerRequestFactory::fromGlobals($_SERVER, $_GET, $_POST, $_COOKIE, $_FILES);
$response = new Response();
```

Adding headers to response:
```php
$response->withHeader('My-Custom-Header', 'My Custom Message');
```

Appending values to headers
```php
$response->withAddedHeader('My-Custom-Header', 'The second message');
```

Checking if header exists:
```php
$request->hasHeader('My-Custom-Header'); // will return false
$response->hasHeader('My-Custom-Header'); // will return true
```
> Note: My-Custom-Header was only added in the Request

Getting comma-separated values from a header (also applies to request)
```php
// getting value from request headers
$request->getHeaderLine('Content-Type'); // will return: "text/html; charset=UTF-8"
// getting value from response headers
$response->getHeaderLine('My-Custom-Header'); // will return:  "My Custom Message; The second message"
```

Getting array of value from a header (also applies to request)
```php
// getting value from request headers
$request->getHeader('Content-Type'); // will return: ["text/html", "charset=UTF-8"]
// getting value from response headers
$response->getHeader('My-Custom-Header'); // will return:  ["My Custom Message",  "The second message"]
```





>x
middleware access
PSR-7 and routing
invoke application 
interface
invoke
trait
factory + how to use factories in DK3

