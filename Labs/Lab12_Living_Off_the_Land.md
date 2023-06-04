<div align='center'>

# **Lab 12: Living Off the Land** 

</div>

## **Env Commands For Host & Network Recon**

`Systeminfo` command will print a summary of the host's information

![](../imgs/Lab/Lab12/1.png)

## **Harnessing PowerShell**

- `Get-Module`: List all the modules that are currently loaded into the PowerShell session

    ![](../imgs/Lab/Lab12/2.png)

- `Get-ExecutionPolicy -List`: Print the execution policy settings for each scope on a host.

    ![](../imgs/Lab/Lab12/3.png)

- `Get-ChildItem Env: | ft Key,Value`: Return environment values such as key paths, users, computer information, etc.

    ![](../imgs/Lab/Lab12/4.png)

## **Downgrade Powershell**

Powershell version 2.0 or older will not be logged in Event Viewer

```powershell
powershell.exe -version 2
```

![](../imgs/Lab/Lab12/5.png)

## **Checking Defenses**

**Firewall Checks**

```powershell
netsh advfirewall show allprofiles
```

![](../imgs/Lab/Lab12/6.png)

![](../imgs/Lab/Lab12/7.png)

**Windows Defender Check**

```powershell
sc query windefend
```

![](../imgs/Lab/Lab12/8.png)

- **Get-MPComputerStatus**: Get the status and configuration settings of Windows Defender on the host

    ![](../imgs/Lab/Lab12/9.png)

    - **Question**: Enumerate the host's security configuration information and return the hosts AMProductVersion.

        **Answer**: 4.18.2109.6

        ![](../imgs/Lab/Lab12/21.png)

## **Network Information**

**arp -a:** Lists all known hosts stored in the arp table.

![](../imgs/Lab/Lab12/10.png)

**Viewing the Routing Table**

```powershell
route print
```

![](../imgs/Lab/Lab12/11.png)

![](../imgs/Lab/Lab12/12.png)

**Windows Management Instrumentation (WMI)**

```powershell
wmic ntdomain get Caption,Description,DnsForestName,DomainName,DomainControllerAddress
```

This command provides the information about the domain and the child domain, and the external forest that our current domain has a trust with

![](../imgs/Lab/Lab12/13.png)

## **net/net1 Commands**

**Listing Domain Groups**

```powershell
net1 group /domain
```

![](../imgs/Lab/Lab12/14.png)

**Information about a Domain User**

```powershell
net1 user /domain wley
```

![](../imgs/Lab/Lab12/15.png)

**Question**: What domain user is explicitly listed as a member of the local Administrators group on the target host?

```powershell
net1 localgroup administrators
```

![](../imgs/Lab/Lab12/22.png)

- **Answer**: adunn

![](../imgs/Lab/Lab12/23.png)

## **Dsquery**

**User Search**

```powershell
dsquery user
```

![](../imgs/Lab/Lab12/16.png)

**Computer Search**

```powershell
dsquery computer
```

![](../imgs/Lab/Lab12/17.png)

**Wildcard Search**

```powershell
dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL"
```

![](../imgs/Lab/Lab12/18.png)

**Users With Specific Attributes Set (Password not required)**

```powershell
dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl
```

![](../imgs/Lab/Lab12/19.png)

**Searching for Domain Controllers**

The below search filter looks for all Domain Controllers in the current domain, limiting to five results.

![](../imgs/Lab/Lab12/20.png)

**Question**: Utilizing techniques learned in this section, find the flag hidden in the description field of a disabled account with administrative privileges. Submit the flag as the answer.

Look for disabled accounts and their description

```powershell
dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=2))" -attr distinguishedName description
```

![](../imgs/Lab/Lab12/24.png)

- **Answer**: HTB{LD@P_I$_W1ld}

![](../imgs/Lab/Lab12/25.png)