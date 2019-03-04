HTTP
====

Sessions
--------

A session comprises three stages:
1. Client establishes a TCP connection;
2. Client sends request and waits for response;
3. Server processes request and sends back response.

Note: as of HTTP 1.1, the connection is no longer necessarily closed after
completing phase 3, adn the client is granted a further request. This enables
phases 2 and 3 to be performed any number of times.

### Request Phase ###

A client request consists of:
- text directives;
- separated by CRLF;
- divided into three blocks:
  1. Request method + path + HTTP protocol version
  2. HTTP Headers, e.g.:
     - what languages are understood;
     - what MIME types are understood;
     - data that alters behaviour, e.g., don't send an answer if the client
       already has it cached.
     - Ends with an empty line
  3. Optional data block (used mainly for PUT/POST)


Example 1:
```http
GET / HTTP/1.1
Host: developer.mozilla.org
Accept-language: fr

```

Example 2:
```http
POST /contact-form.php HTTP/1.1
Host: developer.mozilla.org
Content-length: 64
Content-type: application/x-www-form-urlencoded

name=Joe%20User&request=Send%20me%20one%20of%20your%catalogue
```

Note: the `Content-length` value is a decimal number describing the number of
octets (bytes) in the entity-body (i.e., the payload).

### Response Phase ###

- Similar to a request, a response is formed of:
  - text directives;
  - separated by CRLF;
  - divided into three blocks:
    1. status line (acknowledgement of HTTP version + status code + status text)
    2. HTTP headers (ending with a blank line)
    3. (optional) data block

For examples see <developer.mozilla.org/en-us/docs/Web/HTTP/Session#Structure_of_a_server_response>

Auth
----

### HTTP Basic Auth ###

- A mechanism for sending a username and password through HTTP headers.
- Flask has a package called `flask-httpauth` to make using this easier.

### JSON Web Tokens (JWT) ###

- Pronounced 'Jot'
- self-contained, transmitted by json, can be used inside HTTP header
  or even in URL
- Flask uses the "It's Dangerous" package to handle token-related stuff.
- Has the following structure:  
  `aaaaaaa.bbbbbbbb.ccccccc`
  - each set of characters is a base64encoded version of JSON
  - the `a` characters represent the header
  - the `b` characters represent the payload
  - the `c` characters represent the signature

#### Header ####

Example:
```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

In the above example the `typ` value declares the type is JSON, the `alg`
declares the hashing algorithm.

#### Payload ####

- aka the 'JWT claims'
- can be one of three types:
  - registered (pre-defined claims);
  - public (claims we create ourselves);
  - private (custom claims unique to a particular producer/consumer 
    combination).

Example:
```json
{
  "iss": "mysite.com",
  "exp": 1924700000,
  "name": "my user",
  "admin": true
}
```

In the above example, the first two claims are *registered claims* and the 
last two claims are *public claims*.

#### Signature ####

- A hash of the header + payload + secret
- where secret is a signature held by the server


