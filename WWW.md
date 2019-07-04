### Intro
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


### HTML
- to specify the general layout
- tag: to specify categories of content; like suggestion; a browser has its own default presentation spec
- browsers ignore all unrecognized tags and line breaks
- An HTML must include <html>, <head>, <title>, <body>
- <head> always contains <title> and <meta>
- Image: GIF jiffy; JPEG Jay-peg; PNG ping
```html
<!-- comment -->
<!DOCTYPE html>
<html lang = "en">
  <head>
    <title> Sample html</title>
    <meta charset = "utf-8">
    <meta name = "keywords" content = "for search engine: html sample">
  </head>
  <body>
    <header>
      <h1> 6 levels headings </h1>
      <h6> heading </h6>
    </header>
    <p> multiple   spaces in p will be replaced by single space <br />
    </p>
    <pre>  to
             preserve
                     the white spaces
    </pre>
    <blockquote>
      "will be indented on both sides mostly as quotes"
    </blockquote>
    
    <p> content-based style tags</p>
    <em>italics</em>
    <strong>bold</strong>
    <code> X<sub>2</sub><sup>3</sup></code>
    
    <p> character entities like 2 &lt 5</p>
    <!-- horizonal rule -->
    <hr />
    
    
    <a href = "google.com"> 
      <img src= "image/goog_logo.jpeg" alt = "just logo pic">
      Image as an effective link
    </a>
    
    <h2 id = "uniqId"> id attribut must be unique within the docu</h2>
    <a href = "#uniqId"> back to id </a>
    
    <ul>
      <li> unordered list </li>
      <ol> 
        <li> ordered list, nested</li>
      </ol>
    </ul>
    
    <dl>
      <dt> page 234</dt>
      <dd> definition list <dd>
    </dl>
    
    <table>
       <caption> sample table </caption>
       <tr>
         <th colspan = "1"> header</th>
       </tr>
       <tr>
         <td> data1 </td>
         <td> data2 </td>
       </tr>
    </table>
    <form action = "call">
      <input type = "text" name = "comment" size = "25" maxlength = "25">
      <!-- type as checkbox, radio-->
      <select name = "classes" >
        <option> English </option>
        <option> others a lot .. </option>
      </select>
      <textarea name = "draft" row = "3", cols = "40">
        (long and meaningless)
      </textarea>
      <input type = "submit" value = "trigger">
      <input type = "reset"  value = "to default">
    </form>
    <!--html5-->
    <audio contols = "controls">
       <source src = "xx.ogg" />
       <source src = "xx.wav" />
       <source src = "xx.mp3" />
    </audio>
    <vedio contols = "controls" width = "900" heigth = "600" autoplay = "autoplay" preload = "preload">
       <source src = "xx.ogv" />
       <source src = "xx.webm" />
       <source src = "xx.mp4" />
    </audio>
    <time datetime = "2019-07-04T15:45" pubdate = "pubdate">
       Today
    </time>
  </body>
</html>
```
- html5 organization elements
  - <header>, <footer>, <hgroup>, <artical>
  - <section>
  - <aside>, <nav>

#### html
- case insensitve
- can omit closed tag
- attribute value must be quoted only if contans special char



