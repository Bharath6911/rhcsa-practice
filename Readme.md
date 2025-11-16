# RHCSA Practice – Day 1 (2025-10-29)

I started studying Red Hat
This is my practice log and proof of work. 

---

## QUESTION 1

**Configure your Host Name, IP Address, Gateway and DNS.**
Host name: `station.domain40.example.com`
IP Address: `172.24.40.40/24`
Gateway: `172.24.40.1`
DNS: `172.24.40.1`

### Answer:

I SSH’d through PuTTY to my node1 Red Hat Linux system. I used VMware Workstation Pro with Red Hat 10 (latest version from Developers Red Hat learning page).
Commands used:

```
ls /etc/NetworkManager/system-connections/
sudo nmcli con mod ens160 ipv4.addresses 172.24.40.40/24
sudo nmcli con mod ens160 ipv4.gateway 172.24.40.1
sudo nmcli con mod ens160 ipv4.dns 172.24.40.1
sudo nmcli con mod ens160 ipv4.method manual
sudo nmcli con mod ens160 connection.autoconnect yes
sudo hostnamectl set-hostname station.domain40.example.com
hostnamectl
sudo nmcli con reload ens160
sudo nmcli con up ens160
sudo nmcli con down ens160  # if needed
```

Verification commands:

```
ip addr show ens160
cat /etc/resolv.conf
hostname
nmcli con show ens160 | grep ipv4
```

Expected results:

* IP: 172.24.40.40/24
* Gateway: 172.24.40.1
* DNS: 172.24.40.1
* Hostname: station.domain40.example.com

---

## QUESTION 2

**Add 3 users: harry, natasha, tom.**
Requirements:

* The Additional group of the two users (harry, natasha) is the `admin` group.
* The user tom’s login shell should be non-interactive.

### Answer:

Commands used:

```
grep admin /etc/group  # check if admin group exists
sudo groupadd admin     # create the admin group if required
sudo useradd -G admin harry
sudo useradd -G admin natasha
sudo useradd -s /sbin/nologin tom
```

Explanation:

* `-G admin` adds the user to the admin group as a secondary group.
* `-s /sbin/nologin` makes tom non-interactive (he can’t log in).

Verification:

```
id harry; id natasha
grep tom /etc/passwd
cat /etc/passwd
```

---

## QUESTION 3

**Create a catalog under `/home` named `admins`.**

* Its respective group is requested to be the `admin` group.
* The group users could read and write, while other users are not allowed to access it.
* The files created by users from the same group should also be the `admin` group.

### Answer:

Commands used:

```
cd /home
mkdir admins
chown :admin admins
chmod 770 admins
chmod g+s admins
ls -ld admins
```

Explanation:

* `chown :admin admins` sets group ownership to the admin group.
* `chmod 770 admins` gives read/write/execute to owner and group, no access to others.
* `chmod g+s admins` sets the setgid bit so new files inherit the admin group.

---

## QUESTION 4

**Configure a task: plan to run `echo hello` command at 14:23 every day.**

### Answer:

Commands used:

```
which echo         # typically returns /bin/echo
crontab -l         # see existing cron jobs
crontab -e         # edit the cron table
```

Entry added:

```
23 14 * * * /bin/echo hello
```

Explanation:

* `23` = minute
* `14` = hour (2 PM)
* `* * *` = every day, every month, every weekday
  Verification:

```
crontab -l
```

Optional enhanced entry for logging:

```
23 14 * * * /bin/echo hello >> /home/cronlog.txt
```

---

## QUESTION 5

**Find the files owned by harry, and copy them to directory `/opt/dir`**

### Answer:

Commands used:

```
cd /opt
mkdir dir
find / -user harry -exec cp -rfp {} /opt/dir/ \;
```

Explanation of the command breakdown:

* `find / -user harry`: search the entire system for files owned by user `harry`.
* `-exec cp -rfp {} /opt/dir/ \;`: for each file found, copy it to `/opt/dir/` preserving permissions, ownership, timestamps.

---

That’s Day 1 of my RHCSA practice.
I will continue to add more questions.
Thank you

---

 ## Today Date 2025-11-05 

 ## QUESTION 6

**Find the rows that contain abcde from file /etc/testfile, and write it to the file/tmp/testfile, and the sequence is requested as the same as ` /etc/testfile`**

### Answer:

Commands used:

```
# cat /etc/testfile | while read line;
do
echo $line | grep abcde | tee -a /tmp/testfile
done
OR
grep `abcde' /etc/testfile > /tmp/testfile
```

Explanation of the command breakdown:

* cat /etc/testfile | while read line; do echo $line | grep abcde | tee -a /tmp/testfile done : cat command reads the content line by line the processes each line from the file in a loop and then check the line contains `abcde` then it appends the matching line to the file `/tmp/testfile` while also printing it to the screen.

* `grep "abcde" /etc/testfile > /tmp/testfile` redirects the output directly into that testfile overwriting anything existing content.

---

 ## QUESTION 7

**Create a 2G swap partition which take effect automatically at boot-start, and it should not affect the original swap partition.**

### Answer:

Commands used:

```
# fdisk /dev/sda
p
(check Partition table)
n
(create new partition: press e to create extended partition, press p to create the main partition, and the
extended partition is further divided into logical partitions) Enter
+2G
t
l
W
partx -a /dev/sda
partprobe
mkswap /dev/sda8
Copy UUID
swapon -a
vim /etc/fstab
UUID=XXXXX swap swap defaults 0 0
(swapon -s)

```

Explanation of the command breakdown:

* got confused i need to learn this part a bit more

---

## QUESTION 8

**Create a user named alex, and the user id should be 1234, and the password should be alex111.**

### Answer:

Commands used:

```
# useradd -u 1234 alex
# passwd alex
alex111
alex111
OR
echo alex111|passwd -stdin alex

```
Explanation of the command breakdown:

* `useradd -u 1234 alex` : create's a user with -u is giving a UID to the user named alex.

* `passwd alex` : using this we can give a new password like alex111

---

--- 
 
## Day (2025-11-10) 
 
### What I did today 
 
* Started the **Udemy course**: [Prepare RHCSA with Practice Course (Unofficial)](https://www.udemy.com/course/prepare-rhcsa-with-practice-course-unoffical) 
  Took the 90-day free trial of redhat developer subcription. I’ll see how much it helps and progress here. 
 
--- 
 
### Networking Lab Setup 
 
Created a two-node mini-lab in **VMware Workstation Pro**: 
 
* RHEL 10 → `redhat1@station` 
* CentOS → `bharath@localhost` 
 
Used both **nmcli** and **nmtui** for configuration practice. 
Both nodes can now ping and SSH each other. 
 
#### On RHEL (station) 
 
``` 
sudo hostnamectl set-hostname station.domain40.example.com 
sudo nmcli con mod ens160 ipv4.addresses 172.24.40.40/24 
sudo nmcli con mod ens160 ipv4.gateway 172.24.40.1 
sudo nmcli con mod ens160 ipv4.dns 172.24.40.1 
sudo nmcli con mod ens160 ipv4.method manual 
sudo nmcli con up ens160 
sudo nmcli con delete hostonly || true 
sudo nmcli con add type ethernet ifname ens224 con-name hostonly ipv4.addresses 192.168.159.254/24 ipv4.method manual 
sudo nmcli con mod hostonly ipv4.never-default yes 
sudo nmcli con mod hostonly connection.autoconnect yes 
sudo nmcli con up hostonly 
echo "192.168.159.254 station.domain40.example.com station" | sudo tee -a /etc/hosts 
echo "192.168.159.10 client.example.com client" | sudo tee -a /etc/hosts 
``` 
 
#### On CentOS (client) 
 
``` 
sudo nmcli con delete hostonly || true 
sudo nmcli con add type ethernet ifname ens224 con-name hostonly ipv4.addresses 192.168.159.10/24 ipv4.method manual 
sudo nmcli con mod hostonly ipv4.never-default yes 
sudo nmcli con mod hostonly connection.autoconnect yes 
sudo nmcli con up hostonly 
echo "192.168.159.254 station.domain40.example.com station" | sudo tee -a /etc/hosts 
echo "192.168.159.10 client.example.com client" | sudo tee -a /etc/hosts 
``` 
 
After creating a **Host-only network** in VMware, both machines communicate via ping and SSH. 
A big step [Em Dash U+2014] built a working internal training lab. 
 
--- 
 
### SELinux Notes 
 
Learned SELinux modes: 
 
* `Enforcing` → blocks violations 
* `Permissive` → logs only 
* `Disabled` → off 
 
Commands: 
 
``` 
getenforce 
setenforce 0   # Permissive 
setenforce 1   # Enforcing 
sestatus 
``` 
 
Permanent config: 
 
``` 
sudo vim /etc/selinux/config 
``` 
 
--- 
 
### Resetting Root Password (RHEL 10) 
 
Steps followed from the course: 
 
1. Show GRUB every boot 
 
   ``` 
   sudo vim /etc/default/grub 
   GRUB_TIMEOUT_STYLE=menu 
   GRUB_TIMEOUT=10 
   GRUB_ENABLE_BLSCFG=false 
   sudo grub2-mkconfig -o /boot/grub2/grub.cfg 
   ``` 
2. Reboot → press `e` in GRUB. 
3. Replace `rhgb quiet` with: 
 
   ``` 
   init=/bin/bash 
   ``` 
4. Boot (Ctrl+X) → run: 
 
   ``` 
   mount -o remount,rw / 
   passwd 
   touch /.autorelabel 
   exec /sbin/init 
   ``` 
5. Log in with the new password. 
 
The change didn’t persist; contacted the instructor and will re-try on another VM. 
 
--- 
 
### Extra 
 
Downloaded a few networking PDFs to start reading about switches, routers, MAC addresses, and frames. 
Still working through those. 
 
--- 

## Day (2025-11-11) 

### What I did today 

- Fixed a NAT/internet issue in VMware for my RHEL 10 VM and prepared an offline local repository so package installs work without internet access.

- The RHEL VM couldn't reach the internet (VMware NAT misconfiguration). I needed to install packages on an offline machine using the RHEL/CentOS installation ISO.

### Solution 

- Attach or mount the RHEL/CentOS install ISO on the VM, create a local repository that points to the mounted media, disable online repos, then use the local repo for package installation. Optionally copy the ISO contents to a local folder (e.g. /repo) so the repository remains available if the media is removed.

### Commands / examples

Check existing repo files:

```
ls /etc/yum.repos.d
```

Create or edit a local repo file (example: `/etc/yum.repos.d/local.repo`):

```
[BaseOS]
name=BaseOS
baseurl=file:///run/media/<user>/RHEL-*/BaseOS
enabled=1
gpgcheck=0

[AppStream]
name=AppStream
baseurl=file:///run/media/<user>/RHEL-*/AppStream
enabled=1
gpgcheck=0
```

Disable any online repos (example):

```
sudo sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/centos.repo
sudo sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/centos-addons.repo
sudo yum clean all
sudo yum repolist
```

You should see only the local repos in the list, for example:

```
repo id    repo name
BaseOS     BaseOS
AppStream  AppStream

```

Test installing a package from the local repo:

```
sudo yum install httpd -y
```

Optional — make a persistent local copy of the install media (recommended so the repo survives media unmount):

```
sudo mkdir -p /repo/BaseOS /repo/AppStream
sudo cp -irv /run/media/*/BaseOS/*/repo/BaseOS/* /repo/BaseOS/
sudo cp -irv /run/media/*/AppStream/*/repo/AppStream/* /repo/AppStream/
```

Notes

- `cp -irv`: `-i` interactive (asks before overwrite), `-r` recursive, `-v` verbose.
- If you're unsure about yum/dnf usage, read the man page: `man yum` or `man dnf`.
- Replace `<user>` or wildcard paths with the actual mount point shown on your system (for example `/run/media/bharath/...`).

---


### Today 12-nov-2025 

---

searched for some python book to know some knowledge in python for an free online resource where we can study online > https://automatetheboringstuff.com/
I continued with the udemy course in details.
In the sections managing software aside from DNF online and local repos Got to know flatpaks and how to install from a remote repos how to add these in different areas like:
system wide or user wide applications we can get to know about the commands using the --help command `> flatpak --help `

```

>flatpak --help
>flatpak list
>flatpak remotes
>flatpak remote-ls --app rhel_or_fedora_or_flatpak
>flatpak remote-add --if-not-exists fedora oci+https://registry.fedoraproject.org
>flattpak remotes --system
>flatpak remote-add --if-not-exists --user flathub https://dl.flathub.org/repo/flathub.flatpakrepo
>flatpak remotes --user
>flatpak list
>flatpak install fedora puTTY

```

After that i wanted to practice again how to add hostname and adding forward backward dns  in the hosts file and again add another os to the network of RHEL 10 the main system and the centos which is connected to RHEL 

Used kali linux because wanted to practice python and networking thinks kali had better understanding things so yeah.check these : 

```
>nmcli con show 
>ip link sh
>nmcli con mod ens225 ipv4.addresses 192.168.159.11/24
>nmcli con mod ens225 ipv4.gateway 192.168.159.10
>nmcli con mod ens225 ipv4.dns 192.168.159.10
>nmcli con mod ens225 ipv4.method manual
>nmcli con mod ens225 ipv4.never-default yes
>hostnamectl set-hostname kali.domain40.example.com 
>hostname 
>nmcli con show ens225
>ping 192.168.159.10

```


---

### Today 13-nov-2025

---

**Started with the user management and went deep inside what variable does in each command like for example question :**

**Create a user named riya with uid 6001 and assign a home directory with /riya/private and her password mush be changed at the first login with a spam period of 30 days.**

```
>useradd -u 6001 -d /riya/private riya
```

Here we have created a user with name riya and given her a UID numbers and a home directory of her own which can be accessed by her you can see the permission levels using the command 

```
>ls -ld /riya/private
```

I have created this user as per the course instructor question from the section and not only that we need to create a password renewal every span of 30 days so we used 

```
>chage riya 
```

This will allow us to give passwd age, account expiration such settings 

```
root@station:~# chage riya
Changing the aging information for riya
Enter the new value, or press ENTER for the default

	Minimum Password Age [0]: 
	Maximum Password Age [99999]: 30
	Last Password Change (YYYY-MM-DD) [2025-11-13]: 0
	Password Expiration Warning [7]: 
	Password Inactive [1]: 
	Account Expiration Date (YYYY-MM-DD) [-1]: 
root@station:~# chage -l riya
Last password change					: password must be changed
Password expires					: password must be changed
Password inactive					: password must be changed
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 30
Number of days of warning before password expires	: 7
root@station:~# passwd riya
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: password updated successfully
root@station:~# chage -l riya
Last password change					: Nov 13, 2025
Password expires					: Dec 13, 2025
Password inactive					: Dec 14, 2025
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 30
Number of days of warning before password expires	: 7
root@station:~# more /etc/shadow | grep riya
riya:$y$j9T$72oD2HKSGYlnVPTAumHzR1$ul8sTSATaqVMoU6HuAd9I8clUkTAnsVpfq/tQYUK1vA:20405:0:30:7:1::
root@station:~# passwd -S riya
riya P 2025-11-13 0 30 7 1

```
---

### Practice question 

**Create a group named redhat it should have GID as 5555 and assign a user named lisa as supplementary or secondary group.**  

```
root@station:~# groupadd -g 5555 redhat
root@station:~# usermod -aG redhat lisa
root@station:~# more /etc/group | grep redhat
wheel:x:10:redhat1
redhat1:x:1000:
redhat:x:5555:lisa
root@station:~# id lisa
uid=6000(lisa) gid=6000(lisa) groups=6000(lisa),5555(redhat)

```

### Practice question 

**Create user with username vivek and set password as access but Account should expire on 31st Dec current year (2025) and Password should expire every 70 days and Set password expiry warning to 4 days.**

```
root@station:/home# useradd vivek

root@station:/home# passwd vivek
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: password updated successfully
root@station:/home# chage vivek
Changing the aging information for vivek
Enter the new value, or press ENTER for the default

	Minimum Password Age [0]: 
	Maximum Password Age [99999]: 70
	Last Password Change (YYYY-MM-DD) [2025-11-13]: 
	Password Expiration Warning [7]: 4
	Password Inactive [1]: 
	Account Expiration Date (YYYY-MM-DD) [-1]: 2025-12-31
root@station:/home# chage -l vivek
Last password change					: Nov 12, 2025
Password expires					: Jan 21, 2026
Password inactive					: Jan 22, 2026
Account expires						: Dec 30, 2025
Minimum number of days between password change		: 0
Maximum number of days between password change		: 70
Number of days of warning before password expires	: 4
root@station:/home# more /etc/shadow |grep vivek
vivek:$y$j9T$HRZ949XhKaxFbqvnCkq76.$f.PCh4k3csFrcRa/h1u2jySOP2uxOCKTQT9.R62ucX/:20404:0:70:4:1:20452:
root@station:/home# passwd -S vivek
vivek P 2025-11-12 0 70 4 1
root@station:/home# 

```

**In my previous session practices i created a user harry so i wanted to delete the user used the command wrong which was**

```

>userdel harry # But this will not remove him from the other folders you can check by these commands 
>getent passwd harry
>getent shadow harry
>getent group harry
>pkill -u harry # you well see their are files of him to remove completely use 
>userdel -r harry  # -r means recursively delete everything 

```

---

### Today 15-nov-2025

**Today going to do some questions**

## QUESTION 9

**Install a FTP server, and request to anonymous download from /var/ftp/pub catalog. (it needs you to configure yum direct to the already existing file server. )**

Explanation :
```
# cd /etc/yum.repos.d
# vim local.repo
[local]
name=local.repo
baseurl=file:///mnt
enabled=1
# yum makecache
# yum install -y vsftpd
# service vsftpd restart
# chkconfig vsftpd on
# chkconfig --list vsftpd
# vim /etc/vsftpd/vsftpd.conf
anonymous_enable=YES
```

## QUESTION 10

**Configure a HTTP server, which can be accessed through http://station.domain40.example.com and Please download the released page from http://ip/dir/example.html.**

Explanation:
```
# yum install -y httpd
# systemctl start httpd
# cd /var/www/html
# wget http://ip/dir/example.html # its provided in the exam 
# cp example.com index.html
# vim /etc/httpd/conf/httpd.conf
NameVirtualHost 192.168.0.254:80
<VirtualHost 192.168.0.254:80>
DocumentRoot /var/www/html/
ServerName station.domain40.example.com
</VirtualHost>
# curl station.domain40.example.com
```
## Questions from course 

**Create the directory /dir and set the group and user ownership to redhat and lisa respectively and give readonly access to group redhat and rwx access to lisa.**

Explanation and answer:

```
# sudo -i 
# cd home
# mkdir /dir
# chown lisa:redhat /dir
# ls -ld /dir
```
**Configure System (system.example.com) as IPA client to use LDAP services configured on IPA Server with Free IPA Server solution and Use user admin and password as password to enroll System and ldap users should be able to login on System.**

Explanation and answer:

```
dnf install ipa-client  ---  To install package for ipa client
ipa-client-install      ---  Configuring System as IPA Client
su - Idap               ---  Verify Idap user login
```

---

### Today 16-Nov-2025

I learned about autofs 

autofs -Mounting file systems on demand 

autofs service is used to mount the filesystems on demand which means filesystem gets mounted automatically when it is accessed and unmounted when it is not used.

when we mount filesystem through fstab file, it remains mounted no matter if filesystem is accessed or not and system has to allocate dedicated resources to keep the mounted system in place. So, this is not good practice specially for infrequently accessed filesystems because it can cause performance issues when there are multiple such filesystems.

autofs can mount Nfs, smd, cifs and local filesystems. We will use autofs to mount LDAP user's home directories which are exported as NFS filesystems on ipa server

autofs conf files:
1. the master map file(/etc/auto.master)
2. Map-file

## Questions from course 

**Configure system.example.com to automount home directory of LDAP user Idap when logged in and Home directory of LDAP user is /home/ldapuser/Idap and Home directory is exported by ipaserver.example.com as NFS export and LDAP user should get his home directory when logged in.**

Explanation and answer:

```
rpm -q autofs                   --- to know if the autofs is instaled or not
#dnf install autofs              --- To install packages required for autofs
#systemctl start autofs          --- To start autofs service 
#systemctl enable autofs          --- configuring to start service at the reboot
#more /etc/autofs.conf            --- to see if everything is configured correctly 
#dnf group list --hidden          --- to list the hidden group list of packages and look for 'Network file system client' then
#dnf groupinstall 'Network file system client'     --- to install it 
# showmount -e ipaserver.example.com
/idap/home      *
/home/idapuser  *
/nfsshare       *      ----to get to know the list of exports pick one you need 

#vim /etc/auto.master 
/home/idapuser    /etc/auto.idap    -rw  --- to define master automounter map
#vim /etc/auto.idap 
idap     ipaserver.example.com:home/idapuser/idap       --- creating a new file auto.idap add this line 'idap     ipaserver.example.com:home/idapuser/idap '
#systemctl restart autofs         --- restart autofs service 
#su - idap                       ---  switch to idap
#pwd                            ---  current directory should be idap user's home directory 
```

**Configure system.example.com to automount home directories of LDAP users Idap1 and Idap2 and Home directories of LDAP users Idap1 and Idap2 are /Idap/home/Idap1 and /Idap/home/Idap2 respectively and Home directories are shared by ipaserver.example.com through NFS export and LDAP user should get his home directory when logged in.**

Explanation and answer:

```
# dnf install autofs             ---  installing autofs 
# systemctl start autofs         ---  starting autofs service 
# systemctl enable autofs        ---  configuring autofs to start at reboot 
# vim /etc/auto.master           ---  to define base location for home directory 
/idap/home       /etc/auto.idap12     
# vim /etc/auto.idap12
*     ipaserver.example.com:/idap/home/&
# systemctl restart autofs         ---  restarting autofs service 
# su - idap1                     ---  switch user to ldap1
# pwd                            ---  current directory should be home directory of /ldap/home/ldap1
```


