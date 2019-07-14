
### Basics of REST 
- Representational State Transfer
- REST is a set of constraints
  - Client-server Seperation of concern, independence of evolution
  - Stateless requests, session data return back
  - explicitly labeled as cacheable or non-cacheable
  - Uniform interface for all clients: generaioc, high-level, abstract capabilities
  - Layered-system
  
- Any information that can be named / identified via a unique, global id (the URI) - is a Resource.
- multiple Representations: format: marshal
- Hypermedia APIs are easier to work with. 
Along with some type of data as a response to a request, an application receives a list of links that defines what can be done next. 
  - HAL (Hypertext Application Language)
  - JSON is not
 
- The Persistence layer
- The Service layer
- The Web / API layer
- in a production-grade application - always return a separate DTO over Entity.

### with HTTP Semantics 
- 400 Bad Request
  - the generic, catch-all for client side errors
  - usually happens for PUT or POST requests
  - invalid JSON format or failed validation otr invvlida Enityt
- Global Exception Handler
  - At a high level - we have application code on the one hand - throwing exceptions, 
and on the other hand we have this exception handler - handling them.

- validation
  - Bean Validation: @NotNull
  - The best place to trigger the validation process is early on in the lifecycle of the request - in the Controller layer of the API. 
  
#### [Best API Practices](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
- logical, meaningful and predictable 
- Resource-centric, nouns in URL
    - not leak irrelevant implementation details out to your API
    - actions that don't fit into the world of CRUD operations
      - Treat it like a sub-resource with RESTful principles.```DELETE /gists/:id/star```
      - Restructure the action to appear like a field of a resource.PATCH
- plurals are more widely accepted in practice.
- raw IDs or UUIDs 
- URIs should not contain any information about the representation
    - use the Accept (and Accept-Language) headers.
- Always use SSL. No exceptions.
    - Do not redirect non-SSL access to API URLs to their SSL counterparts. Throw a hard error instead! 
- An API is only as good as its documentation.
- Always version your API. 
    - [Stripe](https://stripe.com/docs/api/errors): the URL has a major version number (v1), 
    but the API has date based sub-versions which can be chosen using a custom HTTP request header.(Stripe-Version header.)
- Aliases for common queries
  - packaging up sets of conditions into easily accessible RESTful paths.
- Limiting which fields are returned by the API
  - Use a fields query parameter that takes a comma separated list of fields to include. 
- Updates & creation should return a resource representation
  - A PUT, POST or PATCH call may make modifications to fields of the underlying resource that weren't part of the provided parameters (for example: created_at or updated_at timestamps).
  - In case of a POST that resulted in a creation, use a HTTP 201 status code and include a Location header that points to the URL of the new resource.
- append a .json or .xml extension to the endpoint URL.
- snake_case vs camelCase for field names: idiomatic naming conventions for underlying language
- Pretty print by default & ensure gzip is supported
  - always set the Accept-Encoding header. 
  - If Content-Encoding is set on the response, use the specified algorithm. If it is missing, assume GZIP.
- Don't use an envelope by default, but make it possible when needed
   - There are 2 situations where an envelope is really needed - if the API needs to support cross domain requests over JSONP or if the client is incapable of working with HTTP headers.
- Rate limiting
  - 429 Too Many Requests
  - X-Rate-Limit-Limit - The number of allowed requests in the current period
  - X-Rate-Limit-Remaining - The number of remaining requests in the current period
  - X-Rate-Limit-Reset - The number of seconds left in the current period
  
- A RESTful API should be stateless. This means that request authentication should not depend on cookies or sessions. Instead, each request should come with some sort authentication credentials.
  - 401 Unauthorized
- Caching
  - ETag: When generating a response, include a HTTP header ETag containing a hash or checksum of the representation. This value should change whenever the output representation changes. Now, if an inbound HTTP requests contains a If-None-Match header with a matching ETag value, the API should return a 304 Not Modified status code instead of the output representation of the resource.
  - Last-Modified
- Error: At a minimum, the API should standardize that all 400 series errors come with consumable JSON error representation.

```
GET /tickets?q=return&state=open&sort=-priority,created_at - Retrieve the highest priority open tickets mentioning the word 'return'
GET /tickets?fields=id,subject,customer_name,updated_at&state=open&sort=-updated_at


# envelope to include additional metadata or pagination information
{
  "data" : {
    "id" : 123,
    "name" : "John"
  }
}
```  

#### HTTP Verbs
  - PUT as an i-dem-potent operation and POST as not idempotent.
    - PUT: fully update(pass id) => 200 OK; POST: like insert => 201 Created
  - PATCH: update field
  - DELETE: 200 OK; 404 Not Found
  - GET
```rest
/api/privileges?page=1&size=5
# pagination param
```


- An idempotent HTTP method is a HTTP method that can be called many times without different outcomes.
  - state of the system
- Safe methods are HTTP methods that do not modify resources.
```
HTTP Method	Idempotent	Safe
OPTIONS	    yes	        yes
GET	        yes	        yes
HEAD	      yes	        yes
PUT	        yes	        no
POST	      no	        no
DELETE	    yes	        no
PATCH	      no	        no
```
- query parameters
  - Filtering: Use a unique query parameter for each field that implements filtering. 
  - Sorting: a generic parameter sort can be used to describe sorting rules. 
  - Searching: Search queries should be passed straight to the search engine and API output should be in the same format as a normal list result.

#### HTTP status code
- 200 OK - Response to a successful GET, PUT, PATCH or DELETE. Can also be used for a POST that doesn't result in a creation.
- 201 Created - Response to a POST that results in a creation. Should be combined with a Location header pointing to the location of the new resource
- 204 No Content - Response to a successful request that won't be returning a body (like a DELETE request)
- 304 Not Modified - Used when HTTP caching headers are in play
- 400 Bad Request - The request is malformed, such as if the body does not parse
- 401 Unauthorized - When no or invalid authentication details are provided. Also useful to trigger an auth popup if the API is used from a browser
- 403 Forbidden - When authentication succeeded but authenticated user doesn't have access to the resource
- 404 Not Found - When a non-existent resource is requested
- 405 Method Not Allowed - When an HTTP method is being requested that isn't allowed for the authenticated user
- 410 Gone - Indicates that the resource at this end point is no longer available. Useful as a blanket response for old API versions
- 415 Unsupported Media Type - If incorrect content type was provided as part of the request
- 422 Unprocessable Entity - Used for validation errors
- 429 Too Many Requests - When a request is rejected due to rate limiting

  
  
  
