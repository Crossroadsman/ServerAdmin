API Notes
=========

Resources
---------

- In REST, the nouns are "resources"
- Resources are often models from the application
- CRUD operations are performed on resources using specific URLs, these 
  are 'endpoints'
- Examples:
  ```
  /api/v1/games
  /api/v1/games/1247
  ```
- Always include the API's version in the URL to preserve backward compatibility
- Unique identifiers should always come after the category identifier

Actions
-------

The noun is the URL, the verb is the http method:
- GET: retrieve
- POST: create
- PUT: update
- DELETE: delete

When POSTing, we are adding a resource to a collection. Thus we POST to 
collection URLs not record URLs.

In contrast, we use PUT to update an existing record, so we use PUT on the
record's URL not the collection URL.

In principle we can use DELETE with either records or collections, but it's 
often too dangerous to implement DELETE on collection URLs.


Requests
--------

You can include request parameters in the URL using a query string.

You can also specify request parameters as headers in the HTTP request.

Here is an example request showing the use of both query strings and headers 
for specifying parameters:

```http
GET /myuser?topic=security HTTP/1.1
Host: teamtreehouse.com
Accept: application/JSON
Cache-control: no-cache
Postman-Token: .....
```

In this case the server has been configured to return JSON-formatted responses
if it sees `application/JSON` in the `Accept` header.

Here are some common headers that many APIs implement:

- `Accept`
- `Accept-language`
- `Cache-control`

The Wikipedia page [List of HTTP Header Fields][wiki_01] has a list of the 
standardized fields with examples.

Responses
---------

### HTTP Status Codes ###
(See Wikipedia: [List of HTTP Status Codes][wiki_02])

- 1XX: Informational: request was received, continuing process
- 2XX: Successful: request was successfully received, understood, and accepted
- 3XX: Redirection: further action needs to be taken to complete the request
- 4XX: Client Error: request contains identifiably bad syntax or cannot be 
       fulfilled
- 5XX: Server Error: server was unable to fulfill an apparently valid request

### Response Fields ###
(See Wikipedia: [List of HTTP Header Fields][wiki_01])


Security and Resiliency
-----------------------

### Caching ###
- Use caching when appropriate
- [`memcached`](https://memcached.org) is very popular

### Rate Limiting ###
- Each user is permitted a specified number of requests in a particular period
- Prevents legitimate users overwhelming the system and also protects against
  DDOS
  
### Authentication ###
- Most common method is through use of API tokens
- Because HTTP is stateless, tokens need to be included with every request
- Token (and secret) might be included in:
  - keys in JSON/XML data sent by client
  - request header (authentication header)
  - URL


[wiki_01]: https://en.wikipedia.org/wiki/List_of_HTTP_header_fields
[wiki_02]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
