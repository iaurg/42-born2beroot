# Born2beroot

This document is a System Administration related exercise. Let's create our first virtual machine.

- [] The use of VirtualBox is mandatory.
- [] You only have to turn in a signature.txt file at the root of your repository. You
must paste in it the signature of your machine’s virtual disk. Go to Submission and
peer-evaluation for more information
- [] It is therefore forbidden to install X.org or any other equivalent graphics server

*Debian*:

Debian was one of the first Linux distributions, having been available since 1993. Currently on version 10, Debian had a 17% market share of Linux web servers in April 2020.

Hostname: Is a single name that identifies you into a network

*CentOS*:
CentOS is based on the open source code of RHEL (Red Hat Enterprise Linux).

Only truly stable versions of CentOS are released, which is partly why they sometimes take so long – some versions require more testing than others to get to a stable state. The years between releases ensure plenty of time for thorough testing, and any security fixes are backported into older versions of CentOS for any vulnerabilities discovered when building the new version. This makes CentOS another good choice for enterprises.

There are existing and active community forums, but these are not officially run and exist separately. It’s therefore more difficult to find online tutorials and support.

The lack of community support and more difficult installation makes CentOS tricky to pick up for Linux beginners. Instead, because it’s based on the enterprise-grade RHEL, CentOS is usually preferred by businesses and web agencies who are already familiar with Linux distros.

*Package managers*

CentOS uses the RPM package format and YUM/DNF as the package manager.

Debian uses the DEB package format and dpkg/APT as the package manager.

**LVM**

Logical Volume Manager, it's a way to manage storage on your OS.

Allow the creation of "groups" of disks or partitions that can be assembled into a single (or multiple) filesystems

LVM works with 3 layers.

PV (Physical Volume) - Just tag physical devices
VG (Volume Group) - A pool of physical volume so that you can divide and work with
LV (Logical Volume) - Separate your VG in many ways you want  
File System - The "folders" you use

Advantages
- Flexibility with volumes, allow you to resize volumes
- Snapshots like backups in "point in time" allocated in another space, allow you to go back in history of your HD

1 - First I create the boot volume without lvm so that we can access boot

2 - Then I've created a encrypted volume so that I can acess and partionate this

## Step by Step

First configure your LVM following the steps presented on this video (only if you want the bonus):
https://www.youtube.com/watch?v=GEl2S5MI-WU

To check your volumes use `lsblk`

If you need to rename a logical volume:

`lvrename LVMGroup var--log var-log`

**Installing, checking and configuring sudo**
Access as sudo `su -`

Install sudo package

Learn what sudo does: [sudo introduction](https://www.sudo.ws/intro.html)

```sh
apt-get update -y
apt-get upgrade -y
apt install sudo
```

Add your user to sudo

```sh
usermod -aG sudo userintra42
```

Check sudo users
`getent group sudo`

Add your user into `sudo visudo`

To change user use `su username42`

**SSH**

Debian docs: https://wiki.debian.org/SSH#Installation_of_the_client

See SSH service running in port

`sudo service ssh status`

Change SSH default port

`sudo nano /etc/ssh/sshd_config`

Remove comment from line with "#Port 22" and replace with "Port 4242", after restart the ssh server

`sudo service ssh restart`

To connect into ssh confirm that you're using a host terminal and use:
`ssh user42@127.0.0.1 -p 4242`

**UFW**

Is a simple firewall tool to allow some ports and services on linux

Enable UFW

`sudo ufw enable`

`sudo ufw status`

**Strong password policy**

https://ostechnix.com/how-to-set-password-policies-in-linux/

edit file /etc/pam.d/common-password

*minlen=10* - Minimum lenght of password
*ucredit=-1* - At least one upper-case letters in the password
*dcredit=-1* - At least one digite in the password
*lcredit=-1* - At least one lower case in the password
*retry=3* - allow only 3 retries
*maxrepeat=3* - max repeat same letter
*usercheck=0* - not allow username in password
*difok=7* - number of characters that must be different from old password
*enforce_for_root* - same policy for root password

Password expiration file:
`sudo nano /etc/login.defs`

```
PASS_MAX_DAYS 30
PASS_MIN_DAYS 2
PASS_WARN_AGE 7
```

Edit /etc/sudoers and add rule passwd_retries=3 to allow only three attempts

Log sudo outputs

Require tty: (Why use tty? If some non-root code is exploited (a PHP script, for example), the requiretty option means that the exploit code won't be able to directly upgrade its privileges by running sudo.)

**Wordpress and DB**

_it's not secure to show this here, but it's only on VM..._

db:wordpress

db_user:italo

db_pass:123123

wordpress user:italo

wordpress pass:&6VAfRM(KJfCKyoT!H

## Defense

Check sha1 vdi
`certutil -hashfile ./born2beroot_copy.vdi sha1`
SHA1 hash of ./born2beroot_copy.vdi:
c81ba7a127ad1bfec30b1df80d05017dbec95d88

### Project Overview
**How a virtual machine works:**
It's like matrix, a computer inside another computer. Virtual machines create "fake" hardware above real hardware and use it as a virtual machine.
Host Machine -> Guest OS (Virtual Machine)
Isolated computer

**Choice operating System**
I've choosed Debian because has a lot of material online, community and is a SO in development easy to use and friendly. Not company involved. CentOS is beign to be discontinued and has a open source project called Rocky Linux as yout substitute maintenade by CentOS founder.

**Basic differences CentOS - Debian**

CentOS
- Released and supported by a company (Red Hat)
- Slower updates
- Very stable
- Red Hat based
- Recommended for servers
- Usually more secure, with default configuration
- Less architeture support
- Indicated for critical servers
- CentOS uses the RPM package format and YUM/DNF as the package manager.

Debian
- Released and supported by the community
- Ubuntu based
- A lot of packages
- Version launched faster, it means less stable
- Easy to update version
- Debian uses the DEB package format and dpkg/APT as the package manager.

**Purpose of virtual machine**
Create a isolated enviroment

**apt get vs aptitude**
Both are packages managers

apt-get (and apt-cache) is the classic interface, apt is the newer UI with the same functionality. aptitude is an old optional program that has a different resolver strategy, an optional full-screen UI and some other features. While some people use it for the additional features, others seem to think its resolver is better.

aptitude:
- manage package in a way that all packages are some kind of related. If you uninstalled the package you want usign aptitude he removes all related packages if necessary.
- has command line interfaces
- high level

apt get:
- uses command line only
- only remove the package you want to uninstall, but you can remove like aptitude using autoremove flag
- low level
- is default from Debian, so community use it more

**App Armor is**
Check app armor running
`sudo aa-status`

Restrict actions on SO, to allow only authorized users/proccess to access some paths.

### Simple Setup 

See services running
`sudo service ufw status`
`sudo ufw status`
`sudo service ssh status`

Check OS version
`cat /etc/os-release`

### User

Check groups
`groups itaureli42`

Create user
`sudo adduser new_username`

Explain password rules:
`sudo nano /etc/pam.d/common-password`

Create group
`sudo groupadd evaluating`

Assign user to group
`sudo usermod -aG evaluating your_new_username`

Check user group
`groups your_new_username`

## Hostname and partitions
Show users
`less /etc/passwd`

Change hostname
`sudo hostnamectl set-hostname username`
`sudo nano /etc/hosts`
`sudo reboot`

View partitions
`lsblk`

## Sudo

Check sudo installed
`sudo -V`

Add new user into sudo group
`usermod -aG sudo your_username`

Check group
`getent group sudo`

Sudo limits
`sudo visudo`

By example on this machine only sudo can reboot the server, because I don't want any user with this power, so command `reboot` not work without sudo privilegies.

Check log
`sudo nano /var/log/sudo/sudo.log`

Show log working
```sh
su -
cd /var/log/sudo
rm sudo.log
sudo echo "hello 42"
less sudo.log
```

## UFW

Check ufw is installed
`ufw version`

Check ufw is working properly and list active porst
`ufw status numbered`

**UFW is**
A firewall manager that allow or disallow access to ports, it's a netfilter. This type of filter creates a barrier between a public and private connection.

The rules of the firewall can be based on:
- ports
- ip addresses
- domain names
- protocols
- programs

Add new rule to 8080
`ufw allow 8080`

Delete rule
`ufw delete ROWNUMBER`

## SSH
Check port running ssh
`sudo service ssh status`

**SSH is**
SSH basicly is a encrypted content that can be sent into the internet without risk, because only the servers with keys can access and read it. This is a secure way to transfer data through network. SSH encrypt the payload of a package before send it to another computer.

Check it's working
- Open power shell
- `ssh newuser42@127.0.0.1 -p 4242`
- Connected

## Script monitoring

Monitoring shell
`sudo nano /usr/local/bin/`

**cron is**
Is a job manager to allow you to run some task into the future or from time-to-time, schedule tasks and run it.

Edit cron
`crontab -u root -e`

Stop cron service
`sudo service cron stop`
`sudo /etc/init.d/cron stop`

## Bonus

Check services running on machine
`systemctl list-units --type=service`

Show php
`php -v`

Show mariadb
`sudo mariadb -v`

Show lighttpd
`sudo lighttpd -v`