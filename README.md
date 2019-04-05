# Udacity-Linux-Configuration

Take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

## Server Information

### IP Address and SSH Port
`IP Address: 35.166.108.139`
`SSH Port: 2200`

### Connect To Server As Grader
A SSH key called grader.pem is created to allow one to connect as the user called grader.

`ssh -i .ssh/grader.pem grader@35.166.108.139 -p 2200`

### URL to Web Host Application

Link: [35.166.108.139/catalog](35.166.108.139/catalog)

## Setup

### Create a New Server Instance on Amazon Lightsail

Follow the necessary steps provided by Udacity in the **Get started on Lightsail** for the **Linux Server Configuration Project**.

### Secure The Server

#### Updating Packages

After getting the server, the first step is to update all the currently installed packages. This can be done with the following commands:
```
$ sudo apt-get update
$ sudo apt-get upgrade
```

If not all packages are updated/upgraded, use the following command:
```
$ sudo apt-get dist-upgrade
```
#### Changing SSH Port

As a part of securing the server, changing the port from the default port is necessary.

First, the Lightsail firewall configuration needs to change to allow the port we want to use. The SSH Port will need to be changed from **Port 22** to **Port 2200**.