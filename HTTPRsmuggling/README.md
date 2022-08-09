CL.TE: the front-end server uses the Content-Length header and the back-end server uses the Transfer-Encoding header.
TE.CL: the front-end server uses the Transfer-Encoding header and the back-end server uses the Content-Length header.
TE.TE: the front-end and back-end servers both support the Transfer-Encoding header, but one of the servers can be induced not to process it by obfuscating the header in some way.
