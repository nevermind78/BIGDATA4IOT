---
tags: IntroToBigData
title: Lab 1 - Installing HDP Sandbox
---

# Lab 1 - Installing HDP Sandbox

**Course:** Intro to Big Data - IU S23  
**Author:** Firas Jolha

## Agenda
[TOC]

## Prerequisites
- Familiarity with terminals, shell commands, and fundamental knowledge in operating systems (Linux)
- Basic knowledge in virtualization and managing virtual machines
- Basic knowledge in containerization and dealing with Docker images and containers  
<!-- - Fundamental knowledge in Relational databases and DBMS  -->
<!-- - Familiarity with PostgreSQL server -->

## Objectives
<!-- <div style="text-align: justify;"> -->
- Install a hypervisor or VM monitor such as `VirtualBox`
- Install `Hortonworks Data Platform`
- Access `Hortonworks Data Platform` via `ssh`
- Access `HDFS` and learn how to transfer files  
<!-- - Install and access `PostgreSQL` database server -->
<!-- - Learn how to manage the cluster -->

## Introduction
Every business is now a data business. Data is the organization's future and most valuable asset. The `Hortonworks Data Platform (HDP)` is a security-rich, enterprise-ready open-source Hadoop distribution based on a centralized architecture (YARN). 

`Hortonworks Sandbox` is a single-node cluster that can be run as a Docker container installed on a virtual machine. [HDP](https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.5.0/index.html) handles big data processing and storage. It comprises multiple Apache Software Foundation (ASF) projects like Apache Hadoop, designed to meet enterprise demands. Hortonworks merged with Cloudera in 2019, becoming a subsidiary of Cloudera, Inc.

<div style="text-align: center;">
  <img src="https://i.imgur.com/Nel8wxT.png" width="300" />
  <img src="https://i.imgur.com/d8DlPRE.png" width="300" />
  <p>Hortonworks merged with Cloudera in 2019</p>
</div>

At the beginning of this course, we aim to refresh your knowledge of relational databases before diving into big data. We will use the PostgreSQL relational database management system (RDBMS). Fortunately, *HDP* comes with a pre-installed PostgreSQL server.

## Hardware requirements
- **Memory** dedicated to the cluster:  
  - Minimum: 4 GiB  
  - **Recommended:** 8+ GiB (More is better)
- **CPU**:  
  - Minimum: 4 Cores  
  - **Recommended:** 6+ Cores  
  - Virtualization should be enabled ([Check Virtualization on Windows](https://www.thewindowsclub.com/check-intel-amd-processor-supports-hyper-v), on Linux: `lscpu`). Virtualization might need enabling in BIOS.
- **Storage**:
  - 25-35 GiB for HDP 2.5.0
  - 65-75 GiB for HDP 2.6.5
  - 80-100 GiB for HDP 3.0.1  

:::info  
HDP 2.5.0 is based on CentOS 6.8, which reached EOL in 2021. Package updates via `yum` are no longer possible. We recommend installing HDP 2.6.5 unless system resources are limited.  
:::

## Exercises
:::success  
### **Instructions**  
**Note:** Take only full-screen and high-quality screenshots.  

1. Prepare a report `week1` with the lab results. Create a new Google document with the [following format](https://docs.google.com/document/d/1PkaJQT9n7S9NV2WufyIrtDSkGkJxur6sBdnyg07VDxw/edit?usp=sharing).
2. **[50 points]** Install HDP on your machine. Take a full screenshot of the Ambari dashboard and insert it into the report.  
3. **[5 points]** Access HDFS. Screenshot the content of the root folder and add it to the report.  
4. **[5 points]** Download the [data file](https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch06/populationbycountry19802010millions.csv). Upload it to the HDFS root folder via Ambari Files View, screenshot the folder content, and add it to the report. Then, delete the file from HDFS.
5. **[15 points]** Transfer the same file from the local machine to HDFS root via `scp` or `docker cp`. Screenshot the command and output, and add them to the report.
6. **[15 points]** Access the cluster node's shell via port `2222` (terminal) or `4200` (browser). Create a `data` folder in the HDFS root. Move the file into the `data` folder and copy the folder to the local machine. Add screenshots of the commands and outputs to the report.
7. **[5 points]** Reset the Ambari-Admin password and access Ambari as admin. Display the user roles in the cluster and screenshot them for the report.
8. **[5 points]** Create a new user with your name and assign the cluster manager (admin) role. Access it and screenshot the session. Check the HDP roles [here](https://docs.cloudera.com/HDPDocuments/Ambari-2.5.1.0/bk_ambari-administration/content/cluster_roles.html).
9. Submit the `week1` report to Moodle before the deadline (check Moodle for deadlines).  
:::
