## File upload vulnerabilities
1. <b>Remote code execution via web shell upload</b> - Try to upload just a php script with .php extension
2. <b>Web shell upload via Content-Type restriction bypass</b> - Try to change <b>content-type</b> from x-php to <b>image/png</b>
3. <b>Web shell upload via path traversal</b> - Change the <b>filename</b> from burpsuite intercept to <b>..%2fget_flag.php</b> and get the flag to /files/avatars/../get_flag.php
4. <b>Web shell upload via extension blacklist bypass</b> - Change the filename from <b>.php</b> extension to <b>.phtlm</b> extension and upload it
5. <b>Web shell upload via obfuscated file extension</b> - Intercept upload request and change the filename with extension <b>.php%00.png</b> - <b>Null byte technique</b>
6. <b>Remote code execution via polyglot web shell upload</b> - For this lab we have to use <b>exiftool</b> and one random .jpg or .png image. The exploitation part `exiftool -comment="<?php system('badstuff');?>" image_name.png or .jpg -o get_flag.php` after that a php file will created with get_flag.php name upload it and redirect into it to get the flag.
7. <b>Web shell upload via race condition</b> - 

 [More tricks about file upload vulnerabilities](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files#upload-tricks)
