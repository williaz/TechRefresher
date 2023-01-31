
### DOM
- add event listener
```js
  <h1 id="header">Hello World</h1>
  <button id="button">Change Header</button>
  <script>
    const header = document.getElementById("header");
    const button = document.getElementById("button");

    button.addEventListener("click", function() {
      header.innerHTML = "The header has been changed";
    });
  </script>

```
