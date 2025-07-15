* Isolate the back-end Database from other servers
* Configure the database to only allow encrypted connections.
* Always require authentication for the database (even for connections from local server)
* Database accounts:
  * Use strong and unique password
  * Use 1 account for 1 application or service
  * Consider least privilege & regular review of permissions
  * Remove user accounts after the application or service is decommissioned
  * Change passwords when staff leaves
* Storing DB credentials
  * DB credentials should never be stored in the application source code
  * Use environment variable
  * When possible, encrypt the credentials
* Database Configurations
  * Run database services under a low privileged user account
  * Remove default accounts and databases
  * Store database logs on a separate disk
  * Configure regular backup
* Software specific (Microsoft SQL Server):
  * Disable xp_cmdshell, xp_dirtree
    * `xp_cmdshell` is a command in Microsoft SQL Server used to execute commands on the server. if this function is enabled mistakenly, an attacker can gain RCE from the SQL Injection vulnerability.
    * `xp_dirtree` Lists directory structure on the server.
