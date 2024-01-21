# An Overview of the Azure Database Migration Project

## Table of Contents
1. [Introduction](#introduction)
2. [Milestone 1: Set up the Environment](#milestone1)
3. [Milestone 2: Set up the Production Environment](#milestone2)
    1. [Task 1](#M2T1)
    2. [Task 2](#M2T2)
    3. [Task 3](#M2T3)
    4. [Task 4](#M2T4)
4. [Milestone 3](#milestone3)
5. [Milestone 4](#milestone4) 
6. [Milestone 5](#milestone5)
7. [Milestone 6](#milestone6)
8. [Milestone 7](#milestone7)

## Introduction <a name="introduction"></a>
The purpose of the Azure Database Migration Project is to gain experience with designing and implementing a cloud-based database system using Microsoft Azure. This will involve the initial set up of the "Adventure Works" database in a provisioned Azure Virtual Machine, before migrating it into Azure's cloud system. Following this, we will ensure the security of the uploaded database through data backups/restores, and create a development environment where we can test and experiment. The resilience of the environment will then be tested by simulating a disaster, and attempting to recover the "lost" data. We shall then establish a backup copy of the database in a secondary region, adding an extra layer of data protection and availability. Finally, we will manage user access by integrating Microsft Entra Directory to the SQL Database setup, using concepts such as Role-Based Access Control (RBAC) and principal of least privilege.

## Milestone 1: Set up the Environment <name="milestone1"></a>

This milestone was a relatively trivial one. In this milestone, a GitHub repository was created to track the progress of the project for marking later. Additionally, the login details required to access the Microsoft Azure account were provided by a member of the supporting staff. The same Microsoft Azure account would be used throughout the rest of the project, with a sufficient level of access required to fully migrate a database from a local machine to an Azure database available on the cloud.

## Milestone 2: Set up the Production Environment <name="milestone2"></a>

In this milestone, we laid the foundation of the tools we would need in order to simulate a real data migration project, as well as the cloud-based database system on Azure.
