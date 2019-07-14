
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
  
- [Good URI Practices](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
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
  
- HTTP Verbs
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
HTTP Method	Idempotent	Safe
OPTIONS	    yes	        yes
GET	        yes	        yes
HEAD	      yes	        yes
PUT	        yes	        no
POST	      no	        no
DELETE	    yes	        no
PATCH	      no	        no

  
  
  
  
