First we should know that **No single measure will stop all XSS attacks**. combination of different techniques can reduce risk of XSS. these protections can be implemented in multiple layers and categories:

### 1. Output Encoding:

* Variables should not be interpreted as code instead of text.
* Encoding can be done in multiple contexts:
  * HTML Contexts
    * Where? between 2 HTML tags like `<div> $HERE </div>`
    * Solution? use HTML Entity Encoding (Can be done using `.textContent` in JavaScript)
    * Which characters to encode? Unsafe characters (`& < > " '`) and non ASCII characters.
  * HTML Attributes
    * Where? Inside HTML attribute value like `<div attr=$HERE>`
    * Solution?
      1. HTML Attribute Encode
      2. Use quotation marks to surround variables
         * To make it difficult to change context
         * Reduce characters needed to encode
      3. using `.setAttribute` in JavaScript.
    * All attributes that accept JavaScript are unsafe.
  * JavaScript Context
    * Where? Directly inside JavaScript Code (like `var name = $user_input;`)
    * Solution? Encode ALL Characters as \\xHH
    * The only safe place is between quoted data value (like `<script>alert("HERE")</script>`)
  * CSS Context
    * Where? Example: `body { background-image: url('{{ user_input }}'); }`
    * Solution? 
      * Variable should only be placed in CSS property. Any other CSS contexts are unsafe.
      * Can be done using `style.property = x` in JavaScript.
  * URL Context
    * Where? variable is placed into a URL (Example: `<a href="/search?q={{ user_input }}">Search</a>`)
    * Solution?
      * Use URL encoding and sanitization
      * Can be done using `window.encodeURIComponent(x)` in JavaScript.
* Extremely DANGEROUS Contexts:
  * Never place user input inside $HERE without strict validation:

  ```xml
  <sctipt>HERE</script>
  <style>HERE</style>
  <!-- HERE -->
  <div HERE=test>
  <HERE href=/>
  ```
  * All JavaScript event handlers
  * Never pass user input to Unsafe functions like `eval()` `setInterval()` `setTimeOut()`
  * Handling URLs (Exploitable with _javascript:_ scheme if not handled securely)
    * CSS: {background-url: "Here"}
    * HTML: \<a href="Here"\>

---

### 2. HTML Sanitization:

* Strip unsafe HTML from variable and return a safe string
* Use [DOMPurify](https://github.com/cure53/DOMPurify) (See an online demo of how it works [here](https://cure53.de/purify))
* Never modify or change the value after its sanitized! (for example if you are passing the result of a sanitized input to a library or unknown function, check it carefully)
* You can see example of what is exactly a sanitization library like DOMPurify doing in code box bellow:

```javascript
DOMPurify.sanitize('<img src=x onerror=alert(1)//>'); // becomes <img src="x">
DOMPurify.sanitize('<svg><g/onload=alert(2)//<p>'); // becomes <svg><g></g></svg>
DOMPurify.sanitize('<p>abc<iframe//src=jAva&Tab;script:alert(3)>def</p>'); // becomes <p>abc</p>
```

---

### 3. Frameworks Security:

* Modern frameworks → fewer XSS
* They are secure but they can be used insecurely:
  * Using [escape hatches](https://react.dev/learn/escape-hatches)
    * `dangerouslySetInnerHTML` in React
    * `bypasssecuritytrusthtml` in Angular
    * If these escape hatches are used somewhere directly with user-input data, the value must be sanitized.
  * Template Injection: Some server-side template engines (e.g., Jinja2 in Python, Handlebars in Node.js) can be vulnerable to injection if user input is improperly handled.

    ```python
    # Unsafe usage (Jinja2 example - vulnerable to template injection)
    user_input = request.args.get("name")
    return render_template_string(f"Hello {user_input}")
    
    # Safe usage
    return render_template("greeting.html", name=user_input)
    ```

    :warning: Never pass unsanitized or raw user input into `render_template_string` or any method that evaluates templates directly.
  * Outdated frameworks: Old versions of frameworks may lack critical security patches or default XSS protections.
* Code Examples:

```python
# Unsafe usage
function App() {
    const params = new URLSearchParams(window.location.search);
    const userInput = params.get('msg'); # attacker-controlled input
    return (
        <div dangerouslySetInnerHTML={{ __html: userInput }} />
    );
}
```

```python
# Safe Code using DomPurify
import DOMPurify from 'dompurify'; # sanitize dangerous HTML

function App() {
    const params = new URLSearchParams(window.location.search);
    const userInput = params.get('msg');
    const safeHTML = DOMPurify.sanitize(userInput || '');

    return (
        <div dangerouslySetInnerHTML={{ __html: safeHTML }} />
    );
}
```

---

### 4. Sensitive Functions:

Be careful where and how you use following functions while coding.

<table>
<tr>
<th>Function</th>
<th>Danger</th>
<th>Example</th>
</tr>
<tr>
<td>innerHTML</td>
<td>Parses the input as HTML, allowing script execution.</td>
<td>

`element.innerHTML = userInput;` → `<img src=x onerror=alert(1)>`
</td>
</tr>
<tr>
<td>outerHTML</td>
<td>Same as innerHTML, but replaces the element itself.</td>
<td>

`element.outerHTML = userInput;` → `<script>alert(1)</script>`
</td>
</tr>
<tr>
<td>document.write()</td>
<td>Injects raw HTML into the page; easily abused if user input is involved.</td>
<td>

`document.write(userInput);` → `<script>alert(1)</script>`
</td>
</tr>
<tr>
<td>document.writeln()</td>
<td>Same as document.write(), but adds a newline after the input.</td>
<td>

`document.writeln(userInput);`
</td>
</tr>
<tr>
<td>setAttribute('href', ...)</td>
<td>

Dangerous if an attacker injects a `javascript:` URL.
</td>
<td>

`link.setAttribute('href', userInput);` → `javascript:alert(1)`
</td>
</tr>
<tr>
<td>setAttribute('src', ...)</td>
<td>

Dangerous if `javascript:` or untrusted domains are allowed.
</td>
<td>

`img.setAttribute('src', userInput);`
</td>
</tr>
<tr>
<td>eval()</td>
<td>

Executes any JavaScript string as code. Extremely dangerous with untrusted input. **Never use it if possible.**

> [_“Eval is evil.”_](https://www.geeksforgeeks.org/is-javascripts-eval-evil/) — Some frustrated JavaScript engineer
</td>
<td>

`eval(userInput);` → `alert(1)`
</td>
</tr>
<tr>
<td>setTimeout()</td>
<td>Unsafe if called with a string (acts like eval).</td>
<td>

`setTimeout(userInput, 1000);` → `alert(1)`

`setTimeout("userInput")`
</td>
</tr>
<tr>
<td>setInterval()</td>
<td>Unsafe if called with a string (acts like eval).</td>
<td>

`setInterval(userInput, 1000);`

`setInterval("userInput")`
</td>
</tr>
<tr>
<td>window.location</td>
<td>Unsafe if attacker can control the assigned URL.</td>
<td>

`window.location = userInput;` → `javascript:alert(1)`
</td>
</tr>
<tr>
<td>window.open()</td>
<td>Unsafe if attacker controls the URL; phishing and XSS risks.</td>
<td>

`window.open(userInput);`
</td>
</tr>
<tr>
<td>insertAdjacentHTML()</td>
<td>Inserts raw HTML at a specified position; vulnerable to XSS if input is not sanitized.</td>
<td>

`element.insertAdjacentHTML('beforeend', userInput);` → `<script>alert(1)</script>`
</td>
</tr>
<tr>
<td>Function()</td>
<td>Like eval(), dynamically creates new functions from strings.</td>
<td>

`new Function(userInput)();` → `alert(1)`
</td>
</tr>
<tr>
<td>template literals (unsafe)</td>
<td>If you directly insert user input into a template literal that becomes HTML, XSS is possible.</td>
<td>

``element.innerHTML = `Hello ${userInput}`;``
</td>
</tr>
</table>

