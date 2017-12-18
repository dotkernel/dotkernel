# PSR-7

- [PSR-7](#psr-7)
    - [What is PSR-7](#what-is-psr-7)
    - [Interfaces](#interfaces)
        - [`Psr\Http\Message\MessageInterface` Methods](#psrhttpmessagemessageinterface-methods)
        - [`Psr\Http\Message\RequestInterface` Methods](#psrhttpmessagerequestinterface-methods)
        - [`Psr\Http\Message\ServerRequestInterface` Methods](#psrhttpmessageserverrequestinterface-methods)
        - [`Psr\Http\Message\ResponseInterface` Methods](#psrhttpmessageresponseinterface-methods)
        - [`Psr\Http\Message\StreamInterface` Methods](#psrhttpmessagestreaminterface-methods)
        - [`Psr\Http\Message\UriInterface` Methods](#psrhttpmessageuriinterface-methods)
    - [PSR-7 Usage](#psr-7-usage)
        - [Examples (using Zend Diactoros)](#examples-using-zend-diactoros)
        - [Working with HTTP Headers](#working-with-http-headers)
            - [Adding headers to response](#adding-headers-to-response)
            - [Appending values to headers](#appending-values-to-headers)
            - [Checking if header exists](#checking-if-header-exists)
            - [Getting comma-separated values from a header (also applies to request)](#getting-comma-separated-values-from-a-header-also-applies-to-request)
            - [Getting array of value from a header (also applies to request)](#getting-array-of-value-from-a-header-also-applies-to-request)
            - [Removing headers from HTTP Messages](#removing-headers-from-http-messages)
        - [Working with HTTP Message Body](#working-with-http-message-body)
            - [1. Getting the body separately](#1-getting-the-body-separately)
            - [2. Working directly on response](#2-working-directly-on-response)
        - [Getting the body contents](#getting-the-body-contents)
        - [Append to body](#append-to-body)
        - [Prepend to body](#prepend-to-body)
            - [Prepending by rewriting separately](#prepending-by-rewriting-separately)
            - [Prepending by using contents as a string](#prepending-by-using-contents-as-a-string)

## What is PSR-7

PSR-7 is a set of common interfaces defined by PHP Framework Interop Group.
These interfaces are representing HTTP messages, and URIs for use when communicating trough HTTP.

> HTTP messages are the foundation of web development. Web browsers and HTTP clients such as cURL create HTTP request messages that are sent to a web server, which provides an HTTP response message. Server-side code receives an HTTP request message, and returns an HTTP response message.

> HTTP messages are typically abstracted from the end-user consumer, but as developers, we typically need to know how they are structured and how to access or manipulate them in order to perform our tasks, whether that might be making a request to an HTTP API, or handling an incoming request.

*source: [PSR-7: HTTP message interfaces](http://www.php-fig.org/psr/psr-7/)*


## Interfaces

In this section the Interfaces methods will be listed. The purpose of this list is to help in finding the methods when working with PSR-7. This can be considered as a cheatsheet for PSR-7 interfaces.
The interfaces defined in PSR-7 are the following:

| Class Name                                                                                                        | Description                                                        |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| [Psr\Http\Message\MessageInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessagemessageinterface)             | Representation of a HTTP message                                   |
| [Psr\Http\Message\RequestInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessagerequestinterface)             | Representation of an outgoing, client-side request.                |
| [Psr\Http\Message\ServerRequestInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageserverrequestinterface) | Representation of an incoming, server-side HTTP request.           |
| [Psr\Http\Message\ResponseInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageresponseinterface)           | Representation of an outgoing, server-side response.               |
| [Psr\Http\Message\StreamInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessagestreaminterface)               | Describes a data stream                                            |
| [Psr\Http\Message\UriInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageuriinterface)                     | Value object representing a URI.                                   |
| [Psr\Http\Message\UploadedFileInterface](http://www.php-fig.org/psr/psr-7/#psrhttpmessageuploadedfileinterface)   | Value object representing a file uploaded through an HTTP request. |

### `Psr\Http\Message\MessageInterface` Methods

| Method Name                       | Description                                                          | Notes                                                                                                                                                                                            |
| --------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `getProtocolVersion()`            | Gets HTTP protocol version                                           | 1.0 or 1.1                                                                                                                                                                                       |
| `withProtocolVersion($version)`   | Sets HTTP protocol version                                           |                                                                                                                                                                                                  |
| `getHeaders()`                    | Gets all HTTP Headers                                                | [Request Header List](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields), [Response Header List](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Response_fields) |
| `hasHeader($name)`                | Checks if HTTP Header with given name exists                         |                                                                                                                                                                                                  |
| `getHeader($name)`                | Retrieves a array with the values for a single header                |                                                                                                                                                                                                  |
| `getHeaderLine($name)`            | Retrieves a comma-separated string of the values for a single header |                                                                                                                                                                                                  |
| `withHeader($name, $value)`       | Sets a HTTP Header                                                   | If header already exists, value will be overwritten                                                                                                                                              |
| `withAddedHeader($name, $value)`  | Appends value to given header                                        | If header already exists value will be appended, if not a new header will be created                                                                                                             |
| `withoutHeader($name)`            | Removes HTTP Header with given name                                  |                                                                                                                                                                                                  |
| `getBody()`                       | Get the HTTP Message Body                                            | Returns object implementing `StreamInterface`                                                                                                                                                    |
| `withBody(StreamInterface $body)` | Sets the HTTP Message Body                                           |                                                                                                                                                                                                  |

### `Psr\Http\Message\RequestInterface` Methods

Same methods as `Psr\Http\Message\MessageInterface`  + the following methods:

| Method Name                                         | Description                                         | Notes                                                                                                                                                                                |
| --------------------------------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `getRequestTarget()`                                | Retrieves the message's request target              | origin-form, absolute-form, authority-form, asterisk-form ([RFC7230](https://www.rfc-editor.org/rfc/rfc7230.txt))                                                                    |
| `withRequestTarget($requestTarget)`                 | Return an instance with the specific request-target |                                                                                                                                                                                      |
| `getMethod()`                                       | Retrieves the HTTP method of the request.           | GET, HEAD, POST, PUT, DELETE, CONNECT, OPTIONS, TRACE (defined in [RFC7231](https://tools.ietf.org/html/rfc7231)), PATCH (defined in [RFC5789](https://tools.ietf.org/html/rfc5789)) |
| `withMethod($method)`                               | Return an instance with the provided HTTP method    |                                                                                                                                                                                      |
| `getUri()`                                          | Retrieves the URI instance                          |                                                                                                                                                                                      |
| `withUri(UriInterface $uri, $preserveHost = false)` | Returns an instance with the provided URI           |                                                                                                                                                                                      |

### `Psr\Http\Message\ServerRequestInterface` Methods

Same methods as `Psr\Http\Message\RequestInterface`  + the following methods:

| Method Name                               | Description                                                             | Notes                              |
| ----------------------------------------- | ----------------------------------------------------------------------- | ---------------------------------- |
| `getServerParams()`                       | Retrieve server parameters                                              | Typically derived from `$_SERVER`  |
| `getCookieParams()`                       | Retrieves cookies sent by the client to the server.                     | Typically derived from `$_COOKIES` |
| `withCookieParams(array $cookies)`        | Return an instance with the specified cookies                           |                                    |
| `withQueryParams(array $query)`           | Return an instance with the specified query string arguments            |                                    |
| `getUploadedFiles()`                      | Retrieve normalized file upload data                                    |                                    |
| `withUploadedFiles(array $uploadedFiles)` | Create a new instance with the specified uploaded files                 |                                    |
| `getParsedBody()`                         | Retrieve any parameters provided in the request body                    |                                    |
| `withParsedBody($data)`                   | Return an instance with the specified body parameters                   |                                    |
| `getAttributes()`                         | Retrieve attributes derived from the request                            |                                    |
| `getAttribute($name, $default = null)`    | Retrieve a single derived request attribute                             |                                    |
| `withAttribute($name, $value)`            | Return an instance with the specified derived request attribute         |                                    |
| `withoutAttribute($name)`                 | Return an instance that removes the specified derived request attribute |                                    |

### `Psr\Http\Message\ResponseInterface` Methods

Same methods as `Psr\Http\Message\MessageInterface`  + the following methods:

| Method Name                             | Description                                                                       | Notes |
| --------------------------------------- | --------------------------------------------------------------------------------- | ----- |
| `getStatusCode()`                       | Gets the response status code.                                                    |       |
| `withStatus($code, $reasonPhrase = '')` | Return an instance with the specified status code and, optionally, reason phrase. |       |
| `getReasonPhrase()`                     | Gets the response reason phrase associated with the status code.                  |       |

### `Psr\Http\Message\StreamInterface` Methods

| Method Name                         | Description                                                              | Notes |
| ----------------------------------- | ------------------------------------------------------------------------ | ----- |
| `__toString()`                      | Reads all data from the stream into a string, from the beginning to end. |       |
| `close()`                           | Closes the stream and any underlying resources.                          |       |
| `detach()`                          | Separates any underlying resources from the stream.                      |       |
| `getSize()`                         | Get the size of the stream if known.                                     |       |
| `eof()`                             | Returns true if the stream is at the end of the stream.                  |       |
| `isSeekable()`                      | Returns whether or not the stream is seekable.                           |       |
| `seek($offset, $whence = SEEK_SET)` | Seek to a position in the stream.                                        |       |
| `rewind()`                          | Seek to the beginning of the stream.                                     |       |
| `isWritable()`                      | Returns whether or not the stream is writable.                           |       |
| `write($string)`                    | Write data to the stream.                                                |       |
| `isReadable()`                      | Returns whether or not the stream is readable.                           |       |
| `read($length)`                     | Read data from the stream.                                               |       |
| `getContents()`                     | Returns the remaining contents in a string                               |       |
| `getMetadata($key = null)()`        | Get stream metadata as an associative array or retrieve a specific key.  |       |

### `Psr\Http\Message\UriInterface` Methods

| Method Name                             | Description                                             | Notes |
| --------------------------------------- | ------------------------------------------------------- | ----- |
| `getScheme()`                           | Retrieve the scheme component of the URI.               |       |
| `getAuthority()`                        | Retrieve the authority component of the URI.            |       |
| `getUserInfo()`                         | Retrieve the user information component of the URI.     |       |
| `getHost()`                             | Retrieve the host component of the URI.                 |       |
| `getPort()`                             | Retrieve the port component of the URI.                 |       |
| `getPath()`                             | etrieve the path component of the URI.                  |       |
| `getQuery()`                            | Retrieve the query string of the URI.                   |       |
| `getFragment()`                         | Retrieve the fragment component of the URI.             |       |
| `withScheme($scheme)`                   | Return an instance with the specified scheme.           |       |
| `withUserInfo($user, $password = null)` | Return an instance with the specified user information. |       |
| `withHost($host)`                       | Return an instance with the specified host.             |       |
| `withPort($port)`                       | Return an instance with the specified port.             |       |
| `withPath($path)`                       | Return an instance with the specified path.             |       |
| `withQuery($query)`                     | Return an instance with the specified query string.     |       |
| `withFragment($fragment)`               | Return an instance with the specified URI fragment.     |       |
| `__toString()`                          | Return the string representation as a URI reference.    |       |

`Psr\Http\Message\UploadedFileInterface`

| Method Name            | Description                                           | Notes |
| ---------------------- | ----------------------------------------------------- | ----- |
| `getStream()`          | Retrieve a stream representing the uploaded file.     |       |
| `moveTo($targetPath)`  | Move the uploaded file to a new location.             |       |
| `getSize()`            | Retrieve the file size.                               |       |
| `getError()`           | Retrieve the error associated with the uploaded file. |       |
| `getClientFilename()`  | Retrieve the filename sent by the client.             |       |
| `getClientMediaType()` | Retrieve the media type sent by the client.           |       |

## PSR-7 Usage

All PSR-7 applications comply with these interfaces
They were created to establish a standard between middleware implementations.

> `RequestInterface`, `ServerRequestInterface`, `ResponseInterface` extend `MessageInterface`  because the `Request` and the `Response` are `HTTP Messages`.
> When using `ServerRequestInterface`, both `RequestInterface` and `Psr\Http\Message\MessageInterface` methods are considered.

The following examples will illustrate how basic operations are done in PSR-7.

### Examples (using Zend Diactoros)

Zend Diactoros is an implementation for PSR-7 interfaces. It will be used to illustrate these examples.
Installation guide for Zend Diactoros: [Zend Diactoros Documentation - Installation](https://zendframework.github.io/zend-diactoros/install/)

> All other PSR-7 implementations should have the same behaviour.

To use the `Zend Diactoros` classes add this at the beggining of the php file:

```php
<?php

use Zend\Diactoros\ServerRequestFactory;
use Zend\Diactoros\Response;

// autoloading

$request = ServerRequestFactory::fromGlobals($_SERVER, $_GET, $_POST, $_COOKIE, $_FILES);
$response = new Response();
```

### Working with HTTP Headers

#### Adding headers to response

```php
$response->withHeader('My-Custom-Header', 'My Custom Message');
```

#### Appending values to headers

```php
$response->withAddedHeader('My-Custom-Header', 'The second message');
```

#### Checking if header exists

```php
$request->hasHeader('My-Custom-Header'); // will return false
$response->hasHeader('My-Custom-Header'); // will return true
```

> Note: My-Custom-Header was only added in the Response

#### Getting comma-separated values from a header (also applies to request)

```php
// getting value from request headers
$request->getHeaderLine('Content-Type'); // will return: "text/html; charset=UTF-8"
// getting value from response headers
$response->getHeaderLine('My-Custom-Header'); // will return:  "My Custom Message; The second message"
```

#### Getting array of value from a header (also applies to request)

```php
// getting value from request headers
$request->getHeader('Content-Type'); // will return: ["text/html", "charset=UTF-8"]
// getting value from response headers
$response->getHeader('My-Custom-Header'); // will return:  ["My Custom Message",  "The second message"]
```

#### Removing headers from HTTP Messages

```php
// removing a header from Request, removing deprecated "Content-MD5" header
$request->withoutHeader('Content-MD5');

// removing a header from Response
// effect: the browser won't know the size of the stream
// the browser will download the stream till it ends
$response->withoutHeader('Content-Length');
```

### Working with HTTP Message Body

When working with the PSR-7 there are two methods of implementation:

#### 1. Getting the body separately

> This method makes the body handling easier to understand and is useful when repeatedly calling body methods. (You only call `getBody()` once). Using this method mistakes like `$response->write()` are also prevented.

```php
$body = $response->getBody();
// operations on body, eg. read, write, seek
// ...
// replacing the old body
$response->withBody($body);
// this last statement is optional as we working with objects
// in this case the "new" body is same with the "old" one
// the $body variable has the same value as the one in $request, only the reference is passed
```

#### 2. Working directly on response

> This method is useful when only performing few operations as the `$request->getBody()` statement fragment is required

```php
$response->getBody()->write('hello');
```

### Getting the body contents

The following snippet gets the contents of a stream contents.
> Note: Streams must be rewinded, if content was written into streams, it will be ignored when calling `getContents()` because the stream pointer is set to the last character, which is `\0` - meaning end of stream.

```php
$body = $response->getBody();
$body->rewind(); // or $body->seek(0);
$bodyText = $body->getContends();
```

> Note: If `$body->seek(1)` is called before `$body->getContents()`, the first character will be ommited as the starting pointer is set to `1`, not `0`. This is why using `$body->rewind()` is recommended.

### Append to body

```php
$response->getBody()->write('Hello'); // writing directly
$body = $request->getBody(); // which is a `StreamInterface`
$body->write('xxxxx');
```

### Prepend to body

Prepending is different when it comes to streams. The content must be copied before writing the content to be prepended.
The following example will explain the behaviour of streams.

```php
// assuming our response is initially empty
$body = $repsonse->getBody();
// writing the string "abcd"
$body->write('abcd');

// seeking to start of stream
$body->seek(0);
// writing 'ef'
$body->write('ef'); // at this point the stream contains "efcd"
```

#### Prepending by rewriting separately

```php
// assuming our response body stream only contains: "abcd"
$body = $response->getBody();
$body->rewind();
$contents = $body->getContents(); // abcd
// seeking the stream to beginning
$body->rewind();
$body->write('ef'); // stream contains "efcd"
$body->write($contents); // stream contains "efabcd"
```

> Note: `getContents()` seeks the stream while reading it, therefore if the second `rewind()` method call was not present the stream would have resulted in `abcdefabcd` because the `write()` method appends to stream if not preceeded by `rewind()` or `seek(0)`.

#### Prepending by using contents as a string

```php
$body = $response->getBody();
$body->rewind();
$contents = $body->getContents(); // efabcd
$contents = 'ef'.$contents;
$body->rewind();
$body->write($contents)
```

Sources:
[PSR-7: HTTP messages](http://www.php-fig.org/psr/psr-7/)
[zend-diactoros](https://zendframework.github.io/zend-diactoros/)
