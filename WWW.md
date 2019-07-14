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
      - GET, HEAD, POST, PUT, DELETE, OPTIONS(cors)
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
```html
  - <header>, <footer>, <hgroup>, <artclel>
  - <section>
  - <aside>, <nav>
```

#### html
- case insensitve
- can omit closed tag
- attribute value must be quoted only if contans special char


### CSS
- benefit: imposing consistency on the styl of Web documens
- lower-level style sheets can override higher level style sheets
  - inline: tag, appear in opening tag
  - document: whole html body, ```<head> <style>```
  - external: any htmls,``` <link>```
  
- comment: /*  */

#### Selector
- simple: element name
- class: ````p.normal {prop-val list}  <p class = "normal">````
- Generic: more than one tag ```.normal```
- id: ```#spec-id {list}```
- Contextual: position chain, direct child ```ul ol {list}```
- Pseudo: events like link, visited, focus, hover ```h2:hover {list}```

#### Properties
- fonts, lists, alignment of text, margins, colors, background, borders
- Font:
  - font-family: generic font
  - font-size: 12em == 120%
  - font-variant: small-caps
  - font-style: italic
  - font-weight: bold
  - font: includes all types
  - text-decoration: line-through, overline, underline
  - letter-spacing: spaces between letters
- List
  - ul level, li level
  - bullet: can use image
- Aligment of text
  - text-indent
- Color
  - color: foreground
  - background-color
  
#### Box Model
- padding is the space between the content of an elelment and its border
- margin is the space between the border of two elements

```html
no default layout
<span> line
  
<div> section
```
#### Conflict
- 1. level
  - ```!important``` user origin
  - ```!important```author orign
  - normal author origin
  - normal user 
  - any browser
- specificity
  - id
  - class, pseudo
  - contextual
  - universal
- most recently seen: exteranl as seen in link or @import
  

### JS
- explicit embedding: in HTML ```<script>```
```html
<!--
// JS code in case browser doesn't have JS interpreters
// -->
```
- implicit embedding: ```<script tyoe = "text/javascript" src = "roots.js" />```
- prototype-based inheritance
- JS objects are collections of properties, root object is Object
- Number, String, Boolean, undefined, null
  - undefined: a Type; means a variable has been declared but has not yet been assigned a value. initialized by JavaScript with a default value of undefined
  - null is object; an assignment value. done programmatically
```javascript
var today = new Date();
```
#### Window
- an HTML document
- two prop:
  - document: Document object
  - window
  

```js
// dyamically creating HTML document content, ca include any tag
document.write("It is " + result+ "<br />");

// default object for JS is Window object
alert("Alert it \n"); // OK
confirm("Approve?"); // OK, Cancel
prompt("Waht's your name", "type your name"); // OK, Cancel, textfield
```
- false : "", "0", null, 0
- strictly equal: ===
```js
var list = [1, 2, '5', "23"];
// sort alphabetically by default
function num_reverse_order(a, b) {return b - 1};
list.sort(num_reverse_order);
list.length
```
- declared without var is global scope
- ^, $ only used in head/end to make effect
```js
var isValidPhone = str.search(/^\d{3}-\d{3}-\d{4}$/);
```

- this
  - bind: bind this with preset value
  - Store reference to context/this inside another variable
  - arrow: ES6
```js
var module = {
  x: 42,
  getX: function() {
    return this.x;
  }
}

var unboundGetX = module.getX;
console.log(unboundGetX()); // The function gets invoked at the global scope
// expected output: undefined

var boundGetX = unboundGetX.bind(module);
console.log(boundGetX());
// expected output: 42

function MyConstructor(data, transport) {
    this.data = data;
    transport.on('data', () => alert(this.data));
}
```
- SIAF(self-invoked anonymous function)
```js
for (var i=0; i<5; i++){
  setTimeout(function(){ 
    console.log(i); 
    }, 1000);
}
// all 5

for (var i=0; i<5; i++){
  (function (j){
    setTimeout(function(){ 
      console.log(j); 
      }, 1000);
   })(i);
}
```
- Closure: fucntion has access to the var in parent scope even after parent functions have returned
  - To use a closure, define a function inside another function and expose it. To expose a function, return it or pass it to another function.
  - The inner function will have access to the variables in the outer function scope, even after the outer function has returned.
```js
//
var xhr = new XMLHttpRequest();
xhr.open("POST", url, true);
xhr.setRequestHeader(yourId, "wz12312");
var fd = ew FormData;
var obj = this;
xht.onreadystartchange = function() {
  this;// XMLHttpRequest
  obj.otherFunc();
}
xhr.send(fd);
```

### DOM
- When a global variable is created in a client side script, it's created as a new prperty of the Window object
- DOM 1: getElementById
- event: all lowercase
- The write method of document should never be used in an event handler
- DOM2 Event propagation
  - capturing: root -> target node
    - addEventListener
  - target node
  - bubbling: back to rootm triggering any handlers in the path
- navigator: browser info

### Ajax
- starts from iframe, Google map uses to update tiles
- JS eval() func can convert JSON strings to JS objects, but it will interprets any JS code, security issue
- When the readyState property of the request object is 4 and the status property is 200, the callback function can process the returned data.
- if received text will be populated in text box, scanned for script tags first
```js
if (window.XMLHttpRequest)
    xhr = new XMLHttpRequest();
else
    xhr = new AtiveXObject("Mircorsoft.XMLHTTP");
xhr.open("GET", url, true); // true for asyc

var response = xhr.responseText;
var myObj = JSON.parse(response);
```

### JSONP
- JSON with Padding.
- JsONP is a method for sending JSON data without worrying about cross-domain issues.
- JSONP does not use the XMLHttpRequest object.
- JSONP uses the <script> tag instead.




