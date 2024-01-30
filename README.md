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
9. [Closing Thoughts](#conclusion)

### Introduction <a name="introduction"></a>
The purpose of the Azure Database Migration Project is to gain experience with designing and implementing a cloud-based database system using Microsoft Azure. This will involve the initial set up of the "Adventure Works" database in a provisioned Azure Virtual Machine, before migrating it into Azure's cloud system. Following this, we will ensure the security of the uploaded database through data backups/restores, and create a development environment where we can test and experiment. The resilience of the environment would then be tested by simulating a disaster, and attempting to recover the "lost" data. We then established a backup copy of the database in a secondary region, adding an extra layer of data protection and availability. Finally, we managed user access by integrating Microsft Entra Directory to the SQL Database setup, creating a user, assigning it the DB Reader role, and verifying that it had the expected permissions. By doing all of this, we hope to have created a 

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

In this milestone, we aim to transition the database onto Azure's cloud ecosystem through a data migration process.

#### Preparation for Migration (Tasks 1-3)

In order to migrate the local database onto the Azure ecosystem, we first need to create a target SQL Database. This can be easily done in the Azure portal. We named the database "migration-project-database", and assigned it to our "Azure_Database_Migration_Project" resource group, before selecting the Basic Compute storage tier, as the the extent of our usage for this database will not be particularly high. The rest of the settings were left to default. We also needed to create a SQL database server - we dubbed this "migration-database-server", and chose the closest geographic location as the region and selected SQL authentication as the authentication method. After the server was created, the database was created following a final review. Additionally, we changed the firewall settings of the database to allow connections from the VM's IP address so that we can connect and make changes to the database later. We then connected to the VM and downloaded Microsoft Azure Data Studio from this [link](https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio) and ran the software, establishing a connection to the newly created Azure SQL Database. We also established another connection to the locally stored database, on the localhost server.

#### Carrying out the Migration (Tasks 4-6)

In order to carry out the migration, we needed two Azure Data Studio extensions: SQL Server Scheme Compare, and Azure SQL Migration. We first used the SQL Server Scheme Compare extension, setting the source to the local SQL database, and the target to the Azure SQL database. We then "compare" the two, and find all of the discrepancies between the two databases. We then applied these changes, copying the struture of the local database over to its Azure counterpart - this includes the tables, views, procedures and other objects. This will form the foundation of the final migration. This is done using the latter extension, and can mostly be done by simply following the on-screen instructions. However, it should be noted that there was a point where "Integration Runtime" needed to be installed and set up using the provided authentication keys in order to allow for data integration capabilities over different network environments. Then we can select the source and target databases, and begin the migration. Once it has completed, the validity of the data transfer was verified by examining the table names and comparing a random selection of data from various tables.


### Milestone 4: Data Backup and Restoration

The purpose of this milestone is to ensure that the database and any of its backups are securely stored on Azure, as we will later perform some operations that will compromise the integrity of the data. We will also provision another VM for the purpose of development; it is good practice for companies to have two copies of databases, one for tracking real customer data (production), and another for experimentation and innovation (development).

#### Initial Backup and Restoration (Tasks 1-3)

The first step here was to create a backup file (.bak) of the desired database using SSMS. This is again fairly straightforward - we connect to the SQL Server hosting our on-premise database, right-click on the database, select **Tasks > Back Up**, and select the default location for the backup file to be stored. Here, we opted for a full backup, as we wanted to create an exact duplicate of the database. Next, we accessed the Azure portal in the VM using Microsoft Edge, and in a similar fashion to before, created a storage account - "migrationprojectstorage", and a container - "data-migration-backup". We chose this due to Azure Storage's seamless integration with other Azure services, as well as its high level of security and encryption in order to keep our objects safe. Then, we uploaded the on-premise backup file from the local environment into the storage account and container, so that it is ready for backup. Another Windows VM was provisioned - "azure-vm-development" to provide us with a separate environment where we can make changes to the database without worrying about compromising the production database. Within this VM, SQL Server, SSMS, and Azure Data Studio were all downloaded, and the backup was restored in a similar fashion to Milestone 2 Task 4.

#### Automating Regular Backups (Task 4)

In SSMS, we can ensure that there is always a backup available for any database by scheduling regular database backups using the SQL Server Agent in SSMS. Aditionally, we needed to create a SQL Server Credential, which is a security object allowing SQL Server to access external resources (in this case Azure Blob Storage) securely. We created this by connecting to the development server on SSMS, and starting a new query. Here, we executed the following:

```
CREATE CREDENTIAL datamigrationservercredential
WITH IDENTITY = 'migrationprojectstorage'
SECRET = '[my_access_key_here]'
```
where [my_access_key_here] was replaced with the secret access key provided in the storage account. After running this, we can select **Management > Maintenance Plans**, right-click Maintenance Plans and select Maintenance Plan Wizard. Here, we can schedule the frequency, timing and type of backup that we want to occur - in this case, we chose a full backup to occur every Sunday at 12:00:00am. Then, we specified that it was the (restored) AdventureWorks2022 database that we wanted to back up, and selected the "migrationprojectstorage" storage account, and the "data-migration-backup" container. The rest of the settings were left as default, and the maintenance plan was put into place. We tested the maintenance plan by manually executing it, and we can see in the Azure portal that a backup has been saved into the correct container.

### Milestone 5: Disaster Recovery Simulation:

The purpose of this milestone is to simulate a disaster recovery situation, in order to prove that the infrastructure put into place is sufficient to recover any changes or data loss made to the database.

#### Mimicking Data Loss in the Production Environment (Task 1)

Three changes were selected in order to mimic a disaster scenario where the integrity of the data has been compromised. We put these changes into place by starting a new query on the production server, and executing the following:

1. Change top 100 SpatialLocations to NULL from Person.Address.
   ```
   
   ```
2. Change every product's colour with an even ProductID to NULL.
   ```

   ```
3. Delete LineTotal from Sales.SalesOrderDetail.
   ```

   ```

We verified that the above changes had indeed been implementing by selecting the top 1000 rows of the affected tables and observing the areas of interest.

#### Restoring the Database from Azure SQL Database Backup (Task 2)

We can restore the database from a historical version of itself using the Azure portal. We select the "migration-project-database" database, and then the **Restore** option, opening a Restore window. Here, we can select a restore point - we chose an hour before the corrupting changes had been made, and a name for the newly restored database - we chose "migration-project-database-restored". The rest of the parameters were left as default, and after a review, we created the backup database. We verified that this was successful by returning to Azure Data Studio in our VM, connecting to the newly created "migration-project-database-restored" database and comparing the data in the critically changed areas. As expected, the data did not contain the corrupting changes, and we had successfully restored the database to a point before the disaster. It is important to note that, since the first production database had been corrupted, that we would be using the "migration-project-database-restored" database as the production database from this point onwards.

### Milestone 6: Geo Replication and Failover

By configuring a geo replica of our production database, we improve the data protection and availability of our database by adding an extra layer of redundancy in another geographic location. We can configure georeplication for the production database by going to the Azure portal, selecting the "migration-project-database-restored" database, and selecting the **Replicas** tab under Data Management. Then we select **Create replica** to open a Geo Replica menu. We need to createa  new SQL Server, as the replica database will be hosted in another geographic location, so we name it "migration-replication-server", and set it to be located in the East US. After this, we could finalise the creation of the geo replica. Once it was successfully created, we could test the functionality of the replica by performing a **failover**, where we seamlessly switch from the primary server in the UK to the backup in the US. In order to do this, we first needed to create a failover group, which can be done after selecting our "migration-database-server" SQL server, and clicking **Failover groups > Add groups**. We named this "migrationfailovergroup", and assigned its secondary server as the "migration-replication-server". The rest of the settings were left as default, and the failover group finalised. Accessing "migrationfailovergroup", we can finally perform a failover in order to verify the functionality of the replica. We executed a failover, and accept that the secondary database will be switched to a primary role. We then verified in the VM that we can connect to the primary database using the connection details of the secondary server, and ran basic queries to check that the functionality was unchanged. Finishing these checks, we then performed a second failover to revert the servers to their initial roles.

### Milestone 7: Microsoft Entra Directory Integration

In this milestone, we aimed to integrate Microsoft Entra Directory into our Azure SQL Database architecture. This gives us a more streamlined way to manage who has permissions to perform certain operations on the data, utilising core security concepts such as Role Based Access Control (RBAC) and the Principle of Least Privilege.

#### Integrating Microsoft Entra ID with Azure SQL Database

Before assigning any roles, we first needed to enable Microsoft Entra, and thus assign a Microsoft Entra Admin for our "migration-database-server". We did this by selecting **Security > Microsoft Entra > Set admin**, finding my account, and assigning it as the admin for this server. Returning to the VM, we would then want to reconnect to the production database as the server admin. We do this by disconnecting from the database, and attempting to reconnect. However, instead of using SQL Login as the authetnication method, we used Azure Active Directory instead. This returned a drop down menu, where we were able to add our admin account. The rest of the details were kept the same, and we were able to successfully reconnect to the production database as an admin.

#### Creating a DB Reader User

A DB Reader user is a default role for Microsoft Entra users, granting them read-only access to the database, meaning they can view data, but not modify it. There are other default roles too, like DB Writer, who can rewrite and modify the data, and the DB Owner, who can modify schema and grant permissions to other users, but for the purpose of testing within this project, we created a DB Reader user. In order to do this, we would create a new user, and assign it the DB Reader role in a query:

```
CREATE USER [DB_Reader_DChen@aicoreusers.onmicrosoft.com] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [DB_Reader_DChen@aicoreusers.onmicrosoft.com];
```

In order to ensure that this newly created user has the correct permissions, we can again disconnect from the production database, and reconnect using the DB Reader's details. First, we checked that the user has the ability to view the data contained within the database. This was quickly verified by choosing any table in the database, and selecting the top 1000 entries, finding that the table appeared as expected. However, if we tried to modify or delete anything from any of the tables, we find that the following error was returned:

```
The DELETE permission was denied on the object 'Person.Address', database 'migration-project-database-restored', schema '???'
```

This confirms that the user does not have the permission to edit or delete any of the data, verifying that the newly created user has all of the expected permissions of the DB Reader role.

