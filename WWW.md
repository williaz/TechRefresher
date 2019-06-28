- **Internet** is a huge collection of coumputers connected in a communications network
  - network of networks <= local network
- Internet Protocol(**IP**): four 8-bit numbers
- Name servers which implement the **DNS**(Domain Name System) to convert domain name to IP address
- **Web** is collection of software(servers, browers) and HTTP protocols installed on the computers on the Internet
- a **brower** is a client on the Web to initiate the communication with a server
- **HTTP**(Hypertext Transfer Protocol) provides a standard form of communication between browers and web servers
- **Web server**: to monitor a communications port on its host machine, accept and perform HTTP commands
  - document root: Web document; server maps requested URLs to the docu root
  - server root: software
  - proxy server
  - Apache, httpd.conf
  - MS IIS, IIS snap-in
- **URL**（Uniform Resource Locators）to address all docu on the Internet
  - scheme(protocol):obj-address
  - default port for web server:80; others need be attach in the URL
  - never have embedded spaces..: %20
  - If the dir does not have a file that can be recognozed as a home page, a directory listing is returned
- **MIME**(Multiperpose Internet Mail Extensions)
  - document type: type/subtype, text/html
  - server side use file name's extension as the key to determine
  - Experimental Docu type: subtype begins with x-, video/x-msvideo; need plug-in
- HTTP:
  - Request
    - HTTP Method, Domain part of the URL, HTTP version
      - GET, HEAD, POST, PUT, DELETE
      - HTTP 1.1 keep open
    - Header fields
      - General, Request, Response, Entity
    - Blank line
    - Message body
  - Response
    - Status line
      - 3 digits: 1 info, 2 success, 3 redirection, 4 client err, 5 server err
    - Response header
    - Blank line
    - Response body
- **Ajax**(Asynchronous JavaScript and XML) => responsive
  - asynchronous, not wait
  - relatively small response









