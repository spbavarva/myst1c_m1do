# User Enumeration without credentials

**Kerbrute - Internal AD Username Enumeration**

```markdown
sudo git clone https://github.com/ropnop/kerbrute.git
make help
sudo make all
ls dist/

doshim@htb[/htb]$ echo $PATH
/home/htb-student/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/snap/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/home/htb-student/.dotnet/tools

sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute

kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users
```

[](User%20Enumeration%20without%20credentials%20e3675fa6fd0e4fffa79f4b98f633cc4f/Untitled%204618a13272194e0d95d830a171beecc2.md)