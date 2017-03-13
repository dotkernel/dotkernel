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

This article will only cover two interface
The interfaces used in [middleware](#link/to/middleware/article) are:

* Psr\Http\Message\ServerRequestInterface 
* Psr\Http\Message\ResponseInterface

### `Psr\Http\Message\ServerRequestInterface`


