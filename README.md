# Udacity-Linux-Configuration

Take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

## Server Information

### IP Address and SSH Port
```
IP Address: 35.166.108.139
SSH Port: 2200
```

### Connect To Server As Grader
A SSH key called grader.pem is created to allow one to connect as the user called grader.
```
ssh -i .ssh/grader.pem grader@35.166.108.139 -p 2200
```

### URL to Web Host Application

Link: [35.166.108.139/catalog](35.166.108.139/catalog)

## Setup

## Create a New Server Instance on Amazon Lightsail

Follow the necessary steps provided by Udacity in the **Get started on Lightsail** for the **Linux Server Configuration Project**.

## Secure The Server

### Updating Packages

After getting the server, the first step is to update all the currently installed packages. This can be done with the following commands:
```
$ sudo apt-get update
$ sudo apt-get upgrade
```

If not all packages are updated/upgraded, use the following command:
```
$ sudo apt-get dist-upgrade
```
### Changing SSH Port

As a part of securing the server, changing the port from the default port is necessary.

#### Step 1
The Lightsail firewall configuration needs to change to allow the port we want to use. The SSH Port will need to be changed from **Port 22** to **Port 2200**.

On the Amazon Lightsail server, go to the **Networking** tab. Under the **Firewall** section, click on the 'Add another' button. Fill in the row to look like the following:
```
Application          Protocol             Port range
-----------------------------------------------------
   Custom              TCP                  2200
```
> Note: It is important to set this up so that you do not lock yourself out of the server. When changing the SSH port, the Lightsail instance will no longer be accessible through the weba pp 'Connect using SSH' button. Connecting through the web app is somewhat unstable as it can disconnect on occasions.

#### Step 2
Modify the sshd_config file with the following command:
```
$ sudo vim /etc/ssh/sshd_config
```

Change the **Port** from 22 to 2200

#### Step 3
Configure the Uncomplicated Firewall (UFW) to allow incoming connections for SSH (Port 2200), HTTP (Port 80), and NTP (Port 123).

First, check to see the status of the UFW with the following commands:
```
$ sudo ufw status
```
> If this is a new instance, the status should return **inactive**

Secondly, it is important to block all incoming and allow all outgoing requests. Use the following commands:
```
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
```
Now the configuration for SSH, HTTP, and NTP can be set with the following commands:
```
$ sudo ufw allow 2200/tcp
$ sudo ufw allow www
$ sudo ufw allow ntp
```
With the configurations set, the firewall is ready to be enabled. Use the following commands:
```
$ sudo ufw enable
$ sudo ufw show status
```
UFW should display the allowed connections that were set above.

## Give `grader` Access To Web Server
In order to allow the project to be reviewed, a grader user was created and setup to allow another user to log in to the server.

### Create a New Account User
To create a new user in Ubuntu, perform the following command:
```
sudo adduser grader
```
### Give `grader` Permission To `sudo`
To give the user `sudo` access, we will need to edit the sudoers file to give grader access. To edit the file, use the following command:
```
$ sudo vim /etc/sudoers.d/grader
```
Add the following line to the file:
```
grader ALL=(ALL:ALL) ALL
```
> Note: Make sure there are no syntactic errors or grammatic mistakes in the file. If incorrectly written, it may corrupt the sudo command.

### Generate the SSH Key
In order for the grader to access the web server instance, a SSH key must be created on the local machine and the public key should be placed on the server. This will allow a handshake to be made between the local machine and the server.

Go on a local machine, a Linux or vagrant VM, and generate a SSH key. This can be done with the following command:
```
$ cd .ssh/
$ ssh-keygen
```
When ssh-keygen is ran, it will generate two keys, a private key and a public key. Name the file as `grader` and now there should be two files - `grader` and `grader.pub`.

Copy the contents of `grader.pub` to the Lightsail server with the following steps:
1. On the Lightsail server, switch to the `grader` user. 
```
$ sudo su - grader
```

> Performing the '-' in `su - grader` will also move the current directory to the user's home directory. 

2. Create a directory called `.ssh` and set the permissions to only allow the grader to read, write, or execute anything in that file.
```
$ sudo mkdir .ssh/
$ sudo chown grader:grader /home/grader/.ssh
$ sudo chmod 700 /home/grader/.ssh
```
3. In the `.ssh` directory, create a file called `authorized_keys` and paste the contents from `grader.pub` into this file. Once created, set the permissions to 400
```
$ cd .ssh/
$ sudo vim authorized_keys
$ sudo chmod 400 authorized_keys
```

Now that the contents have been copied over, we can go back to the local machine and modify some files and then try to SSH into the Amazon Lightsail server.

On the local machine, navigate to the `.ssh` folder if not in the directory already. Change the name of the file `grader` to `grader.pem` with the following command:
```
$ mv grader grader.pem
```

Connect to the Amazon Lightsail server with the command mentioned in the section above called `Connect To Server As Grader`. The following command is the same command:
```
$ ssh -i .ssh/grader.pem grader@35.166.108.139 -p 2200
```
Once connected, it is no longer needed for a password as the public key authentication is strong enough. To disable the root login for SSH, modify `/etc/ssh/sshd_config` with the following command:
```
$ sudo vim /etc/ssh/sshd_config
``` 
Modify the following:
1. `PermitRootLogin without-password` to `PermitRootLogin no`
2. Uncomment `PasswordAuthentication no`

After making the following changes, restart ssh with the following command:
```
$ sudo service ssh restart
```

## Deploy Project To Server
