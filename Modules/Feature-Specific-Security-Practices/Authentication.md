* Login Mechanisms:
  1. Implement robust brute-force protection (IP-based user rate limiting).
  2. Require CAPTCHA test with every login attempt.
  3. Return generic message for existing/non-existing accounts.
  4. Even if you are using generic messages, Ensure that responses return in a consistent amount of time. this will prevent the [time-based username enumerations](https://www.pentestpartners.com/security-blog/time-based-username-enumeration/) by attackers. 2 logic implementations are mentioned below, one is vulnerable and one not.

```python
# Vulnerable to time based username enumeration
def authenticate(username, password):
    user = db.get_user(username) # This query takes longer if user exists
    if not user:
        return False # Fast response for non-existent users
```

```python
# Safe against time based username enumeration
def authenticate(username, password):
    dummy_hash = generate_dummy_hash() # Create fake hash with similar computation time
    user = db.get_user(username) or User(dummy_credentials) # Same query time for all cases
```

---

* UserIDs
  1. Must be randomly generated (example: UUID v4)
     * Insecure usage of UUID v1 can leads to [critical vulnerabilities](https://www.landh.tech/blog/20230811-sandwich-attack/).
  2. Avoid using predictable and sequential userIDs
  3. Avoid using Personally identifiable information (PII) as userIDs (like National Code, ...)
     * Why? PII of people might be leaked or accessible in some ways.
  4. Apply Rate limit to avoid targeting predictable IDs
  5. Do not expose internal userIDs in URLs (GET query parameters)
     * Why? URLs can be archived by [wayback machine](https://archive.org/)

---

* Usernames
  1. Use Email as username (Verify the email during signup process)
  2. Consider an option for choosing username other than email
  3. Enforce username rules (Avoid special characters like \<\>)

---

* Password Policy
  1. Minimum & Maximum password length.
     * min 8 characters
     * support at least 64 characters
  2. Allow usage of **all** characters
  3. No requirement for upper or lower case or numbers or special characters
  4. Credential rotation when a password leak occurs
     * Use [haveibeenpwned API](https://haveibeenpwned.com/api/v3#PwnedPasswords) for free.
  5. Use client-side password strength meters like [zxcvbn](https://github.com/dropbox/zxcvbn) to help users choose secure password
  6. Enable Multi Factor Authentication (MFA)

---

* Change Password
  1. Make sure user is authenticated with active session (check session expiration)
  2. Require Current password (Prevents CSRF attacks)
     * Re-Authentication for sensitive features like change password, change email, transactions is an important security best practice. this will mitigate CSRF, session hijacking.
  3. Avoid Password reuse by implementing password history
  4. Notify Users of Changes

---

* Password Reset (Forgot Password):
* Stage 1 (Requesting the reset password token or link)
  1. Consider generic error messages and equal timing for existing/non-existing accounts as mentioned in "Login Mechanism".
  2. Implement Rate Limiting, CAPTCHA to prevent attackers flooding the user's email/phone inbox.
  3. Implement input validation in username/email field.
* Stage 2 (Resetting the Password):
  1. Ask the New Password 2 times.
  2. Implement secure password policy (Both client and server side) and check for leaked password. (previously mentioned).
  3. Re-authenticate users after password update even if they are already authenticated.
  4. Ask user (or Automatically) to expire all existing sessions after the password reset.
* General considerations:
  1. Ensure the reset password tokens are Randomly generated using a cryptographically safe algorithm.
  2. Ensure the reset password tokens are Single use and expire after a short time-to-live (TTL).
  3. Implement maximum times that user can request for reset password link/otp.
* Error Messages
  * Application should response with generic error messages to prevent username enumeration.
  * Always return the same HTTP status code with each login request.
  * Generic errors must be implemented in both HTTP and HTML layer.

---

* Active Directory Authentication
* If any application wants to authenticate their users with active directory, important considerations are needed:
  * Use Secure LDAP (LDAPS)
  * Authenticate users by binding with their own credentials.
  * Never hard-code credentials for LDAP bind in the application code.

---
