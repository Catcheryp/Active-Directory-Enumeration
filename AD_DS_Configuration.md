<div align='center'>

# **Active Directory Domain Services Configuration**

</div>

## **1. Get Active Directory Domain Services**

We will use Window Server 2022 OS for this lab. The following steps are required to configure Active Directory Domain Services (AD DS) on a Windows Server 2022 machine.

- Open Server Manager and click on **Add roles and features**.

![](./imgs/1.png)

- The **Before you begin** wizard appears. Click **Next** to continue
- The **Select installation type** section appears; leave the default options and click Next.
**Note:** Ensure that the **Role-based or feature-based installation** radio-button is selected.

![](./imgs/2.png)

- The **Select destination server** section appears. Choose the **Select a server from the server pool** radio button and click Next.

![](./imgs/3.png)

- Check **Active Directory Domain Services** from the **Roles** section in the **Select Server Roles** wizard and click **Next**.
**Note:** If the Add Roles and Features Wizard pop-up window appears, click Add Features.

![](./imgs/4.png)

- In the **Select features** section, click **Next** to continue
- The **Active Directory Domain Services** section appears; click **Next** to continue
- The **Confirm installation selections** section appears; check the **Restart the destination server automatically if required** option and click **Install**.
Note: If the **Add Roles and Features** Wizard pop-up window appears, click **Yes**.

![](./imgs/5.png)

- In the **Server Manager** window under **Dashboard**, click the **Flag** icon and click the **Promote this server to a domain controller** link, as shown in the screenshot

![](./imgs/6.png)

- The **Active Directory Domain Services Configuration Wizard** window appears. In the
**Deployment Configuration** section, select the **Add a new forest** radio button and type
**CMC.com** in the **Root domain name** field; click **Next**

![](./imgs/7.png)

- In the **Domain Controller Options** section, enter the password

![](./imgs/8.png)

- The **DNS Options** section appears. Ignore any alerts and click **Next**
- The **Additional Options** section appears; verify that the NetBIOS domain name is **CMC** and click Next.

![](./imgs/9.png)

- In the **Paths** section, click **Next**.
- The **Review Options** section appears; review your selection, and click **Next**

![](./imgs/10.png)

- Wait until the process finishes, and then click **Install** in the **Prerequisites Check** section

![](./imgs/11.png)

- Finally, here is my network configuration

![](./imgs/15.png)

## **2. Get the window workstation**

We will use Window 10 OS for this lab.

- This is my network configuration

![](./imgs/16.png)

- Go to **Access work or school** and click **Connect**

![](./imgs/12.png)

- Click **Join this device to local Active Directory domain**

![](./imgs/13.png)

- Enter the domain name **CMC.com**

![](./imgs/14.png)

- Enter the username and password of the domain administrator

![](./imgs/18.png)

- The **Add an account** wizard appears. Click **Skip** and restart the machine

![](./imgs/19.png)

- After restarting the machine, log in with the domain administrator account

![](./imgs/20.png)