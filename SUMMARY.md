# Table of contents

* [🍒 Quick commands to run first](Quick%20commands%20to%20run%20first.md)
* [📁 Transfer files](transfer-files/README.md)
  * [Linux](transfer-files/linux.md)
  * [Windows - download from kali](transfer-files/windows-download-from-kali.md)
  * [Transferring Files with Code](transfer-files/transferring-files-with-code.md)
  * [Using RDP](transfer-files/using-rdp.md)
  * [Windows - upload to kali](transfer-files/windows-upload-to-kali.md)
* [Initial access](initial-access/README.md)
  * [FTP/TFTP](initial-access/ftp-tftp.md)
  * [SMB](initial-access/smb.md)
  * [NFS - 2049, 111](initial-access/nfs-2049-111.md)
  * [DNS - 53](initial-access/dns-53.md)
  * [SMTP - 25 - telnet](initial-access/smtp-25-telnet.md)
  * [SNMP](initial-access/snmp.md)
  * [MYSQL - 3306](initial-access/mysql-3306.md)
  * [MSSQL - 1433](initial-access/mssql-1433.md)
* [🔑 Password attacks](password-attacks/README.md)
  * [Common brute-force](password-attacks/common-brute-force.md)
  * [💓 CrackMapExec](password-attacks/crackmapexec.md)
  * [Attacking SAM](password-attacks/attacking-sam.md)
  * [Attacking LSASS](password-attacks/attacking-lsass.md)
  * [Attacking Active Directory & NTDS.dit](password-attacks/attacking-active-directory-and-ntds.dit.md)
  * [Credential Hunting in Windows](password-attacks/credential-hunting-in-windows.md)
  * [Credential Hunting in Linux](password-attacks/credential-hunting-in-linux.md)
  * [Windows Lateral Movement](password-attacks/windows-lateral-movement/README.md)
    * [Pass the Hash](password-attacks/windows-lateral-movement/pass-the-hash.md)
    * [Pass the Ticket from Windows](password-attacks/windows-lateral-movement/pass-the-ticket-from-windows.md)
    * [Pass the Ticket from Linux](password-attacks/windows-lateral-movement/pass-the-ticket-from-linux.md)

## Priv esc

* [💻 1. Windows Post exploitation](1.%20general%20-%20Windows%20Post%20exploitation.md)
  * [2. file/dir permission](priv-esc/1.-windows-post-exploitation/2.-file-dir-permission.md)
  * [3. WSL](3.%20wsl.md)
  * [4. JuicyPotato](priv-esc/1.-windows-post-exploitation/4.-juicypotato.md)
  * [# Unquoted Service Path](priv-esc/1.-windows-post-exploitation/unquoted-service-path.md)
* [💻 # Linux Post exploitation](upgrade%20shell.md)
  * [Weak file permission](1.%20weak-file-permission.md)
  * [SUDO Misconfigurations](2.%20sudo-misconfigurations.md)
  * [Cron job](3.%20cron-job.md)
  * [SUID/SGID Permission](4.%20suid-sgid-permission.md)

***

* [Pivoting, Tunneling, and Port Forwarding](pivoting-tunneling-and-port-forwarding/README.md)
  * [Dynamic Port Forwarding with SSH and SOCKS Tunneling](pivoting-tunneling-and-port-forwarding/dynamic-port-forwarding-with-ssh-and-socks-tunneling.md)
  * [Reverse Port Forwarding with SSH](pivoting-tunneling-and-port-forwarding/reverse-port-forwarding-with-ssh.md)
  * [Page](pivoting-tunneling-and-port-forwarding/page.md)
* [Active Directory](active-directory/README.md)
  * [Enumeration (external/internal)](active-directory/enumeration-external-internal.md)
  * [LLMNR/NBT-NS Poisoning](active-directory/llmnr-nbt-ns-poisoning.md)
  * [Enumerating & Retrieving Password Policies](active-directory/enumerating-and-retrieving-password-policies.md)
  * [Password Spraying](active-directory/password-spraying.md)
  * [Internal Password Spraying](active-directory/internal-password-spraying.md)
  * [Credential enum](active-directory/credential-enum.md)

## Basics

* [Linux Basic](basics/linux-basic.md)
* [Windows Basic](basics/windows-basic.md)

***

* [🧠 Memory Forensic](memory-forensic.md)
* [🎯 OSCP](IMP%20THINGS.md)
  * [info gathering](OSCP/info-gathering/README.md)
    * [passive](passive.md)
    * [Active - DNS](active-dns.md)
    * [nc - Banner Grabbing](nc-banner-grabbing.md)
    * [SMB](OSCP/info-gathering/smb.md)
    * [SMTP](smtp.md)
  * [Common web attacks](common-web-attacks.md)
* [✅ eJPT](ejpt.md)
* [🕸️ Footprinting](footprinting.md)

## HTB

* [Page 1](htb/page-1.md)

## THM

* [Page 2](Magician.md)
