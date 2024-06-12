
got 2 ports open

![[Pasted image 20240511160934.png]]

## 21
- Anon is allowed

![[Pasted image 20240511161155.png]]

## 8081

![[Pasted image 20240511161305.png]]

### Burp - rabbit hole
My first though was this web application was vulnerable to insecure file upload and am sure all pentesters know the drill. First i uploaded a legit PNG image and intercepted the request with burpsuite and forwarded the request to repeater

![[Pasted image 20240511161329.png]]

yes it accept php but it's rabbit hole!

## Initial

Then I visit link provided in ftp
I got **CVE**

![[Pasted image 20240511161512.png]]


With some researched I arrived at payload all the things

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Picture%20ImageMagick/imagetragik1_payload_imageover_reverse_shell_netcat_fifo.png

![[Pasted image 20240511161556.png]]


then I just need to nano it and change IP to my kali IP

now when I upload it I get revshell back!!!

![[Pasted image 20240511161700.png]]


### #Privesc-via-port-forwarding

I get to know that 6666 running internally

![[Pasted image 20240511130925.png]]

Transferred Chisel and started client-server

now i access to 6666 with localhost and I type /root/root.txt and I get flag in binary!

![[Pasted image 20240511162031.png]]

![[Pasted image 20240511162021.png]]

![[Pasted image 20240511162049.png]]