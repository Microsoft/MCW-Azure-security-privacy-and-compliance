![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Security baseline on Azure
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
August 2019
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only, and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third-party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2019 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are the property of their respective owners.

**Contents**

<!-- TOC -->

- [Security baseline on Azure hands-on lab step-by-step](#security-baseline-on-azure-hands-on-lab-step-by-step)
    - [Abstract and learning objectives](#abstract-and-learning-objectives)
    - [Overview](#overview)
    - [Solution architecture](#solution-architecture)
    - [Requirements](#requirements)
    - [Exercise 1: Implementing Just-In-Time (JIT) access](#exercise-1-implementing-just-in-time-jit-access)
        - [Task 1: Setup virtual machine with JIT](#task-1-setup-virtual-machine-with-jit)
        - [Task 2: Perform a JIT request](#task-2-perform-a-jit-request)
    - [Exercise 2: Securing the Web Application and database](#exercise-2-securing-the-web-application-and-database)
        - [Task 1: Setup the database](#task-1-setup-the-database)
        - [Task 2: Test the web application solution](#task-2-test-the-web-application-solution)
        - [Task 3: Utilize data masking](#task-3-utilize-data-masking)
        - [Task 4: Utilize column encryption with Azure Key Vault](#task-4-utilize-column-encryption-with-azure-key-vault)
    - [Exercise 3: Migrating to Azure Key Vault](#exercise-3-migrating-to-azure-key-vault)
        - [Task 1: Create an Azure Key Vault secret](#task-1-create-an-azure-key-vault-secret)
        - [Task 2: Create an Azure Active Directory application](#task-2-create-an-azure-active-directory-application)
        - [Task 3: Assign Azure Active Directory application permissions](#task-3-assign-azure-active-directory-application-permissions)
        - [Task 4: Install or verify NuGet Package](#task-4-install-or-verify-nuget-package)
        - [Task 5: Test the solution](#task-5-test-the-solution)
    - [Exercise 4: Securing the network](#exercise-4-securing-the-network)
        - [Task 1: Test network security group rules \#1](#task-1-test-network-security-group-rules-\1)
        - [Task 2: Configure network security groups](#task-2-configure-network-security-groups)
        - [Task 3: Test network security group rules \#2](#task-3-test-network-security-group-rules-\2)
        - [Task 4: Install network watcher VM extension](#task-4-install-network-watcher-vm-extension)
        - [Task 5: Setup network packet capture](#task-5-setup-network-packet-capture)
        - [Task 6: Execute a port scan](#task-6-execute-a-port-scan)
    - [Exercise 5: Azure Sentinel logging and reporting](#exercise-5-azure-sentinel-logging-and-reporting)
        - [Task 1: Create a Dashboard](#task-1-create-a-dashboard)
        - [Task 2: Create an Analytics alert](#task-2-create-an-analytics-alert)
        - [Task 3: Investigate a custom alert case](#task-3-investigate-a-custom-alert-case)
        - [Task 4: Create and run a playbook](#task-4-create-and-run-a-playbook)
        - [Task 5: Execute Jupyter Notebooks](#task-5-execute-jupyter-notebooks)
        - [Task 6: Creating Reports with Power BI](#task-6-creating-reports-with-power-bi)
    - [Exercise 6: Using Compliance Tools (Azure Policy, Secure Score and Compliance Manager)](#exercise-6-using-compliance-manager)
        - [Task 1: Review a basic Azure Policy](#task-1-review-a-basic-azure-policy)
        - [Task 2: Review and create Azure Blueprints](#task-2-review-and-create-azure-blueprint)
        - [Task 3: Secure Score](#task-3-secure-score)
        - [Task 4: Use Compliance Manager for Azure](#task-4-use-compliance-manager-for-azure)
    - [After the hands-on lab](#after-the-hands-on-lab)
        - [Task 1: Delete resource group](#task-1-delete-resource-group)
        - [Task 2: Remove Standard Tier Pricing](#task-2-remove-standard-tier-pricing)
        - [Task 3: Delete lab environment (optional)](#task-3-delete-lab-environment-optional)
    - [Appendix A](#appendix-a)
        - [Task 1: Create storage account](#task-1-create-storage-account)
        - [Task 2: Create virtual networks](#task-2-create-virtual-networks)
        - [Task 3: Create virtual machines](#task-3-create-virtual-machines)
        - [Task 4: Create network security groups](#task-4-create-network-security-groups)
        - [Task 5: Azure SQL server](#task-5-azure-sql-server)
        - [Task 6: Create an Azure key vault](#task-6-create-an-azure-key-vault)

<!-- /TOC -->

# Security baseline on Azure hands-on lab step-by-step 

## Abstract and learning objectives 

In this hands-on lab, you will implement many of the Azure Security Center features to secure their cloud-based Azure infrastructure (IaaS) and applications (PaaS). Specifically, you will ensure that any internet exposed resources have been properly secured and any non-required internet access disabled. Additionally, you will implement a “jump machine” for administrators with Application Security enabled to prevent those same administrators from installing non-approved software and potentially exposing cloud resources. You will then utilize custom alerts to monitor for TCP/IP Port Scans to fire alerts.

At the end of this hands-on lab, you will be better able to design and build secure cloud-based architectures, and to improve the security of existing applications hosted within Azure.

## Overview

Contoso is a multinational corporation, headquartered in the United States that provides insurance solutions worldwide. Its products include accident and health insurance, life insurance, travel, home, and auto coverage. Contoso manages data collection services by sending mobile agents directly to the insured to gather information as part of the data collection process for claims from an insured individual. These mobile agents are based all over the world and are residents of the region in which they work. Mobile agents are managed remotely, and each regional corporate office has a support staff responsible for scheduling their time based on requests that arrive to the system.

They are migrating many of their applications via Lift and Shift to Azure and would like to ensure that they can implement the same type of security controls and mechanisms they currently have. They would like to be able to demonstrate their ability to meet compliance guidelines required in the various countries/regions they do business. They have already migrated a web application and database server to their Azure instance and would like to enable various logging and security best practices for administrator logins, SQL Databases, and virtual network design.

## Solution architecture

Contoso administrators recently learned about the Azure Security Center and have decided to implement many of its features to secure their cloud-based Azure infrastructure (IaaS) and applications (PaaS). Specifically, they want to ensure that any internet exposed resources have been property secured and any non-required internet access disabled. They also decided that implementing a "jump machine" for admins with Application Security was also important as they have had instances of admins installing non-approved software on their machines and then accessing cloud resources. Additionally, they want the ability to be alerted when TCP/IP Port Scans are detected, and fire alerts based on those attacks.

![This diagram shows external access to Azure resources where Just In Time is utilize to lock down the Jump Machine. Azure Log Analytics with Azure Sentinel is then used to monitor the deny events on the network security groups.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image2.png)

The solution begins by creating a jump machine. This jump machine is used to access the virtual machines and other resources in the resource group. All other access is disabled via multiple **virtual networks**. More than one virtual network is required as having a single **virtual network** would cause all resource to be accessible based on the default currently un-customizable security group rules. Resources are organized into these virtual networks. **Azure Center Security** is utilized to do **Just-In-Time** access to the jump machine. This ensures that all access is audited to the jump machine and that only authorized IP-addressed are allowed access, this prevents random attacks on the virtual machines from bad internet actors. Additionally, applications are not allowed to be installed on the jump machine to ensure that malware never becomes an issue. Each of the virtual network and corresponding **network security groups** have logging enabled to record deny events to **Azure Logging**. These events are then monitored by a **custom alert rule** in **Azure Sentinel** to fire **custom alerts**. Once the solution is in place, the **Compliance Manager** tool is utilized to ensure that all GDPR based technical and business controls are implemented and maintained to ensure GDPR compliance.

## Requirements

1. Microsoft Azure subscription must be pay-as-you-go or MSDN.

    - Trial subscriptions will not work.
    
2. A machine with the following software installed:

    - Visual Studio 2017
    - Azure Data Studio + the SQL Server Dacpac (`microsoft.dacpac`) extension
    - Power BI Desktop

## Exercise 1: Implementing Just-In-Time (JIT) access

Duration: 15 minutes

Synopsis: In this exercise, attendees will secure a Privileged Access Workstation (PAW) workstation using the Azure Security Center Just In Time Access feature.

### Task 1: Setup virtual machine with JIT

1.  In a browser, navigate to your Azure portal (<https://portal.azure.com>).

2.  Select **Security Center,** then select **Just in time VM access**.

    ![Security Center is highlighted on the left side of the Azure portal, and Just in time VM access (Preview) is highlighted to the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image9.png)

> **Note**: Your subscription may not be set up with the **Standard** tier; if that is the case then do the following:

   a.  In the **Security Center** blade, select **Security Policy**.

   b.  For your subscription, select **Edit settings**

   c.  Select **Pricing Tier**

   d.  Select **Standard**

   e.  Select **Save**

   f.  Navigate back to Security Center, select **Just in time VM access**.


3.  Select the **Recommended** tab, and then check the checkbox to select the lab vms (db-1, paw-1 and web-1), and then select the **Enable JIT on 3 VMs** link.

> **Note**: It could take up to 10 minutes for new VMs to show up if you upgraded to standard tier security.  Also note that it is possible new VMs display in the **No recommendation** tab until a backend process moves them to the **Recommended** tab.  In you find the VMs do not show up after 10 minutes, you can manually enable JIT by clicking the **Configuration** tab in the VMs configuration blade and then clicking **Enable JIT Access**

![Configuration and Enable JIT Access is highlighted in the Azure portal.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image119.png "Enable JIT")

4.  In the configuration window that opens, review the settings, then select **Save**.

    ![In the configuration window, port settings are listed, and Save is highlighted above them.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image10.png "Select Save")

5.  You should now see the states change to **Resolved**.  

> **Note**: It could take a couple minute for this to revert to the resolved state.
    
   ![On the Virtual machines screen, several virtual machines have their State listed as Resolved.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image11.png "View the state change")

### Task 2: Perform a JIT request

1.  Select the **Configured** tab. You should now see all the machines listed.

2.  Select the **paw-1** virtual machine, and then select **Request access**.

    ![On the Virtual machines screen, the first listed virtual machine name is selected and highlighted (paw-1), as is Request access above it.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image12.png "Request access for paw-1")

3.  For each of the ports, select the **On** toggle button, notice how the default IP settings is **My IP**.

    ![On is highlighted under Toggle for all four of the ports listed under paw-1.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image13.png "Select on for each of the ports")

4.  At the bottom of the dialog, select **Open ports**. After a few moments, you should now see the **APPROVED** requests have been incremented and the **LAST ACCESS** is set to **Active now.**.

    ![On the Virtual machines screen, 1 Requests and Active now are highlighted under Aproved and Last Access next to the paw-1 virtual machine.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image14.png "View Approved and Last Access status")

5.  Select the ellipses, then select **Activity Log**, you will be able to see a history of who requests access to the virtual machines.

    ![Activity Log is highlighted in the shortcut menu for the last user.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image15.png "View the Activity Log")

6.  In the Azure Portal main menu, select **All Services**, then type **Network**, then select **Network Security Group**:

![Network security groups is highlighted on the left side of the Azure portal, and paw-1-nsg is highlighted to the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image16.png "Select paw-1-nsg")

7.  Select the **paw-1-nsg** network security group.

8.  Select **Inbound security rules.** You should now see a set of inbound security rules set up by JIT Access.

    ![The first four listed items are highlighted under Inbound security rules.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image17.png "View the inbound security rules set up by JIT Access")

## Exercise 2: Securing the Web Application and database

Duration: 45 minutes

Synopsis: In this exercise, attendees will utilize Azure SQL features to data mask database data and utilize Azure Key Vault to encrypt sensitive columns for users and applications that query the database.

### Task 1: Setup the database

1.  Switch to your Azure portal, select **All Services** then search for **SQL Servers**.  Select **SQL Servers**:

    ![More services is highlighted on the left side of the Azure portal, and SQL servers is highlighted to the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image18.png "Select SQL Servers")

2.  Select the **Azure SQL** database server you created using the Azure Manager template (Ex:  AzureSecurity-INIT).

3.  Select **SQL Databases**, then select the **SampleDB** database.

    ![SQL databases is selected under Settings on the left, and at right, Insurance is selected.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image19.png "Select the SampleDB database")

4.  In the summary section, select the **Show database connection strings**.

    ![Connection strings: Show database connection strings is highlighted in the summary section.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image20.png "Select the Show database connection strings")

5.  Take note of the connection string for later in this lab, specifically the **Server** parameter:

    ![The Server parameter is listed under ADO.NET (SQL authentication) on the ADO.NET tab.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image21.png "Note the Server parameter")

6.  Open **Azure Data Studio**.

7.  Enter the database server name from above.

8.  Enter the username and password used from the Azure Template deployment (**wsadmin** - **p\@ssword1rocks**).

    > **Note**: If you changed the username and password in the ARM template deployment, use those values instead.

    ![The information above is entered in the Connect to Server dialog box, and Connect is highlighted at the bottom.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image22.png "Sign in to the SQL Server Database Engine")

9.  Select **Connect**, in the **New Firewall Rule** dialog, select **Sign In**.

10. Sign in with your resource group owner credentials.

11. In the dialog, select **OK**, notice how your IP address will be added for connection.

    ![New Firewall Rule Dialog with your Internet IP Address](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image23.png)

12. Right-click **Databases**, and select **Data-tier Application wizard**.

    ![Introduction is highlighted on the left side of the Import Data-tier Application dialog box, and Next is highlighted at the bottom.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image24.png "Select Import Data-tier Application")

13. In the Introduction dialog, selct **Create a database from a .bacpac file [Import Bacpac]** and then select **Next**.

14. In the **File location** field, navigate to the extracted **Database** directory, and select the **Insurance.bacpac** file.

    ![Insurance.bacpac is selected in the Browse dialog box.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image25.png "Select Insurance.bacpac")

15. Select **Next**.

16. On the **Summary** dialog, select **Import** and the database will deploy to Azure.

17. Once completed (it could take ~2min), you should be able to see the **Insurance** database successfuly imported.

    ![Results is highlighted on the left side of the Import Data-tier Application dialog box, and at right, many items are listed under Operation Complete. Next is highlighted at the bottom.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image26.png "View the results")

18. In **Azure Data Studio**, select **File-\>New Query**.

    ![In Azure Data Studio, New Query is selected in the File menu.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image27.png "Open a file")

19. Browse to the extracted GitHub folder, copy the content of the **\\Hands-on lab\\Database\\00\_CreateLogin.sql** file.

20. Ensure that the **master** database is selected.

21. Run the script to create a login called **agent**.

22. Browse to the extracted folder, select the **\\Hands-on lab\\Database\\01\_CreateUser.sql** file.

23. Ensure that the **Insurance** database is selected.

24. Run the script to create a non-admin user called **agent**.

### Task 2: Test the web application solution

1.  In the extracted directory, double-click the **/WebApp/InsuranceAPI/InsuranceAPI.sln** solution file, and Visual Studio will open.

2.  In the **Solution Explorer**, navigate to and double-click the **web.config** file to open it.

    ![Web.config is highlighted under InsuranceAPI in Solution Explorer.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image28.png "Open Web.config")

3.  Update the web.config (line 77) to point to the **Insurance** database created in Task 2. You should only need to update the server name to point to your Azure SQL Server.

    ![Line 72 of the Insurance database is highlighted.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image29.png "Update the server name in Web.config")

4.  Run the **InsuranceAPI** solution and press **F5**.

> **Note** If you get an error, right-click the project, select **Clean**, then right-click the project and select **Rebuild**

5.  In the browser window that opens, browse to [http://localhost:portno/api/Users/E91019DA-26C8-B201-1385-0011F6C365E9](http://localhost:portno/api/Users/E91019DA-26C8-B201-1385-0011F6C365E9) you should see a json response that shows an unmasked SSN column.

> **Note**: Depending on your browser, you may need to download to view the json response.

   ![The json response is displayed in a browser window.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image30.png "View the json response")

### Task 3: Utilize data masking

1.  Switch to the Azure Portal.

2.  Select **SQL databases**.

3.  Select the **Insurance** database.

4.  Under **Security**, select **Dynamic Data Masking**, then select **+Add Mask**.

    ![Dynamic Data Masking is highlighted on the left, and +Add mask is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image31.png "Select +Add mask")

5.  Select the **User** table.

6.  Select the **SSN** column.

7.  Select **Add**.

    ![Add is highlighted at the top of the SSN column, and the User table and SSN column are highlighted below.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image32.png "Select Add")

8.  Select **Save**.

9.  Switch back to your InsuranceAPI solution, press **F5** to refresh the page. You should see the SSN column is now masked with **xxxx**.

    ![The masked SSN column is highlighted in the InsuranceAPI solution.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image33.png "View the masked SSN column")

10. Close **Visual Studio**.

### Task 4: Utilize column encryption with Azure Key Vault

1.  Switch to **SQL Management Studio**.

2.  In the extracted directory, navigate to the **Database** directory.

3.  Open the **02\_PermissionSetup.sql** file, copy and paste the TSQL to the Query Window.

4.  Switch to the **Insurance** database, and execute the SQL statement.

5.  In the **Object Explorer**, expand the **Insurance** node.

6.  Expand the **Tables** node.

7.  Expand the **User** table node.

8.  Expand the **Columns** node.

9.  Right-click the **SSN** column, and select **Encrypt Column**.

    ![Tables and dbo.User are highlighted in the Insurance database tree. Below that, the SSN column is selected and highlighted, and Encrypt Column is highlighted.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image34.png "Select Encrypt Column")

    Notice that the State of the column is such that you cannot add encryption (data masking):

    ![A slashed circle appears in the State column.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image35.png "View data masking")

10. Select **Cancel**.

11. Switch back to the Azure Portal, and select the User.SSN data masking.

12. Select **Delete**.

    ![The Delete icon is highlighted under Edit Masking Rule in the Azure portal.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image36.png "Select Delete")

13. Select **Save**.

14. Switch back to **SQL Management Studio**.

15. Right-click the **SSN** column, and select **Encrypt Column**.

16. Check the checkbox next to the **SSN** column.

17. For the **Encryption Type**, and select **Deterministic**.

    ![The check box next to the SSN column is selected and highlighted, and Deterministic is highlighted under Encryption Type.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image37.png "Select Deterministic")

18. Select **Next**.

19. For the encryption select **Azure Key Vault** in the dialog.

    ![Azure Key Vault is selected and highlighted under Select the key store provider.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image38.png "Select Azure Key Vault")

20. Select **SignIn**.

21. Sign in with your Azure Portal credentials.

22. Select your Azure Key Vault.

23. Select **Next**.

24. On the **Run Settings**, select **Next**.

25. Select **Finish**, and the configured will start.

    > **Note**: You may receive a "wrapKey" error. If so, ensure that your account has been assigned that permission in the Azure Key Vault.

    ![Generate new column master key CMK\_Auto1 in Azure Key Vault paassecurity is highlighted with a green check mark at the top of the Task Summary list.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image39.png "View the task summary")

    a.  Select **Key vault**.

    b.  Select your key vault.

    c.  Select **Access policies**.

    d.  Select **Add New**.

    e.  For the principal, select your account.

    f.  Select **Key permissions**, and select **Select all**.

    ![Select all is selected and highlighted under Key permissions, and below that, Decrypt, Encrypt, Unwrap Key, Wrap Key, Verify, and Sign are selected and highlighted under Cryptographic Operations amid the other selected options.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image40.png "Select all")

    g.  Select **Secret permissions**, and select **Select all**.

    h.  Select **Certificate permissions**, and select **Select all**.

    i.  Select **OK**.

    j.  Select **Save**.

    k.  Retry the operation.

    > **Note**: If you are still getting errors (such as Access Denied), ensure that you have selected the correct subscription and Key Vault.

    ![Results is highlighted on the left side of the Always Encrypted dialog box, and at right, Performing encryption operations is selected under Summary: Task. Performing encryption operations has a green check mark and is listed as Passed under Details.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image41.png "View the task results")

26. Select **Close**.

27. Right-click the **User** table, and select **Select top 1000 rows**.

    ![The User table is selected, and Select Top 1000 Rows is selected in the shortcut menu.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image42.png "Select the top 1000 rows")

    You will notice the SSN column is encrypted based on the new Azure Key Vault key.

    ![The value under UserId is selected on the Results tab.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image43.png "Notice the SSN column")

28. Switch to the Azure Portal.

29. Select **Key Vaults**.

30. Select your Azure Key Vault, and then select **Keys**. You should see the key created from the SQL Management Studio displayed:

    ![CloudSecurityVault is selected on the left, Keys is selected and highlighted under Settings in the middle, and CMKAuto1 is selected and highlighted under Unmanaged on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image44.png "Select your Azure Key Vault")

## Exercise 3: Migrating to Azure Key Vault

Duration: 30 minutes

Synopsis: In this exercise, attendees will learn how to migrate web application to utilize Azure Key Vault rather than storing valuable credentials (such as connection strings) in application configuration files.

### Task 1: Create an Azure Key Vault secret

1.  Switch to your Azure Portal.

2.  Select **Key Vaults**, then select your Azure Key Vault.

    ![Key vaults is highlighted on the left side of the Azure portal, and CloudSecurityVault is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image45.png "Select your Azure Key Vault")

3.  Select **Secrets**, then select **+Generate/Import**.

    ![Secrets is highlighted on the left side of the Azure portal, and Generate/Import is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image45.1.png "Create a new secret")

4.  For the **Upload Options**, select **Manual**.

5.  For the **Name**, enter **InsuranceAPI**.

6.  For the **Value,** copy the connection string information from the InsuranceAPI solution web.config file in Exercise 2.

7.  Select **Create**.

8.  Select **Secrets**.

9. Select **InsuranceAPI**.

10. Select the current version.

    ![The current version is selected with a status of Enabled under InsuranceAPI Versions.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image46.png "Select the current version")

11. Copy and record the secret identifier URL for later use:

    ![The Secret Identifier URL is highlighted under Properties.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image47.png "Copy and record the secret identifier URL")

### Task 2: Create an Azure Active Directory application

1.  In the Azure Portal, select **Azure Active Directory**, then select **App Registrations**.

    ![Azure Active Directory is highlighted on the left side of the Azure portal, and App registrations is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image48.png "Select App registrations")

2.  Select **+New application registration**.

3.  For the user-facing display name, type **AzureKeyVaultTest**.

4.  For the supported accounts, select **Accounts in this organization directory only...**

5.  For the Redirect URL, type <http://localhost:12345>.

    ![AzureKeyVaultTest is entered in the Name box, and http://localhost:12345 is entered in the Sign-on URL box under Create.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image49.png "Create a new application registration")

6.  Select **Register**.

7.  Copy and record the **Application ID** for later use.

    ![The Application ID and Object ID are highlighted under Essentials for the AzureKeyVaultTest application, and All settings is selected at the bottom.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image50.png "Copy and record the Application ID and Object ID")

8.  Select **Certificates and secrets**.

9. Under **Client secrets**, select **New client secret**.

10. For the description, enter **InsuranceAPI**.

11. For the Expires, select **In 1 year**.

12. Select **Add**.

13. Copy and record the key value for later use.

### Task 3: Assign Azure Active Directory application permissions

1.  Switch back to Azure Portal and select your Azure Key Vault.

2.  Select **Access Policies**.

3.  Select **+ Add Access Policy**.

    ![Access policies is selected and highlighted under Settings on the left side of your Azure Key Vault, and is +Add New is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image51.png "Add a new access policy")

4.  Select **Select principal**, type **AzureKeyVaultTest**.

5.  Select the **Secret permissions** drop-down, check the **Get** and **List** permissions.

    ![The information above is entered in the Select principal dialog box.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image52.png "Configure Select prinicipal settings")

6.  Select the application service principal, select **Select**.

7.  Select **Add**.

8.  Select **Save**.

### Task 4: Install or verify NuGet Package

1.  Close the previous Visual Studio solution, then from the extracted GitHub directory, open the **\\Hands-on lab\\WebApp\\InsuranceAPI\_KeyVault\\InsuranceAPI.sln** solution.

>**Note**:  Be sure you re-open the correct solution.

2.  Switch to **Visual Studio**.

3.  In the menu, select **View-\>Other Windows-\>Package Manager Console**.

4.  In the new window that opens, run the following commands:

    a.  Install-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform

    b.  Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    c.  Install-Package Microsoft.Azure.KeyVault

> **Note**: These already exist in the project but are provided as a reference.

5.  From **Solution Explorer**, double-click the **web.config** file to open it.

    Notice the **appSettings** section has some token values:

    ![Some token values are highlighted in the appSettings section of the Web.config file.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image53.png "Note the token values")

6.  Replace the **ClientId** and **ClientSecret** with the values from Task 2.

7.  Replace the **SecretUri** with the Azure Key Vault secret key Uri from Task 1.

8.  Save the file.

### Task 5: Test the solution

1.  Open the **web.config**, and comment out or delete the **connectionString** from the file at line 78.

2.  Open the **global.asax.cs** file, and place a break point at line 28.

> **Note**: This code makes a call to get an accessToken as the application you set up above, then make a call to the Azure Key Vault using that accessToken.

3.  Run the solution, and press **F5**.

    You should see that you execute a call to Azure Key Vault and get back the secret (which in this case is the connection string to the Azure Database).

    ![The connection string to the Azure Database is visible.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image54.png "View the connection string")

4.  Press **F5** to continue the program

5.  Navigate to [http://localhost:portno/api/Users](http://localhost:portno/api/Users), you should get an error. Because you encrypted the column in the previous exercise, EntityFramework is not able to retrieve the value. You would need to:

    a.  Run the **\\Hands-on lab\\Database\\02\_PermissionSetup.ps1** script
    b.  Add the [AzureKeyVaultProvider for Entity Framework](https://blogs.msdn.microsoft.com/sqlsecurity/2015/11/10/using-the-azure-key-vault-key-store-provider-for-always-encrypted/) reference to the project and then register the provider code in order for .NET to handle the encrypted column and add the "Column Encryption Setting=Enabled" to the connection string.

## Exercise 4: Securing the network

Duration: 45 minutes

Synopsis: In this exercise, attendees will utilize Network Security Groups to ensure that virtual machines are segregated from other Azure hosted services and then explore the usage of the Network Packet Capture feature of Azure to actively monitor traffic between networks.

### Task 1: Test network security group rules \#1

1.  In the Azure Portal, select **Virtual Machines**.

2.  Select **paw-1**, then select **Connect**.  In the dialog, select **Download RDP file Anyway**.  Open the downloaded RDP file and connect to the Virtual Machine.

    > **Note**: Default username is **wsadmin** with **p\@ssword1rocks** as password and you may need to request JIT Access if you have taken a break between exercises.

3.  In the **PAW-1** virtual machine, open **Windows PowerShell ISE** as **administrator**.

    -   Select the **Windows** icon

    -   Right-click **Windows PowerShell ISE**, choose **More** then select **Run as Administrator**

4.  Copy and run the following command:

```PowerShell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted
```

5.  In the dialog, select **Yes**

6.  Select **File-\>Open**, browse to the extracted GitHub directory and open the **\\Hands-on lab\\Scripts \\PortScanner.ps1**.

    > **Note**: You would have downloaded the [GitHub repo](https://github.com/Microsoft/MCW-Azure-Security-Privacy-and-Compliance) and extracted this in the setup steps.  If you did not perform those steps, perform them now. You can also choose to copy the file from your desktop to the VM.

7.  Review the script. Notice that it does the following:

    a.  Installs NotePad++

    b.  Adds hosts entries for DNS

    > **Note**: When using multiple virtual networks, you must setup a DNS server in the Azure tenant.

    c.  Executes port scans

8.  Press **F5** to run the script. You should see the following (the Azure ARM Template created a default rule to block all traffic):

    a.  Port scan for port 3389 (RDP) to **DB-1** and **WEB-1** is unsuccessful from the **PAW-1** machine.

    b.  The information above for port 3389 (RDP) is visible after running the script and pressing **F5**.
    
    ![The information above for port 3389 (RDP) is visible after running the script and pressing F5.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image55.png)

    c.  Port scan for port 1433 (SQL) to **DB-1** and **WEB-1** is unsuccessful from the **PAW-1** machine. **DB-1** is running SQL Server but traffic is blocked at NSG and via the Windows Firewall.

    ![The information above for port 1433 (SQL) is visible after running the script and pressing F5.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image56.png "View the information")

    d.  Port scan for port 80 (HTTP) to **DB-1** and **WEB-1** is unsuccessful from the **PAW-1** machine, if traffic was allowed, it would always fail to **DB-1** because it is not running IIS or any other web server.

    ![The information above for port 80 (HTTP) is visible after running the script and pressing F5.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image57.png)

> **Note**  The ARM template deploys a Deny All rule.  If you were to simply create a Network Security Group from the UI, you would not experience this behavior.

### Task 2: Configure network security groups

1.  Switch to the [Azure Portal](https://portal.azure.com).

2.  Configure the database server to only allow SQL Connections from the web server:

    a.  Select **Network Security Groups**.

    b.  Select **DbTrafficOnly**.

    c.  Select **Inbound Security Rules**.

    d.  Select **+Add**.

    e.  For the **Source**, select **IP Addresses**.

    f.  For the **Source IP address**, enter **10.2.0.4**.

    g.  For the **Destination port range**, enter **1433**.

    h.  For the **Priority**, enter **100**.

    i.  For the **Name**, enter **Port_1433**.

    g.  Select **Add**.

3.  Configure the web server to allow all HTTP and HTTPS connections:

    a.  Select **Network Security Groups**.

    b.  Select **WebTrafficOnly**.

    c.  Select **Inbound Security Rules**.

    d.  Select **+Add**.

    e.  For the **Destination port range**, enter **80,443**.

    f.  For the **Priority**, enter **100**.

    g.  Change the **Name** to **Port\_80\_443**.

    h.  Select **Add**.

    > **Note**: In some rare cases it may take up to 15 minutes for your Network Security Group to change its status from **Updating**.  You won't be able to add any other rules until it completes.

4.  Configure both the database and web server to only allow RDP connections from the PAW machine:

    a.  Select **Network Security Groups.** For both the **DbTrafficOnly** and **WebTrafficOnly**, do the following:
    
       -  Select **Inbound Security Rules**.

       -  Select **+Add**.

       -  For the **Source**, select **IP Addresses**.

       -  For the **Source IP address**, enter **10.0.0.4**.

       -  For the **Destination port range**, enter **3389**.

       -  For the **Priority**, enter **101**.

       -  For the **Name**, enter **Port_3389**.

       -  Select **Add**.

5.  Configure all NSGs to have Diagnostic logs enabled.

    a.  Select **Network security groups.** For each NSG (DBTrafficOnly and WebTrafficOnly), do the following:

       -  In the content menu, select **Diagnostic logs**, and then select **Add diagnostic setting**.

       ![Diagnostics logs is selected and highlighted under Monitoring on the left side, and Turn on diagnostics is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image58.png "Select Turn on diagnostics")

       -  For the name, enter the NSG name and then add **Logging** to the end.

       -  Check the **Send to Log Analytics** checkbox, in the **Log Analytics** box, select **Configure**.
       
       -  Select the **azseclog...** workspace.
       
       -  Select both LOG checkboxes.

       -  Select **Save**.

       ![Save is highlighted at the top, and two log items are selected and highlighted below.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image60.png "Save the logs")

       -  Repeat for all remaining Network Security Groups

### Task 3: Test network security group rules \#2

1.  Switch back to the **PAW-1** virtual machine.

2.  Press **F5** to run the **PortScan** script. You should see the following:

    a.  Port scan for port 3389 (RDP) to **DB-1** and **WEB-1** is successful from the **PAW-1** machine.

    ![The information above for port 3389 (RDP) is visible after running the script and pressing F5.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image61.png "View the information")

    b.  Port scan for port 1433 (SQL) to **DB-1** is successful, and **WEB-1** is unsuccessful from the **PAW-1** machine.

> **Note**: You may need to disable the windows firewall on the DB-1 server to achieve this result.

   ![The information above for port 1433 (SQL) is visible after running the script and pressing F5.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image62.png "View the information")

   c.  If IIS has been setup on WEB-1, the port scan for port 80 (HTTP) to **DB-1** is unsuccessful and **WEB-1** is successful from the **PAW-1** machine

   ![The information above for port 80 (HTTP) is visible after running the script and pressing F5.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image63.png "View the information")

### Task 4: Install network watcher VM extension

1.  Switch to the Azure Portal.

2.  Select **Virtual Machines**.

3.  Select **db-1**.

4.  In the blade menu, select **Extensions**, then select **+Add**.

    ![Extensions is selected and highlighted on the left under Settings, and + Add is highlighted at the top right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image64.png "Select + Add")

5.  Browse to the **Network Watcher Agent for Windows**, and select it.

6.  Select **Create**.

    ![Network Watcher Agent for Windows is highlighted on the left, and Create is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image65.png "Create a Network Watcher agent")

7.  In the next **Install extension** dialog window (note that it could be blank) select **OK.** You should see a toast notification about the script extension being installed into the Virtual Machine.

    ![The toast notification states: "Deployment in progress ... Deployment to resource group 'azure-securitytest1' is in progress."](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image66.png "Toast notification about the script installation")

### Task 5: Setup network packet capture

1.  In the main Azure Portal menu, select **Monitor**.

2.  In the context menu, under **Insights** select **Network**.

    ![Monitor is selected on the left side of the Azure portal, and Network watcher is highlighted on the right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image67.png "Select Network watcher")

3.  Select the **Overview** link.

4.  Expand the subscription item you are running your labs in.

5.  For the **East US** region (or whatever region you deployed your VMs too), select the ellipses, then select **Enable Network Watcher**.

    ![The East US row is highlighted under Region, and Enable network watcher is selected in the submenu.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image68.png "Enable Network Watcher")

6.  In the new context menu, select **Packet capture**.

7.  Select **+Add**.

    ![Packet capture is selected and highlighted on the left under Network Diagnostic Tools, and + Add is highlighted at the top right.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image69.png "Add a packet capture")

8.  For the target virtual machine, ensure that **db-1** is selected.

9.  For the capture name, enter **databasetraffic**.

10. Notice the ability to save the capture file to the local machine or an Azure storage account. Ensure that the resource group storage account is selected.

    ![In this screenshot, databasetraffic is entered in the Packet capture name box, and the Storage account check box is selected.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image70.png "Dialog box screenshot")

11. For the values, enter the following:

-   Maximum bytes per packet: 0
-   Maximum bytes per session: 1073741824
-   Time limit: 600

12. Select **OK**.

### Task 6: Execute a port scan

1.  Switch your Remote Desktop connection to the **PAW-1** virtual machine.

2.  Uncomment the last line of the script, and press **F5**.

> **Note**: You should see the basic ports scanned, and then a port scan from 80 to 443. This will generate many security center logs for the Network Security Group which will be used in the Custom Alert in the next exercise.

## Exercise 5: Azure Sentinel Logging and Reporting

Duration: 20 minutes

Synopsis: In this exercise, you will setup Azure Sentinel to point to a logging workspace and then create custom alerts that execute Azure Runbooks.

### Task 1: Create a dashboard

1.  Open the Azure Portal.

2.  Select **All services**, then type **Sentinel**, select **Azure Sentinel**

![In this screenshot, All Services is selected, and then a search for Sentinel is displayed.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image94.png "Searching for Sentinel")

3.  In the blade, select **+Add**, select the Log Analytics resource for your resource group, then choose **Add Azure Sentinel**

4.  In the blade, under **Threat Management**, select **Dashboards**

5.  In the list of dashboards, select **Azure AD Audit logs**, select **Install**

![In this screenshot, Dashboards has been selected and the Azure AD Audit Logs dashboard has also been selected.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image95.png "Adding a dashboard")

6.  In the list of dashboards, select **Azure Network Watcher**, select **Install**

7.  Select **View Dashboard**, take a moment to review your new dashboards

### Task 2: Create an Analytics alert

1.  Navigate back to the **Azure Sentinel** workspace, in the **Configuration** blade section, select **Analytics** then select **+Add**.

![In this screenshot, Analytics is highlighted and so is the Add button.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image96.png "Adding a analytics alert")

2.  For the name, enter **PortScans**.

3.  For the description, enter **A custom rule to detect port scans**.

4.  In the **Set alert query** text box, type:

```powershell
 AzureDiagnostics | where Type != 'AzureMetric' and OperationName == 'NetworkSecurityGroupCounters' and type_s == 'block' and direction_s == 'In' and Resource == 'WEBTRAFFICONLY' and TimeGenerated > ago(1h)
 ```

 ![In this screenshot, the alert simulation shows data after the query has been entered.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image97.png "Reviewing alert simulation data")

> **Note**: If you were quick going through the labs, then you may not have log data in the Log Analytics workspace just yet that corresponds to "AzureMetric". You may need to wait 15-30 minutes before a query will execute.

5.  For the operator value, enter **50**.

6.  For the frequency, select **30** and **Minutes**.

7.  For the period, select **30** and **Minutes**.

> **Note:** This is so that our lab will run quickly and may not be appropriate for real world.

8. For the suppress alerts for, enter **30** and **Minutes**.

    ![The above information is entered in the dialog box for the new custom analytics rule.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image98.png "Enter the information in the dialog box")

9. Select **Create**.

>   **Note**:  It may take a few minutes for the alert to fire.  You may need to run the PortScan script a few times from **paw-1**

### Task 3: Investigate a custom alert Case

1.  In the main menu, select **Azure Sentinel**.

2.  Select **Cases**.

3.  Select the new **PortScans** case.

    ![This is a screenshot of the new PortScans security alert, which has a Medium Severity of 1.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image75.png "View the new PortScans alert")

> **Note**: It may take 15-20 minutes for the alert to fire. You can continue to execute the port scan script to cause log events or you can lower the threshold for the custom alert.

4.  In the dialog, choose **Investigate**

    ![This is a screenshot of the case dialog, with the button Investigate highlighted.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image76.png "Investigate a case")

5.  In future versions, you will get to see insights about the alerts and the resources related to what caused it to fire:

    ![This is a screen shot of an alert instance.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image77.png)

### Task 4: Create and run a playbook

1.  In the **Azure Sentinel** blade, select **Playbooks**.

2.  In the new window, select **Add Playbook**.

    ![Screen shot of the playbooks blade with the Playbooks and Add Playbook links highlighted.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image79.png)

3.  The **Create logic app** blade will display:

    a.  For the name, enter **Email**.

    b.  Select your existing resource group.

    c.  Toggle the **Log Analytics** to **On** and then select your **azuresecurity** Log Analytics workspace.

   ![The information above is entered in the Create logic app dialog box.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image80.png "Enter Create logic app information")

4.  Select **Create**, after a few moments, the **Logic Apps Designer** will load.

5.  Select the **Send notification email** template.

    ![Send notification email is selected on the right side under Choose a template below to create your Logic App.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image81.png "Select the Send notification email template")

6.  Select **Use this template**.

    ![The Use this template button is selected under Send notification email with alert details from Azure Security Center.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image82.png "Select Use this template")

7. Select **Sign In**, and then type your Azure/O365 credentials.

    ![The Sign in button is highlighted next to Office 365 Outlook under This logic app will connect to.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image83.png "Sign in to Office 365 Outlook")

> **Note**: This would need to be a valid Office 365 account, if you do not have a valid Office 365 account, then utilize a basic email template for Outlook.com

8. Select **Continue**.

9. For the email address, enter your email.

10. Select **Save**. You now have an email alert action based on LogicApps for your custom security alert to use.

    ![Save is highlighted in Logic Apps Designer, and information about the custom security alert appears below.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image84.png "Save the email alert action")

11. Lastly, after you have created the new Playbook, ensure that the status is **Enabled**.  If not, then select **Enable** in the menu.

### Task 5: Execute Jupyter Notebooks

1.  In the **Azure Sentinel** blade, select **Notebooks**.

2.  Scroll to the bottom of the page and select **Clone Azure Sentinel Notebooks**.

![Notebooks and Clone Azure Sentinel Notebooks is highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image99.png "Close Azure Sentinel Notebooks")

3.  The Azure Notebooks page will open, if prompted, login, then select **Import**.  

![Import button is highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image100.png "Import Azure Sentinel notebooks")

4.  In your notebook, browse to **/Azure Sentinel/Notebooks**, then select **Get Started.ipynb**.

5.  On the page, select **Run on Free Azure**.

![The Run on Free Azure is button is highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image101.png "Set the run notebook host")

6.  In the menu, select **Kernel->Change kernel**, then select **Python 3.6**.

![The page menu is expanded to the Kernel menu item and the change kernel with Python 3.6 is selected](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image102.png "Set the Kernel to Python 3.6")

7.  Choose the **Run** button until you execute the entire Notebook, note that some steps will required your input.

>   **Note**: You can find the open source GitHub notebooks at [https://github.com/Azure/Azure-Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Notebooks).

### Task 6: Creating Reports with Power BI

1.  Navigate back to your **Azure Sentinel** browser window.  Select **Logs**.

2.  Expand the **LogManagement** node, notice the various options available.

3.  In the schema window, select **AzureDiagnostics**, then choose the **eye** icon.

4.  In the top right, select **Export**, then select the **Export to Power BI (M Query)** link.

    ![All the above button clicks are displayed with red highlighting.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image103.png "Export a Power BI report query")

5.  Select **Open**, a text document with the Power Query M Language will be displayed.

6.  Follow the instructions in the document to execute the query in Power BI.

    ![The instructions at the top of the PowerBIQuery.txt file are highlighted.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image88.png "Follow the query instructions")

6.  Close **Power BI**.

## Exercise 6: Using Compliance Tools (Azure Policy, Secure Score and Compliance Manager)

Duration: 15 minutes

Synopsis: In this exercise, attendees will learn to navigate the Azure Policy and Secure Score features of Azure.  You will also explore the Compliance Manager portal that will provide you helpful tasks that you should consider when attempting to achieve specific compliance policies.

### Task 1: Review a basic Azure Policy

1.  Open the [Azure Portal](https://portal.azure.com).  Select **All Services**, then type **Policy**.  Select **Policy** in the list of items.

    ![The links above are all highlighted to get to the Azure Policy blade.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image104.png "Open the Azure Policy blade")

2.  In the blade menu, select **Compliance**, review your **Overall resource compliance** percentage.

    ![The links above are all highlighted to get to the Azure Policy blade.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image105.png "Open the Azure Policy blade")

3.  For the scope, ensure the proper subscription is selected, then select **ASC Default (subscription...**.

4.  In the **Initiative compliance** blade, review your compliance metrics.

5.  Scroll to the results area and select the **Non-compliant resources** tab.

    ![The non-compliant resources tab is highlighted.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image106.png "Select the Non-Compliant resources tab")

6.  In the filter search box, type **PAW-1** and select it when displayed.

7.  With the **Policies** tab selected, review the policies that the resource is non-complying against.  

    ![The Resource compliance blade for PAW-1 is displayed with the non-compliant items highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image107.png "Review the non-compliant items")

8.  Choose one of the policies.  Review the Definition JSON of the policy definition, notice how it is based on ARM Template format and is looking for specific properties to be set of the non-compliant resources.

    ![The policy definition is displayed](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image108.png "Review the policy definition")

> **Note**: You can use these out of box templates to build your own policies and apply them as blueprints.

### Task 2: Review and create Azure Blueprints

1.  In the Policy blade, select **Definitions**.  These are a list of all defined policies which can be selected for assignment to your subscription resources.

    ![A listing of policy definitions on the Policy Blade Definitions](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image109.png "Review available policy definitions")

2.  In the Policy blade, select **Blueprints**.

3.  In the Blueprints blade, select **Blueprint definitions**.

4.  Select **+Create blueprint**.

    ![All the buttons above are highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image110.png "Create a new blueprint")

5.  Review some of the sample blueprints, then select **Start with blank blueprint**.

    ![Page displayed with the blueprint templates and the Start with a blank blueprint highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image111.png "Create a blank blueprint")

6.  For the name, type **gdprblueprint**.

7.  For the location, select the ellipses, then select your subscription in the drop down.

8.  Choose **Select**.

    ![New blue print dialog with name and location filled in](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image112.png "Set blueprint fields")

9.  Select **Next: Artifacts**.

10.  Select **+Add artifact**.

11. For the Artifact Type, select **Policy Assignment**, review all the policies available to you (at the time of this writing you would see 283 policies).

12. In the search box, type **unrestricted**, browse for the **Audit unrestricted network access to storage accounts**.

    ![Screen shot showing steps 9-12 highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image113.png "Add an artifact")

13. Select **Add**.

14. Select **Save Draft**.

15. For the new blueprint, click the ellipses, then select **Publish Blueprint**

    ![Screen shot showing the Publish blueprint dialog](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image114.png "Publish blueprint dialog")

16. For the version type **1.0.0**.

17. Select **Publish**.

18. For the new blueprint, click the ellipses, then select **Assign Blueprint**.

    ![Screen shot showing the Assign blueprint dialog](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image114.png "Assign blueprint dialog")

19. Review the page, then choose **Assign**.  This policy will now be audited across all your storage accounts in the specific subscription.

### Task 3: Secure Score

1.  In the Azure Portal, select **All Services**, then type **Security**, select **Security Center**.

2.  In the Security Center blade, select **Secure score**.

3.  Review your overall secure score values and then notice the category values.

    ![Screen shot showing Secure score blade and the score and categories highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image115.png "Review Secure Score score and categories")

4.  Select your subscription, you will be presented with the items that have failed resource validation sorted by the score value that is assigned to that particular recommendation item.

5.  Select the **An Azure Active Directory administrator should be provisioned for SQL Servers**, on the recommendation blade, you will be presented with information about how to remediate the recommendation to gain the impact value to your score.

    ![Screen shot with the Probision an Azure AD Administrator for SQL Server highlighted](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image116.png "Review a security recommendation")

### Task 4: Use Compliance Manager for Azure

1.  In a browser, go to the Service Trust/Compliance Manager portal (<https://servicetrust.microsoft.com>).

2.  In the top corner, select **Sign in**, you will be redirected to the Azure AD login page.

    ![Sign in is highlighted at the top of the Service Trust/Compliance Manager portal.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image89.png "Sign in to Compliance Manager")

3.  If prompted, select or sign in with your Azure AD\\Office 365 credentials.

4.  In the menu, select **Compliance Manager->Compliance Manager Classic**.

    ![Compliance Manager Classic is highlight in the menu navigation.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image89.1.png "Open Compliance Manager Classic")

5.  Select on the **+Add Assessment** link.

6.  Select **Create a new Group**, for the name type **AzureSecurity**, select **Next**, set the **Would you like to copy the data from an existing group** toggle to **No**, select **Next**.

7.  For the product dropdown, select **Azure**.

8.  For the certification dropdown, select **GDPR**.

    ![Add a Standard Assessment dialog with Azure and GDPR selected.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image90.png)

9.  Select **Add to Dashboard.** You will now see a new assessment for Azure and GDPR in progress:

    ![Azure GDPR assessment status that shows in progress](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image91.png)

10.  Select **Azure GDPR**.

11. Review the various controls that you can implement:

![Several categories of controls are listed on the page.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image91.1.png)

12. On the top menu, choose **Trust Documents**, then select **Audit Reports**

13. Notice the various tabs that you can select from, select **FedRAMP Reports**.

14. These are all the FedRAMP reports sorted by date that have been performed and publicly posted for Azure customer review. Select the item displayed and briefly review the document.

    ![The FedRAMP Reports report type is highlighted on the Data Protection Standards and Regulatory Compliance Reports page, and Azure - FedRAMP Moderate System Security Plan v3.02 is highlighted at the bottom.](images/Hands-onlabstep-bystep-Azuresecurityprivacyandcomplianceimages/media/image93.png "Select the displayed FedRAMP report")


## After the hands-on lab 

Duration: 10 minutes

In this exercise, attendees will deprovision any Azure resources that were created in support of the lab.

### Task 1: Delete resource group

1.  Using the Azure portal, navigate to the Resource group you used throughout this hands-on lab by selecting **Resource groups** in the menu.

2.  Search for the name of your research group, and select it from the list.

3.  Select **Delete** in the command bar, and confirm the deletion by re-typing the Resource group name and selecting **Delete**.

4.  Don't forget to delete the Azure Key Vault application you created in Exercise 3, Task 3.

### Task 2: Remove Standard Tier Pricing

1.  Be sure to set your Azure Security pricing back to **Free**.

### Task 3: Delete lab environment (optional)

1.  If you are using a hosted platform, make sure you shut it down or delete it.

You should follow all steps provided *after* attending the Hands-on lab.
