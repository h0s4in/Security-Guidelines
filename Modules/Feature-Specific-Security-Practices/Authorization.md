* Enforce Least Privilege (assigning users only the minimum privileges necessary to complete their job)
* During the design phase (early stages in the SDLC):
  1. List all types of users - resources - operations and assign the privileges based on needs.
  2. Create tests for validating mapped permissions
* After deployment:
  * periodically review permissions in the system.
* Validate Permissions on **each request** regardless of the initiating origin of it (AJAX, Server side, ...)
  * Attackers only need to find one way in. so if a single Access Control check is missed, confidentiality is compromised.
  * Technologies for permission checks in [.NET](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-3.1#authorization-filters), [Java](https://jakarta.ee/specifications/platform/8/apidocs/javax/servlet/Filter.html), [Django](https://docs.djangoproject.com/en/4.0/ref/middleware/)

> Authorization flaws (“Broken Access Control”) are critical: OWASP 2021 ranks them as the most serious web vulnerability

So put more attention on Authorization logic and implementing it.
