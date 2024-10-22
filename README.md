---
tags: IntroToBigData
title: Lab 1 - Installing HDP Sandbox
---

# Lab 1 - Installing HDP Sandbox

**Course:** Big Data  For IOT

**Author:** Abdallah Khemais
## Agenda

* [Lab 1 - Installing HDP Sandbox](#lab-1---installing-hdp-sandbox)
* [Agenda](#agenda)
* [Prerequisites](#prerequisites)
* [Objectives](#objectives)
* [Introduction](#introduction)
* [Hardware requirements](#hardware-requirements)
* [Installing Hortonworks Sandbox](#installing-hortonworks-sandbox)
    * [A. Using a Hypervisor](#a-using-a-hypervisor)
        * [1. Installing a Hypervisor](#1-installing-a-hypervisor)
        * [2. Installing HDP Sandbox](#2-installing-hdp-sandbox)
            * [2.1. Downloading the Sandbox](#21-downloading-the-sandbox)
            * [2.2. Installing the Sandbox](#22-installing-the-sandbox)
            * [2.3. Running the VM](#23-running-the-vm)
    * [B. Using Docker](#b-using-docker)
        * [1. Installing Docker Desktop on Windows](#1-installing-docker-desktop-on-windows)
        * [2. Installing HDP Sandbox](#2-installing-hdp-sandbox-1)
* [Configuring the resources for Docker](#configuring-the-resources-for-docker)
* [Stopping/Restarting the cluster](#stoppingrestarting-the-cluster)
* [Access HDP Sandbox cluster](#access-hdp-sandbox-cluster)
* [Access via SSH](#access-via-ssh)
* [Access HDFS](#access-hdfs)
* [Transfer data between HDFS and local file systems](#transfer-data-between-hdfs-and-local-file-systems)
* [Transfer data into and out of the cluster node](#transfer-data-into-and-out-of-the-cluster-node)
* [Transfer data into and out of HDFS](#transfer-data-into-and-out-of-hdfs)
* [Common issues](#common-issues)
* [Self-check questions](#self-check-questions)
* [Appendix](#appendix)
* [Sandbox Version](#sandbox-version)
* [Ambari Roles](#ambari-roles)
* [References](#references)


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


HDP 2.5.0 is based on CentOS 6.8, which reached EOL in 2021. Package updates via `yum` are no longer possible. We recommend installing HDP 2.6.5 unless system resources are limited.  


## Exercises

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
9. Submit the `week1` report .

# Installing Hortonworks Sandbox
There are two common ways to install HDP Sandbox on your PC, either by using a hypervisor such as *VirtualBox* which will pull the Docker image and run a container for your cluster or by directly using Docker where you need to manage your resources via `docker` command line [options](https://docs.docker.com/config/containers/resource_constraints/) in Linux or you can use WSL backend in Windows where you can configure resources via `.wslconfig` file.


**Note:** For M1- and M2-based MacOS users, please install CentOS 7.5 or Ubuntu 18.04. Then follow the approach **B. using Docker** where you need to install Docker on the virtual machine and run cluster containers.

![](https://i.imgur.com/DuC1BqZ.png)
 


## A. Using a Hypervisor
If you are not familiar with Docker, you can follow this approach where configuring the cluster resources can be done via the hypervisor GUI. If you have less resources, then we recommend using Docker, so you do not need to consume resources for running the guest virtual machine.

> In this approach, you will run a virtual machine which in turn will run your cluster container, so the operating system of the virtual machine will be different from the operating system of the container (HDP Sandbox cluster). You can notice that by checking the content of `/home` directory or the version of the operating system `cat /etc/redhat-release`.


### 1. Installing a Hypervisor
We recommend *VirtualBox* as a hypervisor since it is supported for most common operating systems (Linux, Windows, and macOS). Please, follow the attached link in the following list to download your preferred hypervisor. 
- [Oracle VM VirtualBox](https://www.virtualbox.org) **(Recommended)**
- [VMware Workstation Player](https://www.vmware.com/products/workstation-player.html) (Only for Linux and Windows)
- [VMware Fusion for mac](https://www.vmware.com/products/fusion.html) (Only for macOS)
[KVM](https://www.linux-kvm.org/page/Main_Page) (Only for Linux)
- [UTM](https://mac.getutm.app) (Only for M1/M2 macOS) 


For installation instructions, you can use Google but I share here a [tutorial](https://nerdschalk.com/how-to-install-and-use-virtualbox-on-windows-11/) to install *VirtualBox* on *Windows 11*. If you have an old version of the software, we recommend to update it in order to avoid any issues in installing the virtual machines. In my PC, I installed VirtualBox 7.0.4 in January 2023.

### 2. Installing HDP Sandbox

Hortonworks Data Platform (HDP) is a straightforward, pre-configured, learning environment that contains the latest developments from Apache Hadoop. The Sandbox comes packaged in a virtual environment that can run in the cloud or on your personal machine. The Sandbox allows you to learn and explore HDP on your own.



[**Installation**](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/2.html)



### 2.1. Downloading the Sandbox
You can find the download links of the Sandbox in *.ova* format with respect to the chosen hypervisor. If you are using **VirtualBox**, then download from [here](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-2.6.5/HDP_2.6.5_virtualbox_180626.ova). For **VMware** users, the download link is [here](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-2.6.5/HDP_2.6.5_vmware_180622.ov). You can also download them from [the official website](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html) but it needs an account on Cloudera website. I share below the download links for all available versions of HDP Sandbox.
> The download links of HDP Sandbox on VirtualBox:
>- [**HDP 2.5.0**](https://archive.cloudera.com/hwx-sandbox/hdp/dp-2.5.0/HDP_2.5_virtualbox.ova)
>- [**HDP 2.6.5**](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-2.6.5/HDP_2.6.5_virtualbox_180626.ova)
>- [**HDP 3.0.1**](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-3.0.1/HDP_3.0.1_virtualbox_181205.ova) 

You can find here the installation instructions tutorial:
[here](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/1.html)


> The download links of HDP Sandbox on VMware:
>- [**HDP 2.5.0**](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-2.5.0/HDP_2.5_vmware.ova)
>- [**HDP 2.6.5**](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-2.6.5/HDP_2.6.5_vmware_180622.ova)
>- [**HDP 3.0.1**](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-3.0.1/HDP_3.0.1_vmware_181205.ova) 



### 2.2. Installing the Sandbox
I will show here the steps to install the HDP Sandbox on VirtualBox. First of all, you need to be sure that you have installed VirtualBox and it is ready to create VMs.

<p align="center">
<img src="https://i.imgur.com/wGAiLxE.png" width="600"/>
<br>Oracle VM VirtualBox
</center>



Select `File` from the top toolbar then choose `Import Appliance...` from the drop-down list or press `Ctrl+I`. The following window appears which allows you to specify the file where to import the virtual appliance from. Here you need to select the path of the virtual appliance. The virtual appliance has the extension `.ova`.

<p align="center">
  <img src="https://i.imgur.com/ITHlQRW.png" width="600"/>
  <br>Import Appliance window
</p>



As shown in the figure below, select the path of the file `.ovf` then press Next.

<p align="center">
  <img src="https://i.imgur.com/Khd6R0S.png" width="600"/>
  <br>Import Appliance window
</p>


In the next window, you may need to change some settings. Make sure that you set the CPU cores to 4 and the RAM size to 8192 MB.
 (or 4096 MB if you have less resources). 

<p align="center">
  <img src="https://i.imgur.com/gdD7Xlg.png" width="600"/>
  <br>Appliance settings window
</p>



And wait for the appliance to be imported as shown in the figure below.

<p align="center">
  <img src="https://i.imgur.com/IhsRhH4.png" width="600"/>
  <br>Progress of importing the virtual appliance
</p>


If you got a value 0 for the `Base Memory` after importing the appliance (a bug in VirtualBox), please update the value as explained above and start the virtual machine.
<!--
<p align="center">
  <img src="https://i.imgur.com/nAarGTv.png" width="600"/>
  <br>Fixing the RAM size
</p>
-->


### 2.3. Running the VM
    
The first boot of HDP Sandbox takes a lot of time, please take a break and wait till it finishes. Actually, during this time, the virtual machine is building the Docker image and then it starts to run a container for your cluster where you can access it from the host machine.

<p align="center">
  <img src="https://i.imgur.com/kWEru1e.png" width="600"/>
  <br>Booting HDP Sandbox
</p>

<!-- 
![](https://i.imgur.com/Y5A6L77.png)
![](https://i.imgur.com/ty2pJuC.png) 
-->


After finishing the extraction process, the system will run as shown below.
<p align="center">
  <img src="https://i.imgur.com/xfD2P9V.png" width="600"/>
  <br>Running HDP Sandbox
</p>

<!-- ![](https://i.imgur.com/HwKBuzr.png) -->
<!-- ![](https://i.imgur.com/xfD2P9V.png) -->
<!-- ![](https://i.imgur.com/gpslFzA.png) -->


> CentOS 7 is the operating system of the installed virtual machine which will run your sandbox. HDP Sandbox is based on CentOS 



After the boot operation is done, you will see the following screen where it gives the address to access the splash webpage of the platform at http://loaclhost:1080 or http://127.0.0.1:1080 for HDP 2.6.5.
<p align="center">
  <img src="https://i.imgur.com/gpslFzA.png" width="600"/>
  <br>HDP Sandbox 2.6.5
</p>


Now, you have finished installation and are ready to access the cluster.

## B. Using Docker
Here I will explain how you can install HDP Sandbox using Docker in Windows *11*. For Linux users, installing Docker can be done via CLI and you can follow [this tutorial](https://docs.docker.com/engine/install/ubuntu/).

### 1. Installing Docker Desktop on Windows
You can download Docker Desktop and install it by following the instructions in the [official website](https://docs.docker.com/desktop/install/windows-install/). The installation instructions are easy and you can follow this [tutorial](https://docs.docker.com/desktop/install/windows-install/#install-docker-desktop-on-windows).

<p align="center">
  <img src="https://i.imgur.com/8F6Rpxc.png" width="600"/>
  <br>Docker Desktop
</p>


Docker Desktop allows you to manage images and containers via GUI. It also allows you to run the cluster without the need to use CLI.

### 2. Installing HDP Sandbox
There are only two versions of HDP Sandbox on Docker Hub where **HDP version 2.6.5** is what we need. Firstly, you need to download the installation scripts from Cloudera website:
- [**HDP 2.6.5**](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-2.6.5/HDP_2.6.5_deploy-scripts_180624d542a25.zip)
- [**HDP 3.0.1**](https://archive.cloudera.com/hwx-sandbox/hdp/hdp-3.0.1/HDP_3.0.1_docker-deploy-scripts_18120587fc7fb.zip)


The zip folder mainly contains `.sh` scripts which includes instructions to pull the HDP Sandbox image from Docker Hub and run the containers for your cluster. 

<p align="center">
  <img src="https://i.imgur.com/RUKLdog.png" width="600"/>
  <br>HDP Sandbox on Docker
</p>




For Linux users, it will be straightforward to run `.sh` scripts whereas for Windows *10/11* users you can use `C://Windows/System32/bash.exe` program. If you have an older version of Windows then you can install [Git BASH](https://gitforwindows.org) to run `.sh` scripts.

We need to run only the script `docker-deploy-hdp265.sh` from `bash` shell as follows:
```sh
$ bash ./docker-deploy-hdp265.sh
```
<p align="center">
  <img src="https://i.imgur.com/5VPcE0L.png" width="600"/>
  <br>Pulling HDP Sandbox 2.6.5 image from Docker Hub
</p>


Running the installation script will take a lot of time since it will pull *15 GiB* (HDP 2.6.5) from Docker Hub, so take a break. If you cannot see the progress of the download, then you can open another *terminal* and run the same instruction as follows:
```sh
docker pull hortonworks/sandbox-hdp:2.6.5
```
After the installation is successfully done, you will see two images on Docker Desktop and also two running containers.

<p align="center">
  <img src="https://i.imgur.com/wwWS6Tf.png" width="600"/>
  <br>HDP Sandbox images on Docker Desktop
</p>




**The HDP Sandbox cluster is now running on your PC :smiley:.**


You do these steps only for the first time then you can stop/restart the cluster from Docker Desktop.

## Configuring the resources for Docker
Limiting the resources of the containers is important to avoid freezing the host system due to the lack of memory. The default behavior of Docker engine is to give the containers as much resources as they request. Linux users can use command line options whereas Windows users can use configuration of WSL backend (which is installed by default in Windows *10/11* but do not forget that you need to install a Linux distribution such as Ubuntu, see tutorial [here](https://pureinfotech.com/install-windows-subsystem-linux-2-windows-10/)) or Hyper-V backend. Here I will explain how you can configure the resources of the containers if you use WSL backend in Windows. 

You need to download `.wslconfig` [file](https://gist.github.com/firas-jolha/097d188a9b25179f9906d67fbf652d5c) or build a new one with the same structure as follows:
```yaml
# NOTE: This should be stored in %UserProfile% folder which is your home folder c:/Users/&lt;Username&gt; in Windows
# Settings apply across all Linux distros running on WSL 2
[wsl2]
# Limits VM memory to use no more than 4 GB, this can be set as whole numbers using GB or MB
memory=4GB
# Sets the VM to use four virtual processors
processors=4
# Sets amount of swap storage space to 0, default is 25% of available RAM
swap=0
```



> After every update of `.wslconfig` file, you need to shutdown `WSL`, wait for some seconds then restart Desktop Docker. After that, you can start the containers to run the cluster. You can shutdown `WSL` as:
```sh
wsl --shutdown
```
:::
More info about WSL configuration can be found [here](https://learn.microsoft.com/en-us/windows/wsl/wsl-config).

## Stopping/Restarting the cluster
You can stop the cluster by turning off the virtual machine in case you are using a hypervisor. For Docker users, you only need to stop the containers `sandbox-hdp` and `sandbox-proxy`. Meanwhile, you can start the cluster in Docker Desktop by running the containers `sandbox-hdp` and `sandbox-proxy` whereas you just need to start the virtual machine in case you are using a hypervisor.



> **Important:** Do **not** delete the containers in Docker Desktop, otherwise the persisted data will be gone and you need to reinstall the cluster using installation scripts.
&lt;!-- Indeed, you can create multiple containers from a single Docker image for testing purposes. 


# Access HDP Sandbox cluster
Either you followed the first approach or the second one in cluster installation, you would end up here. The installed HDP  Sandbox cluster is a single node implementation of HDP. It is packaged as a virtual machine to make evaluation and experimentation with HDP fast and easy. You can access the splash webpage of the cluster via http://localhost:1080 for HDP 2.6.5 and http://localhost:8888 for HDP 2.5.0.


<p align="center">
  <img src="https://i.imgur.com/9RFzfpO.png" width="600"/>
  <br>HDP Sandbox splash webpage
</p>


The button *Quick Links* will transfer you to the page of links where you can access some services of the cluster.

<p align="center">
  <img src="https://i.imgur.com/8mOrZrX.png" width="600"/>
  <br>HDP Sandbox quick links webpage
</p>


In order to see all services of the cluster, you need to access *Ambari* service at http://localhost:8080 where you can monitor and manage all services.

<p align="center">
  <img src="https://i.imgur.com/xsWhlVR.png" width="600"/>
  <br>Ambari login page
</p>


You need to log in in order to access this service. You can use the credentials of the user `maria_dev/maria_dev` as (username/passowrd). HDP Sandbox comes with 4 default users with different roles in the cluster and there is also Ambari Admin who can manage the other users in the cluster.


<p align="center">
  <img src="https://i.imgur.com/2JEYSJd.png" width="600"/>
  <br>Ambari homepage
</p>


As you can see in Ambari homepage, most services are showing alerts since they did not start so far or due to some problems. You need to wait till services start then you can access them. If you have set less resources than required then it is probably that most services cannot be run, so you can stop the services which are not needed to let the required services run.


> Note: You can reset the password of Ambari Admin by running the command `ambari-admin-password-reset` via `ssh` as follows:
```sh
[root@sandbox-hdp ~]# ambari-admin-password-reset
```

<!-- ![]() -->
<p align="center">
  <img src="https://i.imgur.com/sz0KH33.png" width="600"/>
  <br>Resetting Ambari Admin credentials
</p>


<p align="center">
  <img src="https://i.imgur.com/DpuMnVB.png" width="800"/>
  <br>Overview of HDP services
</p>






## Access via SSH

You can access the cluster via shell web client or called `shell-in-a-box` by following the address http://localhost:4200 in your browser.

For the very first time, the default credentials are `root/hadoop` and you will be asked to reset the password. You need to set a strong password to pass the password-reset step. For example, I use the password `hdpIU2023`.

<p align="center">
  <img src="https://i.imgur.com/CzJCiNQ.png" width="600"/>
  <br>Web Shell client for HDP Sandbox
</p>



You can also access the cluster via `ssh` command on your preferred terminal. You need to `ssh` on the port **2222** as root user:
```sh
ssh root@localhost -p 2222
```

## Configuring Ambari Admin 


# Access HDFS
You can access HDFS files by selecting `Files view` in Ambari homepage.

<p align="center">
  <img src="https://i.imgur.com/AuB9pbo.png" width="600"/>
  <br>Ambari - Files View
</p>


You can see in the following screen the contents of HDFS on the cluster. The page allows you to upload and download files/folders from/to local file system and HDFS.
<p align="center">
  <img src="https://i.imgur.com/DlMQDdh.png" width="600"/>
  <br>HDFS on HDP Sandbox cluster
</p>



> You can also access HDFS via CLI using the command `hdfs dfs`. For example, to list the content of the root directory `/` in HDFS, you can write as follows:
```sh
[root@sandbox-hdp ~]# hdfs dfs -ls /
```


# Transfer data between HDFS and local file systems
The single node of the cluster is running on CentOS which has *ext4* local file system whereas the distributed data in the cluster is stored in HDFS. You also have a local file system on the host machine. We have multiple file systems among whom data may need to be transferred. For example, in order to process the data in the cluster, you need to store it in HDFS.



*and data blocks are replicated in multiple nodes to increase availability and ensure that there is no data loss if one of the nodes holding the data failed*


## Transfer data into and out of the cluster node
You can move data from the local file system in the host machine to the local file system of the cluster node by using command `docker cp`. As an example, to move the file `C:\Users\Admin\Desktop\hello.txt` on Windows to the root directory `/` of the node, we run the following command on the host machine:
```sh
docker cp  "C:\Users\Admin\Desktop\hello.txt" 
```

> `sandbox-hdp` is the container name/id.



In the same way, you can move the file `/hello2.txt` from the local file system of the node to the local file system of the host machine as follows (run the command on the host machine):
```sh
docker cp "sandbox-hdp:/hello2.txt&" "C:\Users\Admin\Desktop"
```

> Make sure that the containers of the cluster are running before running the commands.



> Info: If you installed HDP using a hypervisor, then you can use the command `scp` to immediately copy files from the source machine (where you are running the command) to the local file system of the cluster node.
For instance, to transfer the files in *data* folder from the host machine to the folder `/data` in the cluster node, we run the following command on the terminal.

```sh
scp -P 2222 data/* root@localhost:/data
```

Note: If you got issues as shown in the figure below, then you need to open the file `%USERPROFILE%/.ssh/known_hosts` and remove the previous keys for that port (You can empty the file if all keys are not important).
![](https://i.imgur.com/fKe5BNZ.png)

`scp` will create a new key and exchange it with the cluster node.



*There is also another method to move data into and out of the Docker container*
## Transfer data into and out of HDFS
You can move data between the local file system of the host machine and HDFS via Ambari service as explained in section [Access HDFS](#Access-HDFS).

To move data from HDFS to the local file system of the cluster node, you can use `hdfs dfs` command with appropriate options. The table below shows usage of some options.


![](https://i.imgur.com/OfzCZP2.png)



| Option | Description | Command example | Command explanation |
| -------- | -------- | -------- | ----------- |
| -cp     | Copies files and folders within HDFS (add -f to overwrite)     | `hdfs dfs -cp /file.txt /names.txt`     |  |




# Common issues
* `Docker` does not read the parameters in `.wslconfig` file on Windows.
    * Possible reasons:
        * The file is sensitive to white spaces and character cases. If the file has some issues in some lines then WSL backend of Docker Desktop will ignore the file.
    * Possible solutions:
        * Make sure that your file follows the structure that I specified [here](https://gist.github.com/firas-jolha/097d188a9b25179f9906d67fbf652d5c) and change only values according to your limited resources.


<p align="center">
  <img src="https://i.imgur.com/SYXnMTC.png" width="600"/>
  <br>Cloudera Manager script
</p>




* The virtual machine does not boot up.
    * Possible reasons:
        * You set less resources than minimum for the CPU or the RAM.
    * Possible solutions:
        * Increase the number of cores for CPU and/or increase the allocated RAM.

* When you try to access the database by running the command `psql -U postgres` as `[root@sandbox-hdp data]# psql -U postgres`. You may get the following error:
    &gt; psql: FATAL:  Peer authentication failed for user &#34;postgres&#34;  
    * Possible reasons:
        * The database is configured to allow access only through the credentials of the user.
    * Possible solutions:
        * You can change this configuration to allow all users to access the database without the need to enter credentials. Add the line `local all all trust` at the beginning of the file `/var/lib/pgsql/data/pg_hba.conf` then restart PostgreSQL service by running the command `systemctl restart postgresql` as root user.


> **NB:** If you have other issues, please contact me.

# Self-check questions
1. What are the key components of Hadoop core?
2. What is `Apache Ambari`?
3. How can we import a virtual appliance in VirtualBox?
4. How can we set the password of `Ambari Admin`?
5. What is the version of Hadoop distribution in the installed cluster?
6. What services does HDP provide?
7. How can you upload a file from local file system to HDFS?
8. How can you download a file from HDFS to the local file system?
9. How can you access the cluster via `ssh`?
10. How can we access the cluster via *shell-in-a-box*?
11. How can we access the database `ambari` in PostgreSQL server?
12. What are the default credentials of `root` user in the cluster?
13. How to transfer files from the host machine to the cluster node?


# Appendix
## Sandbox Version
To find information about the cluster Sandbox, execute the following command on the cluster container:
```sh
sandbox-version
```
## Ambari Roles
Ambari 2.4 introduced the notion of Role-Based Access Control(RBAC) for the Ambari web interface. Ambari now includes additional cluster operation roles providing more granular division of control of the Ambari Dashboard and the various Ambari Views. Only the admin id has access to view or change these roles. You can learn more about the roles from the [here](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#login-credentials).



# References
[Cloudera Quickstart VM Installation: The Best Way](https://www.simplilearn.com/tutorials/big-data-tutorial/cloudera-quickstart-vm)

[Cloudera Quickstart VM - Docker version](https://docs.cloudera.com/documentation/enterprise/latest/PDF/cloudera-quickstart.pdf) 

[Learning the Ropes of the HDP Sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html)

[Getting Started with HDP Sandbox](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html)

[Hortonworks - Wikipedia](https://en.wikipedia.org/wiki/Hortonworks)

[IBM Analytics - Data Sheet](https://www.ibm.com/downloads/cas/DKWR4KZB)

[PostgreSQL: The World&#39;s Most Advanced Open Source Relational Database](https://www.postgresql.org)

[Moving data in HDFS](https://gdancik.github.io/CSC-343/data/notes/HDFS.pdf)

[Hadoop HDFS Command Cheatsheet](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html)

[HDP Cluster Roles](https://docs.cloudera.com/HDPDocuments/Ambari-2.5.1.0/bk_ambari-administration/content/cluster_roles.html)



---------------------------------
**Hortonworks Sandbox HDP 3.0**



