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