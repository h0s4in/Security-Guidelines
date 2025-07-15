### Cookies:

* Secure cookies with attributes:
* Best Practice? `Set-Cookie: ID=RANDOM_VALUE_HERE; Path=/; HttpOnly; Secure; SameSite=Strict; Max-Age=3600`
  * Secure
    * Ensures cookie is only sent over HTTPS connections.
    * Prevent man-in-the-middle (MITM) attacks.
  * HttpOnly
    * Prevent JavaScript access to cookies via the DOM `document.cookie` object.
    * Even if the application is vulnerable to XSS, there is no account take over!
  * SameSite
    * Prevents the cookie from being sent with cross-site requests
    * [Prevent](https://www.invicti.com/blog/web-security/same-site-cookie-attribute-prevent-cross-site-request-forgery/) Cross-Site Request Forgery (CSRF) attacks

      | Value | Behavior |
      |-------|----------|
      | Strict | Cookies are sent only in requests originating from the same site |
      | Lax | Cookies are sent with top-level navigation but not with embedded resources (like images or frames). |
      | None | Cookies are sent in all contexts, but require the "Secure" flag to be present. |

    * If cookie issuer doesn't explicitly set a `SameSite` attribute, **Chrome** automatically applies `Lax` restrictions by default.
  * Domain & Path attributes (Restrict where the cookie can be send)
    * Domain -\> Controls which domains can access the cookie.
    * Path -\> Prevents the cookie from being exposed to unrelated parts of the app.
  * Expires (Specifies the exact date/time when the cookie should be deleted.)
    * Limits the window for session hijacking if a cookie is stolen.
    * Prevents old/stale cookies from persisting indefinitely.
    * Use short lifetimes for session cookies, longer ones only if necessary (like "Remember me" Cookies)
* Avoid Storing Sensitive Data in Cookies
  * Store only session identifiers
  * Never store sensitive information directly in cookies.
* Invalidate all open sessions when passwords change

---
