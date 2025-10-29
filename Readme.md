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
Commands used :

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
I will continue to add more questions good luck.
