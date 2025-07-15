#### 1. Use Parameterized Queries / Prepared Statements with variable binding

* Bind user inputs as parameters, not string-injected values.
* Force the developer to define all SQL code first and then pass in each parameter.
* Safe C# .NET Prepared Statement Example (Pass parameters to query using `Parameters.Add()`):

  ```c#
  String query = "SELECT account_balance FROM user_data WHERE user_name = ?";
  try {
      OleDbCommand command = new OleDbCommand(query, connection);
      command.Parameters.Add(new OleDbParameter("customerName", CustomerName Name.Text));
      OleDbDataReader reader = command.ExecuteReader();
  // …
  ```

---

#### 2. Avoid Unsafe Dynamic SQL and Stored Procedures

* If you are using stored procedures call them with parameters rather than concatenating strings.

  ```c#
  # Safely passing userName to the sp_GetUserByName stored procedure
  SqlCommand cmd = new SqlCommand("sp_GetUserByName", connection);
  cmd.CommandType = CommandType.StoredProcedure;
  cmd.Parameters.AddWithValue("@UserName", userName);
  ```
* Beware of dynamic SQL inside stored procedures
  * If SQL is constructed by concatenating user input into `EXEC()` or `sp_executesql`
  * Even parameterized stored procedures can introduce injection if they use dynamic SQL internally

---

#### 3. Input Validation and whitelisting

* Validate all input on the server side
* Check type, format, length, and range of every input
  * Example: if an API field should be an integer or a date, reject anything else
* User-controlled names cannot be safely parameterized -\> explicitly check user-input against white listed values
* When table or column names are needed, ideally those values come from the code and not from user parameters.
  * If user-input is being used for the table or column name this is a poor design. solution?
    1. fully rewrite the logic
    2. Map user-input values to an specified white list (Only if rewriting is impossible)

    ```c#
    string tableName;
    switch (input) {
      case "users":   tableName = "Users"; break;
      case "orders":  tableName = "Orders"; break;
      default: throw new Exception("Invalid table");
    }
    string sql = $"SELECT \* FROM {tableName} WHERE ...";
    ```

---

#### 4. Use ORMs and Safe Query Builders

* Object-Relational Mappers (ORMs) generate parameterized and safe queries by default.
* Using high-level query methods (e.g. `dbContext.Products.Where(p => p.Name == name)` ) avoids manual SQL altogether.
* Be careful about raw queries (If you must write raw SQL in an ORM, use the framework’s safe API)
  * Example (EF Core) :
    * `FromSqlInterpolated` -\> Safe
    * `FromSqlRaw` -\> can be [vulnerable to SQLi](https://learn.microsoft.com/en-us/ef/core/querying/sql-queries#:\~:text=The%20FromSql%20%20and%20,See%20below%20for%20more%20details), if improperly used

    ```c#
    var users = dbContext.Users
      .FromSqlRaw($"SELECT * FROM Users WHERE Name = '{userInput}'")
      .ToList();
    ```

    ```c#
    var users = dbContext.Users
      .FromSqlInterpolated($"SELECT * FROM Users WHERE Name = {userInput}")
      .ToList();
    ```

---

#### 5. Least Privilege

* Restrict database permissions (Grant the minimal permissions necessary for each account​)
  * Example: if an application only needs to read data, use a read-only user
* Never use a DBA or owner account in an application
* Use separate credentials for different web apps and operations
* Run the database server itself with minimal OS privileges

```sql
-- Read-only user (can only view data)
GRANT SELECT ON customers TO app_readonly_user;

-- Write-only user (can insert data but not read or modify)
GRANT INSERT ON feedback TO app_writeonly_user;

-- Read/write user (only on specific table)
GRANT SELECT, INSERT, UPDATE ON orders TO app_orders_user;

-- No access to DROP, ALTER
```

* Avoid granting ALL privileges or using `GRANT ALL` unless absolutely necessary.

---

#### 6. Additional Practices

* Disable _multiple SQL statements per query_ if possible
  * Example: `; DROP TABLE ...`
  * why? prevent [stacked queries](https://www.aptive.co.uk/blog/what-is-stacked-queries-sql-injection/) attacks
* Use a Web Application Firewall (WAF) as an extra layer (detect SQL injection patterns in HTTP requests)
* Monitor database activity logs for suspicious queries
* Keep your database and framework libraries up to date

---

#### 7. Safe vs Unsafe Code Examples:

```csharp
// vulnerable if searchTerm contains SQL syntax
var query = $"SELECT * FROM Products WHERE Name LIKE '%{searchTerm}%'";
var results = context.Products.FromSqlRaw(query).ToList();
```

```
// SAFE: uses LINQ, which is parameterized by EF
var results = context.Products
                    .Where(p => p.Name.Contains(searchTerm))
                    .ToList();
```
