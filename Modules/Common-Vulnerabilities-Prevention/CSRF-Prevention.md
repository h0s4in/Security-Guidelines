### **1- Token-Based Mitigation**

* Always use your framework’s default CSRF protection (with proper configuration).
  * Example: [.NET CSRF Prevention Guide](https://learn.microsoft.com/en-us/aspnet/core/security/anti-request-forgery?view=aspnetcore-9.0)
* CSRF Tokens must be:
  * Unique per session
  * Secret
  * Unpredictable
* **How to send the token?**
  * **Server → Client**:
    * Embed it in the response payload (e.g., inside a JSON response or HTML page).
  * **Client → Server**:
    * Send it via a **custom header** (Recommended — because custom headers force preflight in CORS).
    * Include it as a **hidden field** in a form.
    * Embed it inside the **request payload** (e.g., JSON body).
    * **Double-Submit Cookie Pattern**:
      * **Signed Double-Submit (Recommended)**:
        * The server signs or encrypts the token to ensure it was generated legitimately.
        * Example in pseudocode:

          ```python
          # Server side
          token = sign(random_token(), secret_key)
          set_cookie("csrf_token", token)
          # Client side sends token in header
          headers = {"X-CSRF-Token": token}
          ```
      * **Naive Double-Submit**:
        * A random value is set both in a cookie and a request parameter, and they must match.
        * Example:

          ```javascript
          // Client: Read token from cookie and send in header
          const csrfToken = document.cookie.match(/csrf_token=(\w+)/)[1];
          fetch('/transfer', {
              method: 'POST',
              headers: {'X-CSRF-Token': csrfToken},
              body: JSON.stringify({amount: 100})
          });
          ```

---

### **2- Disallowing **[**Simple Requests**](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS#simple_requests)**:** 

_(Browsers automatically include cookies in "simple" requests.)_

* Avoid allowing only simple content types (`application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`).
* Add a **custom header** and validate it server-side (forces CORS preflight).
  * Example:

    ```javascript
    fetch('/update-profile', {
        method: 'POST',
        headers: {
            'X-MYSITE-CSRF-HEADER': '1',
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({name: "New Name"})
    });
    ```
* Set a simple header with a value **longer than 128 bytes** to trigger CORS preflight.

---

### **3- Defense In Depth Methods** (Use at least one alongside token-based mitigation)

3.1. **Cookie SameSite Attribute**

* Instructs browsers not to send cookies on cross-site requests unless explicitly allowed.
* SameSite options:
  * `Strict`: Cookies only sent for same-site requests.
  * `Lax`: Cookies sent for top-level navigation (safe actions like GET).
  * `None`: Cookies sent cross-site only if `Secure` is set.
  * Example:

    ```http
    Set-Cookie: session_id=abc123; SameSite=Strict; Secure; HttpOnly
    ```

3.2. **User Interaction Based Mitigation**

* Re-authentication mechanism.
  * Ask for current/old password on change password functionality.
  * Confirm user credentials before fund transfers.
* Use **one-time tokens** for critical actions.

3.3. **Use Standard Headers to Verify Origin**

* Steps:
  1. Check the `Origin` or `Referer` header of incoming requests. (Where is the request coming from?)
  2. Determine the target origin (Where is the request going to?)
  3. Match both of them and verify/deny
     * `Origin == Target Origin` **→** Legitimate request
     * `Origin != Target Origin` **→** Forged request
* Logic:

  ```python
  allowed_origin = "https://mywebsite.com" # In this example the target origin is hard-coded
  origin = request.headers.get('Origin')
  if origin != allowed_origin:
      abort(403)  # Forbidden
  ```
* **Important:** The `Origin` and `Referer` headers are [forbidden](developer.mozilla.org/en-US/docs/Glossary/Forbidden_request_header) (cannot be set by JavaScript in browsers — only by the browser itself) so they are trustworthy indicators for CSRF protection.

> Best practice is to use one of main mitigation and use at least 1 defense in depth methods along side them.

---
