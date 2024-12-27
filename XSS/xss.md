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
    