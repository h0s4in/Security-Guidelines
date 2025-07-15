* Why to validate input? Ensure safe data is entering the workflow
* What to validate? Apply validations to all input data
* When to validate input? As early as possible (Right after the data is received from an external actor)
* Validation types?
  * Syntatic -\> Ensure that correct syntax is used (Example: correct date format, correct Credit Card format)
  * Symantic -\> Ensure correctness of the values logically (Example: Negative price is not allowed)
* How to validate?
  * Whitelisting & Blacklisting
    * Always prefer allowlist (whitelist) validation
    * Specify exactly which inputs or patterns are acceptable (For example if a field must be an integer, only permit digits)
    * Use Blacklisting only as an _additional_ check
  * Normalization
    * Attackers often exploit alternate encoding or Unicode tricks to bypass filters. Always **normalize** input to a canonical form before validation.
    * Ensure canonical encoding is used across all the text and no invalid characters are present.
    * Allow broad Unicode categories (letters, digits, symbols) rather than specific code points.
      * Why? Because if all code points are allowed, attackers can play with more characters to get something.
      * How? Use regex
    * Always perform validation on the **decoded** text to avoid bypasses
  * Regex
    * Use regex for structured formats (email, phone, ZIP)
    * Avoid using “match anywhere” patterns if not necessary
    * Validate your regex and make sure its safe (Also check OWASP validation regex repo [here](https://owasp.org/www-community/OWASP_Validation_Regex_Repository))
  * Prevent XSS
    * All user controlled data must be encoded when returning in the HTML page. (Example: `<script>` --\> `$lt;script$gt;`)

---

### Secure Coding Examples:

```csharp
// Example: Validate and parse an integer ID
string idInput = GetParameter("id"); 
if (!int.TryParse(idInput, out int id) || id < 0) {
    logger.LogWarning("Invalid ID input: {Input}", idInput);
    throw new ValidationException("Invalid ID.");
}

// Example: Validate string with regex (e.g., alphanumeric code)
var pattern = @"^[A-Za-z0-9]{5,10}$";
if (!Regex.IsMatch(codeInput, pattern)) {
    throw new ValidationException("Code must be 5-10 alphanumeric chars.");
}
```

---

Data Annotations in ASP.NET Core:

```csharp
public class RegisterModel {
    [Required, StringLength(20, MinimumLength=3)]
    public string Username { get; set; }

    [Required, EmailAddress]
    public string Email { get; set; }

    [Range(1, 150)]
    public int Age { get; set; }
}
// In a controller action:
if (!ModelState.IsValid) {
    // Return error response; details from ModelState are not revealed to user.
}
```

---

Output Encoding to prevent XSS:

```csharp
string comment = GetUserComment();
string safeComment = HttpUtility.HtmlEncode(comment);
ViewBag.Comment = safeComment; // Render in view
```
