<div align='center'>

# **Lab 1: AD Administration** 

</div>

## **Task 1: Manage Users**

### **Add some new users (Andromeda Cepheus, Orion Starchaser, Artemis Callisto)**

- Open **Active Directory Users and Computers** via Start Menu or Server Manager

- Right click on **IT**, Select **New** > **User**

    ![](../imgs/Lab/1.png)

- Enter first name, last name, full name, and logon name

    ![](../imgs/Lab/2.png)

- Enter password and check the boxes like below

    ![](../imgs/Lab/3.png)

- Verify the information and click **Finish**

    ![](../imgs/Lab/4.png)

- Here is the new User

    ![](../imgs/Lab/5.png)

- Right click that new User and view **Properties** and set the email attribute

    ![](../imgs/Lab/6.png)

- Follow the same steps to create the other two users

    ![](../imgs/Lab/7.png)

### **Remove a few user accounts**

- Right click on the Employees OU and select **Find**

    ![](../imgs/Lab/8.png)

- Search for targeted user

    ![](../imgs/Lab/9.png)

- Right click on the user and select **Delete**

    ![](../imgs/Lab/10.png)

- Follow the same steps to delete the other user

    ![](../imgs/Lab/11.png)

### **Unlock a user account**

- Right click on the Employees OU and select **Find**

    ![](../imgs/Lab/8.png)

- Search for targeted user

    ![](../imgs/Lab/12.png)

- Right click on the account and select **Reset Password**

    ![](../imgs/Lab/13.png)

- Set up the new password and check the boxes like below

    ![](../imgs/Lab/14.png)

## **Task 2: Manage Groups and Other Organizational Units**

### **Create A New OU Under I.T.**

- From within the IT OU, Right click and select **New** > **Organizational Unit**

    ![](../imgs/Lab/15.png)

- Enter the name for the OU

    ![](../imgs/Lab/16.png)

- Here is the new OU

    ![](../imgs/Lab/17.png)

### **Creating A Security Group**

- Right click on our new OU **Security Analysts** and select **New** > **Group**

    ![](../imgs/Lab/18.png)

- Enter a Name, scope, and type

    ![](../imgs/Lab/19.png)

- Here is the new group

    ![](../imgs/Lab/20.png)

### **Add Users to the Security Group**

- Right click on the user and select **Add to a group**

    ![](../imgs/Lab/21.png)

- Search for the targeted group

    ![](../imgs/Lab/22.png)

- A user is added to the group

    ![](../imgs/Lab/23.png)

- Follow the same steps to add the other user to the group

    ![](../imgs/Lab/24.png)

## **Task 3: Manage Group Policy Objects**

**Objective:** Duplicate the group policy **Logon Banner**, rename it **Security Analysts Control**, and modify it to work for the new Analysts OU. We will need to make the following changes to the **Policy Object**:

- Modifying the Password policy settings for users in this group and expressly allowing users to access PowerShell and CMD since their daily duties require it.
- For computer settings, we need to ensure the Logon Banner is applied and that removable media is blocked from access.

Once done, make sure the Group Policy is applied to the **Security Analysts** OU

### **Duplicate the Logon Banner Policy**

- Use this command to achieve this: 

```powershell
Copy-GPO -SourceName "Logon Banner" -TargetName "Security Analysts Control"
```

- Here is the result

    ![](../imgs/Lab/25.png)

    ![](../imgs/Lab/26.png)

### **Link the New GPO to an OU**

- Use this command to achieve this: 

```powershell
New-GPLink -Name "Security Analysts Control" -Target "ou=Security Analysts,ou=IT,OU=HQ-NYC,OU=Employees,OU=Corp,dc=INLANEFREIGHT,dc=LOCAL" -LinkEnabled Yes
```
The command will take the new GPO we created, link it to the OU Security Analysts, and enable it.

- Here is the result

    ![](../imgs/Lab/27.png)

### **Modify a GPO**

- Right-click the GPO we wish to modify and select **Edit**

    ![](../imgs/Lab/28.png)

**User Configuration Group Policies**

- Drill down into the User Configuration policies to  **System** > **Removable Storage Access**. Look for the **All Removable Storage classes: Deny all access** policy. Right-click it and select **Edit**.

    ![](../imgs/Lab/29.png)

- Select **Enabled**

    ![](../imgs/Lab/30.png)

- Similarly, the **Prevent access to command prompt** policy are located under **Administrative Templates** > **System**. Disable it.

    ![](../imgs/Lab/31.png)

**Computer Configuration Group Policies**

- Find the policy **Interactive Logon: Message title for users attempting to log on** and **Interactive Logon: Message text for users attempting to log on** like below

    ![](../imgs/Lab/32.png)

- Validate the settings like below

    ![](../imgs/Lab/33.png)

    ![](../imgs/Lab/34.png)

- Next, we will modify the settings for the **Password Policies**. Move into the **Security Settings** hive and click on **Password Policy** under the **Account Policies** dropdown

    ![](../imgs/Lab/35.png)

- Configure the policy like below

    ![](../imgs/Lab/36.png)

    ![](../imgs/Lab/37.png)

    ![](../imgs/Lab/38.png)

    ![](../imgs/Lab/39.png)

    ![](../imgs/Lab/40.png)

- Verify the policy

    ![](../imgs/Lab/41.png)

## **Task 4: Add and Remove Computers To The Domain**

### **Add a Computer to the Domain**

- From the control panel, open up system properties for the pc. Click on Change Settings in the Computer name section.

    ![](../imgs/Lab/42.png)

- In this window, select the Change button beside **Rename this computer or change its domain.**

    ![](../imgs/Lab/43.png)

- Select the Domain radio button and enter the domain name.

    ![](../imgs/Lab/44.png)

- Enter the credentials for the domain admin account.

    ![](../imgs/Lab/45.png)

- Here is the result

    ![](../imgs/Lab/46.png)

### **Check OU Membership of a Host**

- Use this command to achieve this: 

```powershell
Get-ADComputer -Identity "ACADEMY-IAD-W10" -Properties * | select CN,CanonicalName,IPv4Address
```

- Here is the result. We can see the computer is in the **Computers** OU

    ![](../imgs/Lab/47.png)

    ![](../imgs/Lab/48.png)

### **Move a Computer Object to a New OU**

We need to find the new host, and move it to the **Security Analysts** OU

- Select our newly joined host and right click it. Select the option to **Move**

    ![](../imgs/Lab/49.png)

- Select the **Security Analysts** OU

    ![](../imgs/Lab/50.png)

- Here is the result

    ![](../imgs/Lab/51.png)