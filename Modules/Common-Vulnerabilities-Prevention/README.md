In this section, we provide security guidelines to help prevent common vulnerabilities and ensure the safety of our projects.

1. Cross-Site Scripting (XSS)
   * What is XSS?
     * Cross-Site Scripting (XSS) is a vulnerability that allows attackers to **inject malicious scripts** into web pages viewed by other users. These scripts can run in the context of a user’s browser, potentially stealing sensitive information like cookies, session tokens, or login credentials, and performing actions on behalf of the user without their consent.
   * [How to prevent XSS?](https://github.com/h0s4in/Security-Guidelines/blob/main/Modules/Common-Vulnerabilities-Prevention/XSS-Prevention.md)
2. Cross-Site Request Forgery (CSRF)
   * What is CSRF?
     * An attack that forces an end user to execute unwanted actions on a web application in which they’re currently authenticated. For most sites, browser requests automatically include any credentials associated with the site, such as the user’s session cookie. so if a user is currently authenticated the application can not distinguish between the forged request and real one.
     * **Impact?** If the victim is a normal user, the impact is forcing user to do state changing requests like transferring funds. If the victim is an administrative account then the impact is much higher and can lead to compromise the entire application.
     * This was just a short explanation, if its your first time hearing about CSRF read [this blog post](https://owasp.org/www-community/attacks/csrf).
   * [How to Prevent CSRF?](https://github.com/h0s4in/Security-Guidelines/blob/main/Modules/Common-Vulnerabilities-Prevention/CSRF-Prevention.md)
3. SQL Injection
   * What is SQLi?
     * Most programmers likely know it, but for those who might have forgotten: SQL Injection is a type of security vulnerability where an attacker can insert (or "inject") malicious SQL code into your database query. This happens when user input is not properly validated or escaped. It can let attackers read, modify, or even delete data in your database.
   * Impact?
     * Most of programmers are aware of the main impact of SQL Injection, which is querying the database and exfiltrating data. but SQL Injection can also leads to:
       * Denial of Service (DOS): If a very heavy query is inserted as the payload.
       * OS Command Execution:
         * FILE privilege enabled -\> write a web shell to the server
         * If `xp_cmdshell` is enabled, attacker can open a Windows Shell and run commands.
   * [How to prevent SQLi?](https://github.com/h0s4in/Security-Guidelines/blob/main/Modules/Common-Vulnerabilities-Prevention/SQLi-Prevention.md)
