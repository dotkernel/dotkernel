# Writing an API Endpoint Documentation

- [Writing an API Endpoint Documentation](#writing-an-api-endpoint-documentation)
    - [Draft](#draft)
    - [Summary Example](#summary-example)
        - [Specify the format](#specify-the-format)
            - [Getting the values](#getting-the-values)
            - [Getting a single value](#getting-a-single-value)
            - [Creating a new value](#creating-a-new-value)
            - [Changing the value](#changing-the-value)
            - [Deleting the value](#deleting-the-value)
    - [Format](#format)
        - [MyEntity](#myentity)
        - [Performing actions on MyEntity](#performing-actions-on-myentity)
            - [Request](#request)
                - [Example Request types](#example-request-types)
            - [Headers](#headers)
                - [Example headers](#example-headers)
            - [Request Body (Parameters)](#request-body-parameters)
        - [Expected responses](#expected-responses)
            - [Error Responses](#error-responses)
            - [Succes response](#succes-response)

## Draft

Before writing any code there must be a specification regarding the format/content negociation

First write a summary, after the summary, write the actions that can be performed on the endpoint.

> The notes in this summary were added for guiding purposes and are not required in your summary

## Summary Example

### Specify the format

> Specifications about format

#### Getting the values

> Request Type, Parameters, Body(if needed), Expected Error responses, Expected succes Response

#### Getting a single value

> Request Type, Parameters, Body(if needed), Expected Error responses, Expected succes Response

#### Creating a new value

> Request Type, Parameters, Body(if needed), Expected Error responses, Expected succes Response

#### Changing the value

> Request Type, Parameters, Body(if needed), Expected Error responses, Expected succes Response

#### Deleting the value

> Request Type, Parameters, Body(if needed), Expected Error responses, Expected succes Response

## Format

### MyEntity

Entity specifications can be represented in any form, but it is recommended that you represent it in the way your API represents it. (`JSON` in this example)

```json
{
    "id": 2,
    "name": "an entity",
    "value": 23
}
```

> A brief description of the fields. It can contain explanations if necessary

- `id` - integer unsigned value,  used for indexing and is a `PRIMARY KEY` in the `MySQL` database
- `name` - string value, max `200` characters, used for naming `MyEntity` names
- `value` - integer value, used to represent `MyEntity` values

### Performing actions on MyEntity

#### Request

Request representation structure is the following

- `RequestType` (GET, POST, PUT, PATCH, DELETE)
- url `endpoint`
- Parameters for GET requests are represented within the url

which will form a string like: `POST my.api.com/myentity?greaterThan=15&lowerThan=40`

##### Example Request types

Get `MyEntity` list

```http
GET my.api.com/myentity
```

Get `MyEntity` list with get parameters

```http
GET my.api.com/myentity/?greaterThan=15
```

Get `MyEntity` list

```http
GET my.api.com/myentity/2
```

Create new `MyEntity`

```http
POST my.api.com/myentity
```

Partially (or fully) update `MyEntity`

```http
PATCH my.api.com/myentity/2
```

Fully update `MyEntity`

```http
PUT my.api.com/myentity/2
```

Delete `MyEntity`

```http
DELETE my.api.com/myentity/2
```

#### Headers

If headers are needed they will be added after the request type.

##### Example headers

Using `json` for negotiating content and Bearer Token Authorization

```http
Content-Type: application/json
Authorization: Bearer a62399bacd009193d5503014d35caceb
```

The `Content-Type` header is used when working with parameters transmitted in the request `body`

#### Request Body (Parameters)

`POST`, `PATCH`, `PUT` and `DELETE` HTTP requests require `Content-Type` to be defined.
The `Content-Type` header tells the API how the data will be represented.
The request `body` is the actual parameters representation. (`json` in this case)

```json
{
    "name": "another entity",
    "value": 4129
}
```

### Expected responses

#### Error Responses

> The error responses don't always have a body

 The error can be explained briefly with the code

- `401 Unauthorized` - User is not logged or token has expired
- `403 Forbidden` - Trying to `update` or `delete` a `MyEntity` which user doesn't have access to
- `404 Not found` - Entity was not found
- `400 Bad Request` - Malformed `json`/`request body` reveiced
- `422 Unprocessable Entity` - Data sent to the server was not valid

#### Succes response

`200 OK` - Operation succesfully done
`201 Created` - Entity created

> Optional body response example
