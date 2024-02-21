
```
[sumit_chaubey@localhost ~]$ cat /etc/hosts
```
OUTPUT
```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1     	localhost localhost.localdomain localhost6 localhost6.localdomain6
Set the hostname:
```
```
[sumit_chaubey@localhost ~]$ su - root
Password:
```
su: This is the command for switching users.

root: The username to which you want to switch. In this case, it's the root user.

```
[root@localhost ~]# hostname
localhost.localdomain
```

```
[root@localhost ~]# hostnamectl set-hostname sumit.ocp.com
```
hostnamectl: This is a command-line utility on Linux systems that allows for querying and changing the system hostname and related settings.

set-hostname: This is an option used with hostnamectl to set the system's hostname.

sumit.ocp.com: This is the new hostname you are setting for your system. Replace it with the desired hostname for your machine.
```
[root@localhost ~]# hostname
Sumit.ocp.com
```



# Setup DNS server:
```
[root@localhost ~]# yum install bind -y
```
yum: This is a package manager used on Red Hat-based Linux distributions such as CentOS, Fedora, and Red Hat Enterprise Linux (RHEL). It is used to install, update, and manage software packages.

install: This is a command used with yum to install packages.

bind: This is the name of the package being installed. In this case, it refers to the BIND DNS server package.

-y: This is an option used with yum to automatically answer "yes" to any prompts or confirmation messages during the installation process. It is useful for automated or unattended installations.

Output
```
Updating Subscription Management repositories.
Last metadata expiration check: 4:11:57 ago on Wednesday 10 January 2024 01:05:56 PM IST.
Dependencies resolved.
=====================================================================================================================================================
 Package                      	Architecture       	Version                          	Repository                                    	Size
=====================================================================================================================================================
Installing:
 bind                         	x86_64             	32:9.11.36-11.el8_9              	
Transaction Summary
Install  8 Packages

Total download size: 24 M
Installed size: 65 M
Downloading Packages:
(1/8): libmaxminddb-1.2.0-10.el8.x86_64.rpm                                                                      	6.3 kB/s |  33 kB 	00:05    
(2/8): geolite2-country-20180605-1.el8.noarch.rpm                                                                	174 kB/s | 1.0 MB 	00:06    
(3/8): geolite2-city-20180605-1.el8.noarch.rpm                                                                   	2.9 MB/s |  19 MB 	00:06    
(4/8): fstrm-0.6.1-3.el8.x86_64.rpm                                                                              	7.9 kB/s |  29 kB 	00:03    
(5/8): bind-9.11.36-11.el8_9.x86_64.rpm                                                                          	564 kB/s | 2.1 MB 	00:03    
(6/8): bind-libs-9.11.36-11.el8_9.x86_64.rpm                                                                      	43 kB/s | 176 kB 	00:04    
(7/8): bind-license-9.11.36-11.el8_9.noarch.rpm                                                                   	43 kB/s | 104 kB 	00:02    
(8/8): bind-libs-lite-9.11.36-11.el8_9.x86_64.rpm                                                                	299 kB/s | 1.2 MB 	00:04    
-----------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                            	1.7 MB/s |  24 MB 	00:14	 
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
libmaxminddb-1.2.0-10.el8.x86_64        	 

Complete!
```




#  vim /etc/named.conf 
vim: This is a text editor commonly used in Unix-like operating systems. It allows you to view and edit text files.

/etc/named.conf: This is the path to the main configuration file for the BIND DNS server.It's the default location for the named configuration file on many Linux distributions.

```
options {
    	listen-on port 53 { 192.168.1.8; };
    	listen-on-v6 port 53 { ::1; };
    	directory   	"/var/named";
    	dump-file   	"/var/named/data/cache_dump.db";
    	statistics-file "/var/named/data/named_stats.txt";
    	memstatistics-file "/var/named/data/named_mem_stats.txt";
    	secroots-file   "/var/named/data/named.secroots";
    	recursing-file  "/var/named/data/named.recursing";
    	allow-query 	{ 192.168.1.0/24; };


zone "ocp.com" {
 type master;
 file "forward.zone";
};
zone "1.168.192.in-addr.arpa" {
 type master;
 file "reverse.zone";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

![Alt text](<Screenshot from 2024-02-08 22-47-12.png>)

![Alt text](<Screenshot from 2024-02-08 22-47-55.png>)



## Go to forward.zone file
```
#vim forward.zone

after enter the vim file 
$TTL 1D
@   	IN SOA ocp.com. root.sumit.ocp.com. (
                                    	0   	; serial
                                    	1D  	; refresh
                                    	1H  	; retry
                                    	1W  	; expire
                                    	3H )	; minimum
    	NS  	sumit.ocp.com.
sumit.ocp.com. A 192.168.1.8
ocp.com. A 192.168.1.8
www.ocp.com. CNAME sumit.ocp.com.
@ MX 10 sumit.ocp.com.
```

vim: This is the Vim text editor, commonly used on Unix-like operating systems.

forward.zone: This is the name of the file you are opening. The file extension (like .zone) often indicates that it might be a DNS zone file. DNS zone files are used to define mappings between domain names and IP addresses.




# cat /var/named/forward.zone
This command is use to show the change which you have done in vim forward.zone.

![Alt text](<Screenshot from 2024-02-08 22-58-06.png>)

## Go to reverse.zone
```
#vim reverse.zone


$TTL 1D
@   	IN SOA ocp.com. root.sumit.ocp.com. (
                                    	0   	; serial
                                    	1D  	; refresh
                                    	1H  	; retry
                                    	1W  	; expire
                                    	3H )	; minimum
    	NS 	sumit.ocp.com.
8 PTR ocp.com.
8 PTR sumit.ocp.com.
```
vim: This is the Vim text editor, commonly used on Unix-like operating systems.

reverse.zone: Reverse DNS is used to map IP addresses to domain names. If reverse.zone is indeed a reverse DNS zone file, it likely contains mappings between IP addresses and corresponding domain names.


#  cat /var/named/reverse.zone
We can confirm our file is save or not by using this command

![Alt text](<Screenshot from 2024-02-08 22-59-13.png>)

### Restart service

 ```
 systemctl restart named
 ```
 systemctl: This is a command-line utility for controlling the systemd system and service manager. It allows you to start, stop, restart, enable, or disable services and other units.

restart: This is an argument indicating that you want to restart the specified service.

named: This is the service name for the BIND DNS server. The name "named" is often used for BIND in various Linux distributions.


### Enable service
  ```
  systemctl enable named
```
Created symlink /etc/systemd/system/multi-user.target.wants/named.service → /usr/lib/systemd/system/named.service.



# Installing bind utils:
```
[root@sumit named]# yum install bind-utils
```
Output:

Updating Subscription Management repositories.

This system is registered with an entitlement server, but is not receiving updates. You can use subscription-manager to assign subscriptions

<br>
Last metadata expiration check: 0:57:06 ago on Thursday 11 January 2024 11:26:05 AM IST.
Dependencies resolved.
======================================================================================================================================= =====================================================================================================================================================
Install  2 Packages
Total download size: 604 k
Installed size: 1.5 M
Is this ok [y/N]: y
Downloading Packages:
(1/2): bind-utils-9.11.36-11.el8_9.x86_64.rpm                                                                    	1.1 MB/s | 453 kB 	00:00    
(2/2): python3-bind-9.11.36-11.el8_9.noarch.rpm                                                                  	343 kB/s | 151 kB 	00:00    
-----------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                            	1.3 MB/s | 604 kB 	00:00	 
Running transaction check
Transaction check succeeded.

Installed:
  bind-utils-32:9.11.36-11.el8_9.x86_64                                	python3-bind-32:9.11.36-11.el8_9.noarch                              	 

Complete!


![Alt text](<Screenshot from 2024-02-09 22-45-30.png>)

yum: This is a package manager used on Red Hat-based Linux distributions such as CentOS, Fedora, and Red Hat Enterprise Linux (RHEL). It is used to install, update, and manage software packages.

install: This is a command used with yum to install packages.

bind-utils: This is the name of the package you are installing. The BIND utilities package includes tools like dig, nslookup, and host, which are commonly used for DNS-related tasks and troubleshooting.

# To allow DNS service:
```
[root@sumit named]# nslookup sumit.ocp.com
```
Output:

Server:   	 192.168.1.8

Address:    192.168.1.8#53

Name:    sumit.ocp.com

Address: 192.168.1.8

![Alt text](<Screenshot from 2024-02-20 14-50-25.png>)

nslookup: command is used to query DNS (Domain Name System) servers to obtain domain name or IP address information. In your case, you are trying to look up information for the domain "sumit.ocp.com." The nslookup command provides details about the domain's DNS records, including its associated IP addresses.

```
[root@sumit named]# nslookup 192.168.1.8
```
Output:

8.1.168.192.in-addr.arpa    name = sumit.ocp.com.

8.1.168.192.in-addr.arpa    name = ocp.com.
![Alt text](<Screenshot from 2024-02-20 14-51-54.png>)



# Setup single node Openshift Cluster
 ### Make directory
 [sumit_chaubey@sumit ~]$ mkdir ocp4
 ### Change directory
[sumit_chaubey@sumit ~]$ cd ocp4

# Set the Openshift Container Platform version
   OCP_VERSION=latest-4.12

# Set the host architecture
   ARCH=x86_64

## Install podman:
```
[sumit_chaubey@sumit ~]$ sudo yum install podman -y
[sudo] password for sumit_chaubey:
```
Output:

Updating Subscription Management repositories.
Last metadata expiration check: 3:04:40 ago on Thursday 11 January 2024 02:32:20 PM IST.
Package podman-3:4.6.1-4.module+el8.9.0+20326+387084d0.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
![Alt text](<Screenshot from 2024-02-09 17-28-39.png>)



### Change the directory
[sumit_chaubey@sumit ~]$ cd ocp4

### Make directory
 [sumit_chaubey@sumit ocp4]$ mkdir ocp

[sumit_chaubey@sumit ocp4]$ ls

Output:
ocp



# This command is download the OpenShift Container Platform client (oc) and make it available for use:
```
[sumit_chaubey@sumit ocp]$ curl -k https://mirror.openshift.com/pub/openshift-v4/clients/ocp/$OCP_VERSION/openshift-client-linux.tar.gz -o oc.tar.gz
```
Output:

  % Total	% Received % Xferd  Average Speed   Time	Time 	Time  Current
                             	Dload  Upload   Total   Spent	Left  Speed
100 55.3M  100 55.3M	0 	0  6686k  	0  0:00:08  0:00:08 --:--:-- 10.8M



curl: The command itself for making network requests.

-k: This option allows curl to perform the transfer even if the SSL certificate of the server cannot be verified. It's commonly used for testing or if you are certain about the source but the certificate is not trusted.

https://mirror.openshift.com/pub/openshift-v4/clients/ocp/$OCP_VERSION/openshift-client-linux.tar.gz: The URL from which the file will be downloaded. The $OCP_VERSION is a variable that should be defined in your shell environment, providing the version of OpenShift. Make sure to set it before running the command.

-o oc.tar.gz: This option specifies the output file name for the downloaded content. In this case, the downloaded file will be saved as oc.tar.gz.

So, when you run this command, it downloads the OpenShift client binary archive for the specified version and saves it as oc.tar.gz in the current working directory. After the download is complete, you can extract the contents of the archive and use the OpenShift client (oc) for managing OpenShift clusters.

```
[sumit_chaubey@sumit ocp]$ tar zxf oc.tar.gz
```
tar: The command for manipulating tar archives.

z: This option specifies that the archive is compressed with gzip.

x: This option tells tar to extract the contents of the archive.

f: This option is used to specify the archive file to work with.
 ```
 [sumit_chaubey@sumit ocp]$ chmod +x oc
```
chmod: The command for changing file permissions.

+x: This option adds the execute permission to the specified file.

oc: This is the name of the file for which you are modifying permissions. In this context, it's the OpenShift client executable.
```
[sumit_chaubey@sumit ocp]$  sudo cp oc /usr/bin
```
sudo: This is used to execute the command with superuser (root) privileges, as copying files to system directories often requires elevated permissions.

cp: This is the copy command.

oc: This is the source file (OpenShift client executable) that you want to copy.

/usr/bin: This is the destination directory where you want to copy the file. /usr/bin is a standard directory for executable files in many Linux distributions.

# This command is download Openshift Container Platform installer and make it available for use:

```
[sumit_chaubey@sumit ocp]$ curl -k https://mirror.openshift.com/pub/openshift-v4/clients/ocp/$OCP_VERSION/openshift-install-linux.tar.gz -o openshift-install-linux.tar.gz
```

Output
  % Total	% Received % Xferd  Average Speed   Time	Time 	Time  Current
                             	Dload  Upload   Total   Spent	Left  Speed
100  336M  100  336M	0 	0  10.0M  	0  0:00:33  0:00:33 --:--:-- 10.8M






curl: The command-line tool for making HTTP requests.

-k: This option allows curl to perform the transfer even if the SSL certificate of the server cannot be verified. It's commonly used for testing or if you are certain about the source but the certificate is not trusted.

https://mirror.openshift.com/pub/openshift-v4/clients/ocp/$OCP_VERSION/openshift-install-linux.tar.gz: The URL from which the file will be downloaded. The $OCP_VERSION is a variable that should be defined in your shell environment, providing the version of OpenShift. Ensure that you set it before running the command.

-o openshift-install-linux.tar.gz: This option specifies the output file name for the downloaded content. In this case, the downloaded file will be saved as openshift-install-linux.tar.gz.



```
 [sumit_chaubey@sumit ocp]$ tar zxvf openshift-install-linux.tar.gz
```
Output:
README.md
Openshift-install





tar: The command for manipulating tar archives.

z: This option specifies that the archive is compressed with gzip.

x: This option tells tar to extract the contents of the archive.

v: This option stands for "verbose," and it prints the names of the files being extracted to the terminal.

f: This option is used to specify the archive file to work with.



```
[sumit_chaubey@sumit ocp]$ chmod +x openshift-install
```
chmod: The command for changing file permissions.

+x: This option adds the execute permission to the specified file.

openshift-install: This is the name of the file for which you are modifying permissions. In this context, it's the OpenShift installer binary.


# Retrieve the RHCOS ISO URL by running the following command:

ISO_URL=$(./openshift-install coreos print-stream-json | grep location | grep $ARCH | grep iso | cut -d\" -f4)


./openshift-install: Executes the OpenShift installer binary.

coreos print-stream-json: This is an OpenShift installer command that prints CoreOS stream information in JSON format.

grep location: Filters lines that contain the word "location."

grep $ARCH: Further filters lines based on the value of the $ARCH variable. The $ARCH variable typically represents the architecture of the system (e.g., x86_64).

grep iso: Filters lines containing the word "iso."

cut -d\" -f4: Uses the cut command to extract the fourth field from each line, using the double quote (") as the delimiter. This is assuming that the URL is surrounded by double quotes.

ISO_URL=$(...): Assigns the extracted URL to the variable ISO_URL.

### Download the RHCOS ISO:

```
curl -L $ISO_URL -o rhcos-live.iso
```
curl: The command-line tool for making HTTP requests.

-L: This option tells curl to follow redirects. If the URL provided in $ISO_URL redirects to another location, -L ensures that curl follows the redirection.

$ISO_URL: This is the variable containing the URL of the CoreOS ISO file obtained in the previous step.

-o rhcos-live.iso: This option specifies the output file name for the downloaded content. In this case, the downloaded file will be saved as rhcos-live.iso.


# Create the file
```
 [sumit_chaubey@sumit ocp]$ vim install-config.yaml
```
Output:
![Alt text](<Screenshot from 2024-02-09 00-26-56.png>)

```
 [sumit_chaubey@sumit ocp]$ ssh-keygen -f ocp4
```
Output:

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in ocp4.
Your public key has been saved in ocp4.pub.
The key fingerprint is:
SHA256:3/7kd2uJX1h3JtxiCYSXuc+wtWixWitPXL8C58597FE sumit_chaubey@sumit.ocp.com
The key's randomart image is:
+---[RSA 3072]----+
|       	..o   |
|      	..+	|
|       	...   |
|        	+o.o |
|    	S	XB.E|
|     	. +*++B+|
|      	.+B.+o+|
|      	ooo*.oO|
|       	o++*B=|
+----[SHA256]-----+

![Alt text](<Screenshot from 2024-02-09 00-31-25.png>)

sh-keygen: The command for generating SSH key pairs.

-f ocp4: This option specifies the file name of the generated key pair. In this case, it's set to ocp4.


```
[sumit_chaubey@sumit ocp]$ cat ocp4.pub
```
Output:

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCYjlUJuSKp/FTeGi80timPJk1CJrUjQ782nXV2UbOSJdf/ZGG4RLanpo9tJLSqp9/X4zRNqRnQEKqpJB8Y/a1TZkGhX7sOuSrWzbzIV7y

![Alt text](<Screenshot from 2024-02-09 00-34-22.png>)

 cat ocp4.pub :command is used to display the contents of the public key file (ocp4.pub). 

### Copy install.config.yaml in ocp
```
[sumit_chaubey@sumit ocp]$ cp install-config.yaml ocp
```



[sumit_chaubey@sumit ocp]$ ls
Output:

install-config.yaml  kubectl  oc  ocp  ocp4  ocp4.pub  oc.tar.gz  openshift-install  openshift-install-linux.tar.gz  README.md  rhcos-live.iso
```
[sumit_chaubey@sumit ocp]$ cd ocp/
```
```
[sumit_chaubey@sumit ocp]$ ls
```
Output:

auth  bootstrap-in-place-for-live-iso.ign  metadata.json  worker.ign
```
[sumit_chaubey@sumit ocp]$ sudo netstat -tulnp
[sudo] password for sumit_chaubey:
```
Output:

Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address       	Foreign Address     	State   	PID/Program name    
tcp    	0  	0 0.0.0.0:7070        	0.0.0.0:*           	LISTEN  	22181/anydesk  	 
tcp    	0  	0 0.0.0.0:44008       	0.0.0.0:*           	LISTEN  	22181/anydesk  	 
tcp    	0  	0 0.0.0.0:111         	0.0.0.0:*           	LISTEN  	1/systemd      	 
tcp    	0  	0 192.168.1.8:53      	0.0.0.0:*           	LISTEN  	5760/named     	 
tcp    	0  	0 192.168.122.1:53    	0.0.0.0:*           	LISTEN  	1882/dnsmasq   	 
tcp    	0  	0 0.0.0.0:22          	0.0.0.0:*           	LISTEN  	1051/sshd      	 
tcp    	0  	0 127.0.0.1:631       	0.0.0.0:*    

![Alt text](<Screenshot from 2024-02-09 00-44-37.png>)

sudo: This command is used to execute the following command with superuser (root) privileges. It's necessary for viewing detailed information about network connections and listening ports.

netstat: The main command for displaying network-related information.

-t: This option specifies that only TCP connections should be displayed.

-u: This option specifies that only UDP connections should be displayed.

-l: This option stands for "listening" and instructs netstat to display only listening sockets (ports that are actively waiting for incoming connections).

-n: This option specifies that the numeric form of addresses should be used (e.g., display port numbers as numbers instead of resolving them to service names).

-p: This option displays the process or program name associated with each network connection. It shows the PID (process ID) and the name of the program or service using the specified connection.
   	
### Change Directory:
```
[sumit_chaubey@sumit ocp]$ cd ..
```
```
[sumit_chaubey@sumit ocp]$ chmod +x install-config.yaml
```
chmod: This is the command used to change file permissions.

+x: This is the option specifying the permission to be added. In this case, it adds the execute permission.

install-config.yaml: This is the name of the file for which you are modifying permissions. In this context, it's the OpenShift installation configuration file.
```
[sumit_chaubey@sumit ocp]$ ll
```
Output:

total 2333824
-rwxrwxr-x. 1 sumit_chaubey sumit_chaubey   	3831 Jan 12 17:09 install-config.yaml
-rwxr-xr-x. 2 sumit_chaubey sumit_chaubey  137625592 Dec 11 18:55 kubectl
-rwxr-xr-x. 2 sumit_chaubey sumit_chaubey  137625592 Dec 11 18:55 oc
drwxrwxr-x. 3 sumit_chaubey sumit_chaubey    	167 Jan 12 17:07 ocp
-rw-------. 1 sumit_chaubey sumit_chaubey   	2610 Jan 12 14:37 ocp4
-rw-r--r--. 1 sumit_chaubey sumit_chaubey    	581 Jan 12 14:37 ocp4.pub
-rw-rw-r--. 1 sumit_chaubey sumit_chaubey   58016041 Jan 11 22:13 oc.tar.gz
-rwxr-xr-x. 1 sumit_chaubey sumit_chaubey  498096312 Dec 13 17:52 openshift-install
-rw-rw-r--. 1 sumit_chaubey sumit_chaubey  352587098 Jan 11 22:41 openshift-install-linux.tar.gz
-rw-r--r--. 1 sumit_chaubey sumit_chaubey    	706 Dec 13 17:52 README.md
-rw-rw-r--. 1 sumit_chaubey sumit_chaubey 1205862400 Jan 12 10:30 rhcos-live.iso

## Run this command:
```[sumit_chaubey@sumit ocp]$ ./openshift-install --dir=ocp create single-node-ignition-config
```
Output: 

INFO Single-Node-Ignition-Config created in: ocp and ocp/auth

./openshift-install: Executes the OpenShift installer binary.

--dir=ocp: Specifies the directory (ocp) where the installation assets and configuration files are stored. This directory is often referred to as the installation directory.

create single-node-ignition-config: This is a command to create an ignition configuration file for a single-node OpenShift cluster. Ignition files are used during the initial bootstrap process to configure the machine and install the necessary components.


## Embed the ignition data into the RHCOS ISO by run this command:

```
[sumit_chaubey@sumit ocp]$ ali.as coreos-installer='podman run --privileged --pull always --rm -v /dev:/dev -v /run/udev:/run/udev -v $PWD:/data -w /data quay.io/coreos/coreos-installer:release'
```

```
[sumit_chaubey@sumit ocp]$ coreos-installer iso ignition embed -fi ocp/bootstrap-in-place-for-live-iso.ign rhcos-live.iso
```
Output:

Trying to pull quay.io/coreos/coreos-installer:release...
Getting image source signatures
Copying blob 3b503ecc5eec done  
Copying blob 3efd6e844293 done  
Copying blob 7f1384716a3e done  
Copying config d6aa57e0dc done 
  

 # Then create the vm those image is download for node:                                                                      	 
	

### Use this command in your bastion node
        ```
        ./openshift-install --dir=ocp wait-for install-complete
        ```
 ### You have to ssh vm 
 ```
        ssh -i ocokey  username@ip 
```
### Then check the log 
          journalctl  -x -f

 ### Wait 05 to 10 min while installation is complete



### While installation is completed you check your node  
```
      oc get node
```
![Alt text](<copy image 1 openshift.png>)
## Then check the Cluster Operator they take a time to configure operator

```
 oc get co
```


![Alt text](<openshift image ss-1.png>)




