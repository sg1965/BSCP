# Headers
<b>Content-Length: ? </b> <br>
<b>Transfer-Encoding: chunked </b> <br><br>
*Transfer-Encoding overrides the content-length header

# CL.TE 
the front-end server uses the Content-Length header and the back-end server uses the Transfer-Encoding header. <br>

### Explain CL.TE

# TE.CL
the front-end server uses the Transfer-Encoding header and the back-end server uses the Content-Length header. <br>


# TE.TE 
the front-end and back-end servers both support the Transfer-Encoding header, but one of the servers can be induced not to process it by obfuscating the header in some way. <br>


[video](https://www.youtube.com/watch?v=XdbSYNhRszE&ab_channel=CyberSecurityTV)
