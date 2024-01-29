# An Overview of the Azure Database Migration Project

## Table of Contents
1. [Introduction](#introduction)
2. [Milestone 1: Set up the Environment](#milestone1)
3. [Milestone 2: Set up the Production Environment](#milestone2)
    1. [Creating and Provisioning the Windows Virtual Machine (Tasks 1-2)](#M2T1-2)
    2. [SQL Server and SSMS Installation (Task 3)](#M2T3)
    3. [Restoring the Production Database (Task 4)](#M2T4)
4. [Milestone 3](#milestone3)
5. [Milestone 4](#milestone4) 
6. [Milestone 5](#milestone5)
7. [Milestone 6](#milestone6)
8. [Milestone 7](#milestone7)

### Introduction <a name="introduction"></a>
The purpose of the Azure Database Migration Project is to gain experience with designing and implementing a cloud-based database system using Microsoft Azure. This will involve the initial set up of the "Adventure Works" database in a provisioned Azure Virtual Machine, before migrating it into Azure's cloud system. Following this, we will ensure the security of the uploaded database through data backups/restores, and create a development environment where we can test and experiment. The resilience of the environment would then be tested by simulating a disaster, and attempting to recover the "lost" data. We then established a backup copy of the database in a secondary region, adding an extra layer of data protection and availability. Finally, we managed user access by integrating Microsft Entra Directory to the SQL Database setup, creating a user, assigning it the DB Reader role, and verifying that it had the expected permissions.

### Milestone 1: Set up the Environment <a name="milestone1"></a>

This milestone was a relatively trivial one. In this milestone, a GitHub repository was created to track the progress of the project for marking later. Additionally, the login details required to access the Microsoft Azure account were provided by a member of the supporting staff. The same Microsoft Azure account would be used throughout the rest of the project, with a sufficient level of access required to fully migrate a database from a local machine to an Azure database available on the cloud.

### Milestone 2: Set up the Production Environment <a name="milestone2"></a>

In this milestone, we laid the foundation of the tools we would need in order to simulate a real data migration project, as well as the cloud-based database system on Azure.

#### Creating and Provisioning the Windows Virtual Machine (Tasks 1-2)

An Azure virtual machine (VM) is a software emulation of a computer system, which borrows its processing power from the cloud. In this project, we used VMs in order to imitate the operations of an on-premise site within a company, from which we wish to upload data to a centralised cloud location. We first created a resource group: "Azure_Database_Migration_Project" to store all of the related resources, in an effort to keep our project well organised. Next, we initiated the process of creating a VM, filling out the details that would define its properties. We named it "migration-vm", chose the closest geographical region to store our VM (UK South), and the image to be Windows 10 Pro, as this was the assumed OS used by the company. Finally, created an admin account with an accompanying password in order to safely access the VM. The rest of the options were kept as default, and after confirming that I had an eligible Windows 10/11 license, the VM was deployed and created.

#### SQL Server and SSMS Installation (Task 3)

The next task was to download SQL Server and SQL Server Management Studio (SSMS) in the Windows VM. This is a relational database management system developed by Microsoft, and would allow us to later restore the database that we would be working with for the remainder of the project. In order to do this, we would first have to connect to it, which was done by downloading the Remote Desktop Connection (RDP) file, running it, and logging in using the admin details we specified before. Then, using the Microsoft Edge browser installed in the VM, we went to the [Microsoft Download Centre](https://www.microsoft.com/en-gb/sql-server/sql-server-downloads) and downloaded the installer. After running the installer, we followed the on-screen instructions, making sure to select the basic installation option, and the software was installed. We then downloaded SSMS in a similar fashion, and we confirmed the successful installation of both SQL Server and SSMS when they appeared in the Start menu.

#### Restoring the Production Database (Task 4)

Finally, we created the production database by restoring it from a provided backup file: [AdventureWorks2022.bak](https://aicore-portal-public-prod-307050600709.s3.eu-west-1.amazonaws.com/project-files/93dd5a0c-212d-48eb-ad51-df521a9b4e9c/AdventureWorks2022.bak). This is an extensive sample database that has enough complexity and data to accurately simulate the workings of a real manufacturing company. This backup file was downloaded onto the VM. We then copied it to `C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Backup\AdventureWorks2022.bak`. Then, we opened SSMS and connected to the localhost server, before restoring the database so that it was ready for querying and transforming.

### Milestone 3: Migrate to Azure SQL Database

In this milestone, we aimed to transition the database onto Azure's cloud ecosystem through a data migration process.

#### Preparation for Migration (Tasks 1-3)

In order to migrate the local database onto the Azure ecosystem, we first need to create a target SQL Database. This can be easily done in the Azure portal. We named the database "migration-project-database", and assigned it to our "Azure_Database_Migration_Project" resource group, before selecting the Basic Compute storage tier, as the the extent of our usage for this database will not be particularly high. The rest of the settings were left to default. We also needed to create a SQL database server - we dubbed this "migration-database-server", and chose the closest geographic location as the region and selected SQL authentication as the authentication method. After the server was created, the database was created following a final review. Additionally, we changed the firewall settings of the database to allow connections from the VM's IP address so that we can connect and make changes to the database later. We then connected to the VM and downloaded Microsoft Azure Data Studio from this [link](https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio) and ran the software, establishing a connection to the newly created Azure SQL Database. We also established another connection to the locally stored database, on the localhost server.

#### Carrying out the Migration (Tasks 4-6)

In order to carry out the migration, we needed two Azure Data Studio extensions: SQL Server Scheme Compare, and Azure SQL Migration. We first used the SQL Server Scheme Compare extension, setting the source to the local SQL database, and the target to the Azure SQL database. We then "compare" the two, and find all of the discrepancies between the two databases. We then applied these changes, copying the struture of the local database over to its Azure counterpart - this includes the tables, views, procedures and other objects. This will form the foundation of the final migration. This is done using the latter extension, and can mostly be done by simply following the on-screen instructions. 
