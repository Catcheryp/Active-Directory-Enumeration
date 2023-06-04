<div align='center'>

# **Lab 11: Credentialed Enumeration - from Windows** 

</div>

## **ActiveDirectory PowerShell Module**

**Get-ADDomain:** Print out the domain SID, domain functional level, any child domains, and more

Here is the result

![](../imgs/Lab/Lab11/1.png)

**Get-ADUser:** Gets one or more Active Directory users. We will be filtering for accounts with the ServicePrincipalName property populated. This will get us a listing of accounts that may be susceptible to a Kerberoasting attack

```powershell
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```

Here is the result

![](../imgs/Lab/Lab11/2.png)

**Checking For Trust Relationships:** Print out any trust relationships the domain has

```powershell
Get-ADTrust -Filter *
```

Here is the result

![](../imgs/Lab/Lab11/3.png)

**Group Enumeration**

```powershell
Get-ADGroup -Filter * | select name
``` 

Here is the result

![](../imgs/Lab/Lab11/4.png)

**Detailed Group Info**

```powershell
Get-ADGroup -Identity "Backup Operators"
```

Here is the result

![](../imgs/Lab/Lab11/5.png)

**Group Membership:** Print out the members of the group

```powershell
Get-ADGroupMember -Identity "Backup Operators"
```

We get a user `backupagent`

![](../imgs/Lab/Lab11/6.png)

## **PowerView**

Start PowerView

```powershell
powershell -ep bypass
. .\PowerView.ps1
```

**Domain User Information**

```powershell   
Get-DomainUser -Identity wley -Domain inlanefreight.local
```

Here is the result

![](../imgs/Lab/Lab11/7.png)

**Recursive Group Membership:** Retrieve group-specific information. Adding the `-Recurse` switch tells PowerView that if it finds any groups that are part of the target group (nested group membership) to list out the members of those groups

```powershell
Get-DomainGroupMember -Identity "Domain Admins" -Recurse
```

Here is the result

![](../imgs/Lab/Lab11/8.png)

**Trust Enumeration**

![](../imgs/Lab/Lab11/9.png)

**Testing for Local Admin Access**

```powershell
Test-AdminAccess -ComputerName ACADEMY-EA-MS01
```

Here is the result

![](../imgs/Lab/Lab11/10.png)

- **Question:** What PowerView function allows us to test if a user has administrative access to a local or remote host?

    - **Answer:** Test-AdminAccess

![](../imgs/Lab/Lab11/13.png)

**Finding Users With SPN Set:** Check for users with the SPN attribute set, which indicates that the account may be subjected to a Kerberoasting attack

```powershell
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName
```

Here is the result

![](../imgs/Lab/Lab11/11.png)

## **Snaffler**

Snaffler is a tool that can help us acquire credentials or other sensitive data in an Active Directory environment. Snaffler works by obtaining a list of hosts within the domain and then enumerating those hosts for shares and readable directories. Once that is done, it iterates through any directories readable by our user and hunts for files that could serve to better our position within the assessment.

```powershell
Snaffler.exe -s -d inlanefreight.local -v data
```

Here is the result

![](../imgs/Lab/Lab11/12.png)

We can see a readable web config file which contains sensative information

![](../imgs/Lab/Lab11/14.png)

- **Question:** Run Snaffler and hunt for a readable web config file. What is the name of the user in the connection string within the file?

    - **Answer:** sa

- **Question:** What is the password for the database user?

    - **Answer:** ILFREIGHTDB01!

![](../imgs/Lab/Lab11/15.png)

## **BloodHound/SharpHound**

Collecting information

```powershell
SharpHound.exe -c All --zipfilename ILFREIGHT
```

![](../imgs/Lab/Lab11/16.png)

Load into BloodHound

![](../imgs/Lab/Lab11/17.png)

**Analysis**

- `Find Computers with Unsupported Operating Systems` is great for finding outdated and unsupported operating systems running legacy software

    ![](../imgs/Lab/Lab11/18.png)

- `Find Computers where Domain Users are Local Admin` is great for finding any hosts where all users have local admin rights

    ![](../imgs/Lab/Lab11/19.png)

- **Question:** Using Bloodhound, determine how many Kerberoastable accounts exist within the INLANEFREIGHT domain. (Submit the number as the answer)

    Use the query `List all Kerberoastable Accounts`. We see 13 accounts

    ![](../imgs/Lab/Lab11/20.png)

    - **Answer:** 13

    ![](../imgs/Lab/Lab11/21.png)
    
