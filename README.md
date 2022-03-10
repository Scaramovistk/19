# Born2beRoot

</br>


**This tutorial will explain the project in broad outline, it is not intended to be exhaustive: do your research and always understand what you are doing. Good luck !**


This project aims to introduce you to the wonderful world of virtualization.

You are going to create your first machine by respecting precise instructions and by
using VirtualBox (or UTM if VirtualBox is not running on your machine).

Thus, following this project, you will be able to install your own operating system
implementing strict rules.

For the manipulations requested during the correction, see the end of the tutorial.
</br>

___Settin up your VM___
--------------------------------

*More information on system files under Linux on[How To Geek](https://www.howtogeek.com/howto/33552/htg-explains-which-linux-file-system-should-you-choose/) and setting up partitions (little bit old) in [Developpez.com](https://linux.developpez.com/formation_debian/partitionner.html)*
</br>

* Dowload the stable version [Debian](https://www.debian.org/)

It comes in the form of a CD image: an .iso that we will later mount in our virtual machine.

**If you want to do the bonus** you can follow this installation here, made by [hanshazairi](https://github.sre.pub/hanshazairi/42-born2beroot/blob/main/README.md) : [here](https://www.youtube.com/watch?v=2w-2MX5QrQw)

</br>
  
* Launch the VirtualBox virtual machine (VM), already installed on 42 computers normally:

Button New (Ctrl + N) :

Name of your VM - 
Folder : in a place where you can use 8GB.

<blockquote>If you are at 19 you will saturate your session: you can work temporarily in the goinfree be aware that it is often emptied, so remember to export your VM so as not to have any unpleasant surprises</blockquote>

Type : Linux - 
Version : Debian 64bits 	

Amount of RAM allocated to the virtual machine: 1 or 2 GB are sufficient

Create a virtual hard disk now (recommended size: 8GB) - VDI (VirtualBox Disk Image) - Dynamically allocated - Check its location and size: 8GB - Creat

Go to the settings of your virtual machine

_Onglet STORAGE_ - Controller: IDE: Click on the small blue cd below, in Attributes click on the blue disk next to Optical Drive and mount the .iso of the Debian version

_Onglet NETWORK_ -Enable Network Adapter : Bridged Adapter/Access by bridge __OU__ NAT (in 42)

_OK_

</br>

* Lounch your VM 

</br>

__Installing the Debian__
------------------------------------

*More info in [Debian-handbook](https://debian-handbook.info/browse/fr-FR/stable/sect.installation-steps.html) et 
[Doc.Ubuntu](https://doc.ubuntu-fr.org/lvm) and about the ssh connection in[OpenClassRoom](https://openclassrooms.com/fr/courses/43538-reprenez-le-controle-a-laide-de-linux/41773-la-connexion-securisee-a-distance-avec-ssh)*

</br>

Check that the correct iso is used

<blockquote>Enlarge the window with the Video Memory tab - Virtual Screen: 300%

To move in the VM use TAB or the arrows, to display the password: space, confirm: enter</blockquote>

Installation - Language: as you wish - Country: Belgium (or your location) - Keyboard: according to yours (United States at 42)

Configure the network - Hostname/Machine name: yourlogin followed by 42, ex: wil42

Domain: _nothing_

Choose the password for the root partition & confirm it

New user full name: login - Identifier for the user counter: login - Password of the new user: whatever you want

Partition Disks: Assisted - use entire disk with encrypted LVM

Select the hard drive to partition (ends with VBOX HARDDISK)

Partition /home separately - YES

Choose a cool passphrase with numbers, letters and punctuation marks

Partition disks: 7.5 GB

<blockquote>Check that you have: LVM volume group: one logical volume home, another root, another swap

On your hard disk: a primary partition for booting and another logical partition, encrypted</blockquote>

Finish partitioning and apply changes - YES

The base system installs

Should another CD or DVD be scanned? - NO

config. the package management tool: Belgium

Debian Archive Mirror: deb.debian.org

HTTP Proxy/Proxy: *empty*

Statistical study: NO

Software selection: Check (space key) SSH server and usual system utilities, nothing else

Install GRUB - On the /dev/sda ...

Continue - VM is restarting

Enter your passphrase

Login: root - enter the correct password
</br></br>

__SUDO__
-----------------------------------------------

*More info [Wiki.Debian](https://wiki.debian.org/sudo)*

</br>

**Installation**

Install sudo: `apt install sudo`

Verify that sudo is installed: `dpkg -l | grep sudo

**Adding a user**

Add a user to the sudo group (this user will therefore be able to use the sudo command): `adduser <username> sudo`

Check users in sudo group: `getent group sudo`

Reboot the VM with `reboot` for the changes to take effect

Check who has sudo rights with `sudo -v` command

**Sudo Setup**


<blockquote>All files in the /etc/sudoers.d/ directory that do not end with ~ or contain a . are read and parsed when using the sudo command.

To modify the operation of the sudo command, the system administrator no longer modifies the /etc/sudoers file but places customization files in the /etc/sudoers.d directory.

Source: [Ubuntu-fr Wiki](https://doc.ubuntu-fr.org/sudoers)</blockquote>

Create a new file to configure sudo with `nano /etc/sudoers.d/<filename>`

Add the following commands:

To limit authentication attempts to 3 in the event of an incorrect password:

```
Defaults passwd_tries=3
```

To add a custom error message:

```
Defaults badpass_message="<custom-error-message>"
```

Save sudoer activity in a specific log file (remember to create this file afterwards):

```
Defaults log_host, log_year, logfile="/var/log/sudo/<filename>"
```

To log all sudo input/output to /var/log/sudo/:

```
Defaults log_input,log_output
Defaults iolog_dir="/var/log/sudo"
```
To force sudoers to not be able to act anywhere other than in console mode:

```
Defaults required
```
To restrict the paths used by sudo:

```
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```
</br></br>

__Configuration SSH & UFW__
------------------------------------

*More info on installing SSH server on Debian at [Devconnected](https://devconnected.com/how-to-install-and-enable-ssh-server-on-debian-10/) and
installing UFW and setting up the firewall on [Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on -ubuntu-20-04-en#:~:text=You%20can%20sp%C3%A9cify%20of%20beaches,ufw%20allow%206000%3A6007%2Ftcp)*

</br>

**Installation and SSH configuration**

* Launch a package update: `apt-get update`

* Check that the ssh server is installed: `apt-get install openssh-server`

Configure in nano so that the ssh server is only active on port 4242: `nano /etc/ssh/sshd_config`

Uncomment (-#) the line `#Port 22` and put: `Port 4242`

Uncomment (-#) the line `#PermitRootLogin prohibit-password` and put: `PermitRootLogin no`

Exit the file with Ctrl + X

<blockquote>Save (Write) your nano file otherwise your changes will be lost</blockquote>

Check SSH status with `service ssh status`

Know your IP in the VM: `ip a`

Note in a corner what is after the first inet, as well as the second ip address a little further


Go to your VM settings (on virtualBox) - Advanced - Port Forwarding

In the Host IP, put the first ip address, Host Port: 4242

In the Guest IP: the second ip address, Host port: 4242

Okay, okay

Restart the VM

</br>

##**Connect from your computer terminal to your VM via SSH**


Connect to your VM via port 4242 with `ssh <username>@<ip-address> -p 4242`

To exit the session: `logout`

</br>

**Firewall installation with UFW**

Install ufw: `apt-get install ufw`

Enable the firewall: `ufw enable`

Block all connections from outside: `ufw default deny incoming`

Allow server applications to have outgoing access: `ufw default allow outgoing`

Configure the firewall for incoming connections on port 4242: `ufw allow 4242`

Check that everything is in order: `ufw status numbered`

<blockquote>If one of the rules does not suit you: `ufw delete [rule_nbr]`</blockquote>

</br></br>

__Implementation of strong password policy__
-----------------------------------------------

*More info on user authentication with PAM at [Debian.org](https://www.debian.org/doc/manuals/securing-debian-manual/ch04s11.en.html) and [Systutorials. com](https://www.systutorials.com/docs/linux/man/5-pwquality.conf/)*

</br>

Install libpam-pwquality and cracklib-runtime (allows to manage the complexity of mdp):

```
apt-get -y install libpam-pwquality cracklib-runtime
```

Change the duration at which passwords can be changed in the /etc/login.defs file (sets the shadow password suite configuration for the system.):

```
nano /etc/login.defs
```

In the Password aging controls section, indicate the maximum number of days after which the password will expire:

`PASS_MAX_DAYS 30`

And the minimum number of days between two changes to the password:

`PASS_MIN_DAYS 2`

In the config file:

```
nano /etc/pam.d/common-password
```

On the line `password requisite pam_pwquality.so retry=3` add the following instructions:

`difok=7` Number of characters in the new password that are not present in the old one, by default difok=1

`minlen=10` Minimum size of new password. However, a bonus of one more character is added if one more different type of character is present in the password.

`dcredit=-1` If dcredit < 0, dcredit is the opposite of the minimum number of digits in the new password, example if dcredit = -5, it requires at least 5 digits in the password.

`ucredit=-1` If ucredit < 0, ucredit is the opposite of the minimum number of uppercase letters in the new password, example if ucredit = -4, at least 4 uppercase letters are required in the password

`maxrepeat=3` If maxrepeat=N, then a character cannot be present more than N times

`reject_username` Verifies that the username is not contained in the password, if so the password is refused

`enforce_for_root` To enforce these restrictions on root as well

In the end your line will look like:
```password        requisite                       pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root```

On the line below, after pam_unix.so add `remember=1` to keep in memory the last password used and to be able to compare it when creating a new password.


Create a new user:
--

Create a new user: `adduser <username>`

Check that it was created with `getent passwd <username>`

Check the validity time of passwords with `sudo chage -l <username>`

Create a new group:
--

Create a new group named `user42`

Add your new member to this group with `adduser <username> user42`

Check that the user has been added to the group user 42 with `getent group user42`
</br></br>

__AppArmor__
--

*More info on AppArmor: [Debian-Handbook](https://debian-handbook.info/browse/fr-FR/stable/sect.apparmor.html) and SELinux: [Debian-Handbook](https:/ /debian-handbook.info/browse/fr-FR/stable/sect.selinux.html)*

Normally installed by default, you can download it with `apt install apparmor`

Complete the installation with `apt install apparmor-utils`

Check that the apparmor module is working with `/usr/sbin/aa-status` or `apparmor_status`
</br>

__SCRIPT__
--

*More info on the shell on [Doc-Ubuntu](https://doc.ubuntu-fr.org/projets/ecole/scripting/initiation_au_shell) and the regex on [Regexr](https://regexr.com /), go check the man for uname, lscpu, awk ... you can also cat the different /proc files to see what interests you in their content*

Create a monitoring.sh file
```
nanomonitoring.sh
```
Example script for **a terminal in English**, if you are in French or another language some terms will change.
```
#!/bin/bash


MEMTOTAL=$(awk '/MemTotal/ {print $2}' /proc/meminfo) 

MEMAVAIL=$(awk '/MemAvail/ {print $2}' /proc/meminfo)

MEMUSED=$((MEMTOTAL - MEMAVAIL))

LVM=$(lsblk | awk '/root/ {print $6}')


wall << End_Of_Message
	
    #Architecture: `uname -a`

    #CPU physical: `lscpu | awk '/^CPU\(s\)/ {print $2}'`

    #vCPU : `grep -c 'processor' /proc/cpuinfo`

    `echo $MEMUSED $MEMTOTAL | awk '{printf "#Memory Usage: %d/%dMB (%.2f%%)\n", ($1/1024), ($2/1024), ($1/$2)*100}'`

    `df --total | tail -n 1 | awk '{printf "#Disk Usage: %d/%dGb (%d%%)\n", ($3/1024), ($2/1048576), $5}'`

    `awk '{print "#CPU load: "$1"%\n"}' /proc/loadavg*100`

    #Last boot: `who -b | awk '{print $3" "$4}'`

    #LVM use: `if [ "$LVM" = "lvm" ];
    then
      echo "yes"
    else
      echo "no"
    fi`
    
    `awk '$4=="01" {count++} END{printf "#Connexions TCP : %d ESTABLISHED\n", count}' /proc/net/tcp`

    #User log: `who | awk '{print $1}' | uniq | wc -l`

    `ip -br a show $(ip route show default | awk '{print $5}') | sed 's/\/[[:digit:]]\{1,3\}//g' | awk '{printf "#Network: IP %s (%s)\n", $3, $4}'`

    `grep -c 'COMMAND' /var/log/sudo/sudo.log | awk '{printf "#Sudo : %d cmd\n", $1}'`

End_Of_Message
```
</br></br>

Which will work like this:

MEMTOTAL: recover the total memory

MEMAVAIL: recover unused memory

MEMUSED: Calculate the memory used

LVM: Get partition type from /root/, which should be lvm


wall << End_Of_Message: sends the message (all that follows up to End_Of_Message) in the wall command which will display it on all active terminals

#Architecture: `uname -a`: retrieve all your OS information

#CPU physical: `lscpu | awk '/^CPU\(s\)/ {print $2}'`: retrieve in lscpu the 2nd column of the line that begins with CPU(s)

#vCPU: `grep -c 'processor' /proc/cpuinfo`: retrieve in cpuinfo the identifier of the CPU

echo $MEMUSED $MEMTOTAL | awk '{printf "#Memory Usage: %d/%dMB (%.2f%%)\n", ($1/1024), ($2/1024), ($1/$2)*100}'`: get the values of MEMUSED and MEMTOTAL, converts them into MB ( /1024), makes it a % which is expressed in float at 2 decimal places ready thanks to (%.2f%%)

`df --total | tail -n 1 | awk '{printf "#Disk Usage: %d/%dGb (%d%%)\n", ($3/1024), ($2/1048576), $5}'`: recup in df --total | the last line | display the memory used in the 3rd column (/in MB) on the total memory in the 2nd column (/in GB) and the total percentage of mem used, in the 5th column

`awk '{print "#CPU load: "$1"%\n"}' /proc/loadavg`: get current CPU usage from /proc/loadavg + %

#Last boot: `who -b | awk '{print $3" "$4}'` recup in who cols. 3 and 4: date and time

#LVM use: `if [ "$LVM" = "lvm" ]; : Compare your partition type (LVM) with lvm and return yes or no
then
echo "yes"
else
echo "no"
fi`

`awk '$4=="01" {count++} END{printf "#TCP connections: %d ESTABLISHED\n", count}' /proc/net/tcp`: retrieve the number of TCP connections by counting in the col. 4 of /proc/net/tcp the nb of 01

#Userlog: `who | awk '{print $1}' | only | wc -l`: retrieve in who the first column, only once per user

`ip -br a show $(ip route show default | awk '{print $5}') | sed 's/\/[[:digit:]]\{1,3\}//g' | awk '{printf "#Network: IP %s (%s)\n", $3, $4}'`: retrieve ipV4 address and MAC address with ip a | grep link/ether | awk 'print $2'

`grep -c 'COMMAND' /var/log/sudo/<name of the file where you put the activity of sudo> | awk '{printf "#Sudo: %d cmd\n", $1}'`

End_Of_Message


__CRON__
-----------------------------------------------

*More info on Cron configuration on [Fedora-fr.org](https://doc.fedora-fr.org/wiki/CRON_:_Configuration_de_t%C3%A2ches_automatis%C3%A9es#:~:text= The%20configuration%20of%20cron%20se,via%20the%20command%20crontab%20%2De.)*

</br>

Open the crontab file with `crontab -e`, choose Nano

Replace the line
```
m h  dom mon dow   command
``` 
for 

```
*/10 * * * * sh /path/to/script
```

Your script will then activate every 10min.

</br>

Check all: service --status-all

</br>

__Correction__
----
</br>
Comparison of your VM's signature and the one you submitted:

```
diff <(echo “string 1”) <(echo “string2”)
```

You must be able to explain:

- How a virtual machine works and its purpose

- Your choice between CentOs and Debian

- If you chose Debian: the difference between apt and Aptitude and what APPArmor is

- If you have chosen CentOs: what is SELinux and DNF

During the defense your script will have to run and display the desired information on your terminals.

Log in to a user account, other than root, with the correct password.

Verify that the UFW service is running:
```
sudo ufw status verbose OR sudo ufw status numbered
```

Verify that the SSH service is also running:
```
sudo service ssh status
```
Check the operating system:
```
uname -a
```

Check which groups the user belongs to: ```groups```

Create a new user:
```
sudo useradd 
```
List different users : 
```
cut -d: -f1 /etc/passwd
```

How set up rules for passwords : modified file :  
```
nano /etc/login.defs (aging of the passwords)
				
nano /etc/pam.d/common-password (pam : library allows root to authenticate users) (difok : number of char that need to be new ones / dcredit : digit / ucredit : majuscule / maxrepeat : repetition of chars / reject_username )
```

Create a new group : 	
```
sudo groupadd evaluating 

sudo adduser new evaluating 
					
sudo getent group evaluating
```
					
Advantages of password policy
—————————————————————————————————————————————————————————

Check hostname : sudo hostname

Modify hostname and reboot :
```
				sudo nano /etc/hostname
				sudo nano /etc/hosts
				sudo reboot
```
				
View partitions : 
```
sudo lsblk
```
LVM : disk management tool : physical volume (hardware), volume groups : VM and then can divide again : logical volumes : same as doing partitions to a physical ask but inside VM. LVM : allows you to do these divisions. (Flexible in size)

———————————————————————————————————————————————————————————

UFW : 
```
ufw allow 8080

ufw status numbered

ufw delete [rule_nbr]
```
Warning : you need to delete two rules : the one for income and outgoing
——————————————————————————————————————————————————————————

Check sudo is properly installed : 
```
sudo dpkg -l | grep sudo
```

Assign new user to sudo : 
```
sudo adduser <username> sudo
```

Check : 
```
sudo getent group sudo
```
	
Rules for sudo : authentication : 3 attempts, cannot connect with ssh with sudo, paths used by sudo restricted
	
Verify /var/log/sudo folder exist, has one file : sudoers_activity, has history of commands with sudo. Make command : sudo lsblk, should have been updated.
	
```
nano /etc/sudoers.d/myconfig
```
	
( sudo nano /etc/sudoers.d/myconfig)
	
( sudo nano /var/log/sudo/sudoers_activity)
	
——————————————————————————————————————————————————————————
	
Check SSH is installed : ```sudo ssh -V```
	
What is ssh and value of using it : network protocol, secure way to access a computer over a network. Keys and encrypted communication. Enable to log to the computers through network
	
Config file : 
```
nano /etc/ssh/sshd_config
```
Check ssh uses only the port 4242 : 
```
sudo cat /etc/ssh/sshd_config | grep Port
```
	
Use ssh in order to log in with the created user : 
	
 ip a : take first ip address : 127.0.0.1
	
 sudo passwd <user>
	
ssh <user>@127.0.0.1 -p 4242
	
logout
	
—————————————————————————————————————————————————————————
	
Explain you operation of script by displaying it : 
	
cd (to wherever you put your script)
	
```
	sudo nano ./monitoring.sh
```
Script : 
	
_ Memtotal : récupère la memoire, Memavail : recupere la memoire non utilisée, Bemused : difference
	
_LVM : type partition de root
	
_wall << End_Of_Message : career un message qui s’affiche dans les terminaux actifs
	
_Architecture : recupere informations OS
	
_CPU physical: get lscpu

_vCPU: get cpuinfo

_echo $MEMUSED $MEMTOTAL …: retrieve memory values, convert them to MB

_df …. : displays the memory used and the percentage of memory

_awk…: get CPU usage

_Last boot

_LVM use

_awk..: number of TCP connections

_userlog

_ip -br: ipV4 address

_number of commands performed by sudo

sudo bash monitoring.sh

Cron: Programm that allows users to execute scripts, commands or programs at a date/hour specified in advance or according to a specific cycle.
	
Ensure script runs every 1min / every 10 minutes. Make the script stop running when the server starts up.
	
the pdf will ask to launch the script every minute : 
sudo crontab -e
```	
 * * * * * sh /monitoring/monitoring.sh
```	
comment the line with the ****s to desactivate the launch of the script
	
——————————————————————————————————————————————————————
	
shasum born2beroot 
	
goinfre_born2beroot.vdi 


__Sources__
----
https://www.cyberciti.biz/faq/ubuntu-change-hostname-command/

https://doc.ubuntu-fr.org/useradd

https://debian-handbook.info/browse/fr-FR/stable/sect.apparmor.html

https://www.redhat.com/fr/topics/linux/what-is-selinux

https://doc.ubuntu-fr.org/lvm
