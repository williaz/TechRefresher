## HTTP basics

### [What happens when you type an URL in the browser and press enter?](https://medium.com/@maneesha.wijesinghe1/what-happens-when-you-type-an-url-in-the-browser-and-press-enter-bb0aa2449c1a)

1. Check DNS cache to find the IP address for the host; 
  - DNS(Domain Name System)
  - TTL(Time To Live)
  - CName(Canonical Name), Alias Recirds: alias of another
  - browser, OS, router, ISP(Internet service provider) cache
  - ISP DNS query: .com(Top Level Domain) -> NS(Name Server) records -> SOA(Start Of Authority Record)
2. TCP connection, initiatate if not available
  - 3 way hadnshack: client -> server SYN; server -> client SYN/ACK; client -> server ACK +. get "A"(address) record
  - HTTP/1.1 keeps the TCP connection
3. Browser sends an HTTP request
  - HTTP method
  - cookie
4. Server sends back response
  - Status code
5. Display Content
  - render HTML
  - traverse HTML tag, request more elements like images, CSS, JS
  - cache

- [x] [link](http://url)
```javascript
code
```

### URL

### Massage

### Cookie

### Redirect

### CORS
