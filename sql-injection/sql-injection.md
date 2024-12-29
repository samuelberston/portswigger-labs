# SQL injection

## SQL injection in WHERE clause allowing retrieval of hidden data
Injecting an SQL payload into a GET request. 
```
GET /filter?category='+OR+1=1-- HTTP/2
```

## SQL injection vulnerability allowing login bypass
Entering a single quotation mark ' into the password field creates a 500 Internal Server Error which implies this may be exploitable via SQL injection.
Use the payload: **administrator'--** and fill in any password in order to bypass the password check entirely.

## SQL injection action, querying the database version and type on Oracle
