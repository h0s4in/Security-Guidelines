# Security-Guidelines
### General Secure coding practices every developer should know:

* Always try keeping the entire process as simple as possible.
* Complex procedures can lead to inconsistent results or worse.
* Avoid using Outdated Dependencies and 3rd party packages. ([Gitlab Dependency scanner](https://docs.gitlab.com/user/application_security/dependency_scanning/), [Dependabot](https://github.com/dependabot/dependabot-core) can help).
* Avoid reinventing the wheel and rely on proven libraries, frameworks, and design patterns.
* Don’t insert code from unknown sources without understanding it.
* Never trust user input—treat it as malicious unless properly validated and sanitized
* Always validate and sanitize user inputs to prevent injection attacks.
* Encode or sanitize output (When displaying user-supplied data apply proper output encoding)
* Remove [dead code](https://jellyfish.co/library/dead-code/) regularly. (sections of code that are executed, but not used, accessed, or referenced).
* Never use default secrets or passwords in production.
* Set security headers (Content-Security-Policy, Strict-Transport-Security, X-Frame-Options, ...)
* Implement proper error handling without exposing sensitive information.
* Encrypt data at rest (user information, databases, backups)
* Rate-limit requests: Throttle repeated requests or implement CAPTCHA
* Hash passwords securely (Store user passwords using strong algorithms (e.g., bcrypt) and unique salts)
* Use secure communication protocols (e.g., HTTPS/TLS) for data in transit.
* Use environment variables while working with Secrets and tokens and avoid hard-coding them.
* Regularly Rotate and Refresh Tokens and secrets. (Take a look at [Vault](https://developer.hashicorp.com/vault)).
* Separate development, testing, and production environments.
* Log and monitor: Record security-relevant events (logins, permission changes, errors)
* Always follow the principle of least privilege (Grant only the minimum access needed for code or users to function properly).

---

### Where Should We Pay the Most Attention?

While it's important to consider security in all parts of an application and be aware of all types of vulnerabilities, focusing on the OWASP Top 10 helps highlight which security risks are most common and critical in today's real-world applications.

![image.png](https://owasp.org/www-project-top-ten/assets/images/mapping.png)

The changes show how evolving frameworks, cloud adoption, and secure defaults have helped reduce certain risks—while highlighting the need to focus more on logic flaws and access issues.

---

### Let's dive a little deeper:

[How to prevent common vulnerabilities?](https://github.com/h0s4in/Security-Guidelines/tree/main/Modules/Common-Vulnerabilities-Prevention)

[Feature Specific security practices](https://github.com/h0s4in/Security-Guidelines/tree/main/Modules/Feature-Specific-Security-Practices)

---

### References:

* https://cheatsheetseries.owasp.org/cheatsheets
* https://jellyfish.co/library/dead-code/
* https://goteleport.com/blog/authentication-best-practices/
* https://frontegg.com/blog/ldap
* https://portswigger.net/web-security/csrf
* https://learn.microsoft.com/en-us/aspnet/core/security/anti-request-forgery?view=aspnetcore-9.0
* https://www.ravro.ir/api/files/documents/SecureProgrammingHandbook.pdf
* [https://learn.microsoft.com/en-us/sql/relational-databases/security/sql-injection](https://learn.microsoft.com/en-us/sql/relational-databases/security/sql-injection?view=sql-server-ver16#:\~:text=Validate%20all%20input)
* [https://xygeni.io/blog/secure-coding-practices-checklist/](https://xygeni.io/blog/secure-coding-practices-checklist/#:\~:text=3,implement%20centralized%20error%20management%20systems)
* [https://snyk.io/blog/preventing-sql-injection-entity-framework](https://snyk.io/blog/preventing-sql-injection-entity-framework/#:\~:text=1%20public%20List%20SearchProduct,ToList%28%29%3B%205)
* https://owasp.org/www-project-top-ten/
* https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions
