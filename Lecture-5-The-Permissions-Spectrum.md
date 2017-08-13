#### Lecture 5: The Permissions Spectrum

###### [```BIOS```](https://www.computerhope.com/jargon/b/bios.htm)

- _Basic Input/Output System_
- _ROM_ Chip
- Allows you to access and set up your computer system at the most basic level
- 4 function
	- ```POST``` 
		- _Power On Self Test_
		- Test the computer hardware and make sure no errors exist before loading the operating system
	- ```Bootstrap Loader```
		- Locate the operating system. If a capable operating system is located, the BIOS will pass control to it.
	- ```BIOS drivers```
		- Low level drivers that give the computer basic operational control over your computer's hardware.
	- ```BIOS``` or ```CMOS``` Setup
		- Configuration program that allows you to configure hardware settings including system settings such as computer passwords, time, and date.

###### [```CMOS```](https://www.computerhope.com/jargon/c/cmos.htm)

- _Complementary Metal-Oxide Semiconductor_
- On-board, battery powered semiconductor chip inside computers that stores information
- Stores system time and date to system hardware settings for your computer.

###### General Boot Process

- BIOS
- POST
	- CMOS
	- Hardware initialization (CPU, Video Card, …)
- Bootloader
- OS

###### [```Linux Boot Process```](http://www.thegeekstuff.com/2011/02/linux-boot-process/)

![Image of LBP](images/5/linux-boot-process.png)

- ```BIOS```
- ```MBR```
	- ```512 bytes``` in size
	- 3 components
		- ```Primary boot loader info``` in 1st ```446 bytes```
		- ```Partition table info``` in next ```64 bytes```
		- ```MBR validation check``` in last ```2 bytes```
	- MBR loads and executes the GRUB boot loader
- ```GRUB```
	- Loads and executes ```Kernel``` and ```initrd``` images.
	- calls ```start_kernel()``` on the selected option
	- ```start_kernel()``` performs most of the system setup
		- interrupt handling, memory manager, device initialization, drivers
	- ```start_kernel()``` spawns the ```idle``` process, process scheduler, and the init process (which is in ```userspace```) 
- ```Kernel```
	- Mounts the root file system
	- Kernel executes the ```/sbin/init``` program
	- ```init``` is the 1st program to be executed by Linux Kernel so it has the ```PID``` of 1

		```sh
		l32@l32-VirtualBox:~$ ps -ef | grep init
		root         1     0  0 16:13 ?        00:00:01 /sbin/init splash
		l32       4883  2139  0 16:31 pts/1    00:00:00 grep --color=auto init
		l32@l32-VirtualBox:~$
		```
	
	- ```initrd``` stands for Initial RAM Disk
	- ```initrd``` is used by kernel as temporary root file system until kernel is booted and the real root file system is mounted. It also contains necessary drivers compiled inside, which helps it to access the hard drive partitions, and other hardware.
	- Handles all operating system processes
		- Memory management
		- Task scheduling (context switching)
		- I/O (and CPU interrupts, per packet, keystroke, etc...)
		- Interprocess communication
		- Overall system control
	- Kernel is typically loaded as an image file, compressed into either ```zImage``` or ```bzImage``` formats (using zlib).
	- Linux has a ```monolithic``` kernel

- ```Init```
	- Looks at the ```/etc/inittab``` file to decide the Linux run level
	
		Number | Run Level
		-------|----------
		0 		| halt
		1 		| Single user mode
		2 		| Multiuser, without NFS
		3 		| Full multiuser mode
		4 		| unused
		5 		| X11
		6 		| reboot
		
		```sh
		l32@l32-VirtualBox:~$ who -r
         run-level 5  2017-08-12 16:14
		l32@l32-VirtualBox:~$
		```
- ```Runlevel```
	- When the Linux system is booting up, you might see various services getting started. For example, it might say ```starting sendmail …. OK```. Those are the runlevel programs, executed from the run level directory as defined by your run level.
	- Depending on your default init level setting, the system will execute the programs from one of the following directories.
	
		Run Level | Directory
		----------|----------
		Run level 0 | /etc/rc.d/rc0.d/
		Run level 1 | /etc/rc.d/rc1.d/
		Run level 2 | /etc/rc.d/rc2.d/
		Run level 3 | /etc/rc.d/rc3.d/
		Run level 4 | /etc/rc.d/rc4.d/
		Run level 5 | /etc/rc.d/rc5.d/
		Run level 6 | /etc/rc.d/rc6.d/
		
		```sh
		l32@l32-VirtualBox:/etc$ tree rc*
		rc0.d
		├── K01alsa-utils -> ../init.d/alsa-utils
		├── K01bluetooth -> ../init.d/bluetooth
		├── K01irqbalance -> ../init.d/irqbalance
		├── K01lightdm -> ../init.d/lightdm
		├── K01plymouth -> ../init.d/plymouth
		├── K01resolvconf -> ../init.d/resolvconf
		├── K01thermald -> ../init.d/thermald
		├── K01urandom -> ../init.d/urandom
		├── K01uuidd -> ../init.d/uuidd
		├── K02sendsigs -> ../init.d/sendsigs
		├── K03rsyslog -> ../init.d/rsyslog
		├── K04hwclock.sh -> ../init.d/hwclock.sh
		├── K04umountnfs.sh -> ../init.d/umountnfs.sh
		├── K05networking -> ../init.d/networking
		├── K06umountfs -> ../init.d/umountfs
		├── K07umountroot -> ../init.d/umountroot
		├── K08halt -> ../init.d/halt
		└── README
		rc1.d
		├── K01alsa-utils -> ../init.d/alsa-utils
		├── K01bluetooth -> ../init.d/bluetooth
		├── K01irqbalance -> ../init.d/irqbalance
		├── K01lightdm -> ../init.d/lightdm
		├── K01ntp -> ../init.d/ntp
		├── K01thermald -> ../init.d/thermald
		├── K01ufw -> ../init.d/ufw
		├── K01uuidd -> ../init.d/uuidd
		├── K01whoopsie -> ../init.d/whoopsie
		├── K03rsyslog -> ../init.d/rsyslog
		├── README
		├── S01killprocs -> ../init.d/killprocs
		└── S02single -> ../init.d/single
		rc2.d
		├── README
		├── S01apport -> ../init.d/apport
		├── S01rsyslog -> ../init.d/rsyslog
		├── S01uuidd -> ../init.d/uuidd
		├── S02anacron -> ../init.d/anacron
		├── S02cron -> ../init.d/cron
		├── S02dbus -> ../init.d/dbus
		├── S02irqbalance -> ../init.d/irqbalance
		├── S02ntp -> ../init.d/ntp
		├── S02rsync -> ../init.d/rsync
		├── S02ssh -> ../init.d/ssh
		├── S02thermald -> ../init.d/thermald
		├── S02whoopsie -> ../init.d/whoopsie
		├── S03bluetooth -> ../init.d/bluetooth
		├── S03lightdm -> ../init.d/lightdm
		├── S04grub-common -> ../init.d/grub-common
		├── S04ondemand -> ../init.d/ondemand
		├── S04plymouth -> ../init.d/plymouth
		└── S04rc.local -> ../init.d/rc.local
		rc3.d
		├── README
		├── S01apport -> ../init.d/apport
		├── S01rsyslog -> ../init.d/rsyslog
		├── S01uuidd -> ../init.d/uuidd
		├── S02anacron -> ../init.d/anacron
		├── S02cron -> ../init.d/cron
		├── S02dbus -> ../init.d/dbus
		├── S02irqbalance -> ../init.d/irqbalance
		├── S02ntp -> ../init.d/ntp
		├── S02rsync -> ../init.d/rsync
		├── S02ssh -> ../init.d/ssh
		├── S02thermald -> ../init.d/thermald
		├── S02whoopsie -> ../init.d/whoopsie
		├── S03bluetooth -> ../init.d/bluetooth
		├── S03lightdm -> ../init.d/lightdm
		├── S04grub-common -> ../init.d/grub-common
		├── S04ondemand -> ../init.d/ondemand
		├── S04plymouth -> ../init.d/plymouth
		└── S04rc.local -> ../init.d/rc.local
		rc4.d
		├── README
		├── S01apport -> ../init.d/apport
		├── S01rsyslog -> ../init.d/rsyslog
		├── S01uuidd -> ../init.d/uuidd
		├── S02anacron -> ../init.d/anacron
		├── S02cron -> ../init.d/cron
		├── S02dbus -> ../init.d/dbus
		├── S02irqbalance -> ../init.d/irqbalance
		├── S02ntp -> ../init.d/ntp
		├── S02rsync -> ../init.d/rsync
		├── S02ssh -> ../init.d/ssh
		├── S02thermald -> ../init.d/thermald
		├── S02whoopsie -> ../init.d/whoopsie
		├── S03bluetooth -> ../init.d/bluetooth
		├── S03lightdm -> ../init.d/lightdm
		├── S04grub-common -> ../init.d/grub-common
		├── S04ondemand -> ../init.d/ondemand
		├── S04plymouth -> ../init.d/plymouth
		└── S04rc.local -> ../init.d/rc.local
		rc5.d
		├── README
		├── S01apport -> ../init.d/apport
		├── S01rsyslog -> ../init.d/rsyslog
		├── S01uuidd -> ../init.d/uuidd
		├── S02anacron -> ../init.d/anacron
		├── S02cron -> ../init.d/cron
		├── S02dbus -> ../init.d/dbus
		├── S02irqbalance -> ../init.d/irqbalance
		├── S02ntp -> ../init.d/ntp
		├── S02rsync -> ../init.d/rsync
		├── S02ssh -> ../init.d/ssh
		├── S02thermald -> ../init.d/thermald
		├── S02whoopsie -> ../init.d/whoopsie
		├── S03bluetooth -> ../init.d/bluetooth
		├── S03lightdm -> ../init.d/lightdm
		├── S04grub-common -> ../init.d/grub-common
		├── S04ondemand -> ../init.d/ondemand
		├── S04plymouth -> ../init.d/plymouth
		└── S04rc.local -> ../init.d/rc.local
		rc6.d
		├── K01alsa-utils -> ../init.d/alsa-utils
		├── K01bluetooth -> ../init.d/bluetooth
		├── K01irqbalance -> ../init.d/irqbalance
		├── K01lightdm -> ../init.d/lightdm
		├── K01plymouth -> ../init.d/plymouth
		├── K01resolvconf -> ../init.d/resolvconf
		├── K01thermald -> ../init.d/thermald
		├── K01urandom -> ../init.d/urandom
		├── K01uuidd -> ../init.d/uuidd
		├── K02sendsigs -> ../init.d/sendsigs
		├── K03rsyslog -> ../init.d/rsyslog
		├── K04hwclock.sh -> ../init.d/hwclock.sh
		├── K04umountnfs.sh -> ../init.d/umountnfs.sh
		├── K05networking -> ../init.d/networking
		├── K06umountfs -> ../init.d/umountfs
		├── K07umountroot -> ../init.d/umountroot
		├── K08reboot -> ../init.d/reboot
		└── README
		rc.local [error opening dir]
		rcS.d
		├── README
		├── S01console-setup -> ../init.d/console-setup
		├── S02alsa-utils -> ../init.d/alsa-utils
		├── S02apparmor -> ../init.d/apparmor
		├── S02hostname.sh -> ../init.d/hostname.sh
		├── S02mountkernfs.sh -> ../init.d/mountkernfs.sh
		├── S02plymouth-log -> ../init.d/plymouth-log
		├── S02pppd-dns -> ../init.d/pppd-dns
		├── S02resolvconf -> ../init.d/resolvconf
		├── S02ufw -> ../init.d/ufw
		├── S02x11-common -> ../init.d/x11-common
		├── S03udev -> ../init.d/udev
		├── S04keyboard-setup -> ../init.d/keyboard-setup
		├── S05mountdevsubfs.sh -> ../init.d/mountdevsubfs.sh
		├── S05procps -> ../init.d/procps
		├── S06hwclock.sh -> ../init.d/hwclock.sh
		├── S07checkroot.sh -> ../init.d/checkroot.sh
		├── S08checkfs.sh -> ../init.d/checkfs.sh
		├── S09checkroot-bootclean.sh -> ../init.d/checkroot-bootclean.sh
		├── S09kmod -> ../init.d/kmod
		├── S09urandom -> ../init.d/urandom
		├── S10mountall.sh -> ../init.d/mountall.sh
		├── S10networking -> ../init.d/networking
		├── S11mountall-bootclean.sh -> ../init.d/mountall-bootclean.sh
		├── S11mountnfs.sh -> ../init.d/mountnfs.sh
		├── S12mountnfs-bootclean.sh -> ../init.d/mountnfs-bootclean.sh
		└── S13bootmisc.sh -> ../init.d/bootmisc.sh
		
		0 directories, 152 files
		l32@l32-VirtualBox:/etc$
		```
		
		- Programs starts with ```S``` are used during startup. ```S for startup```
		- Programs starts with ```K``` are used during shutdown. ```K for kill```
		- There are numbers right next to S and K in the program names. 
		- Those are the sequence number in which the programs should be started or killed.

###### Academic Ring Model

- For fault tolerance, and security
- Provide different levels of access 

Ring | | Application
-----|------|-------
3 | Normal non-root | user applications
2 | Device drivers | keyboard, mice
1 | Device drivers | video card
0 | Kernel

![Image of ring](images/5/ring.jpg)

- Things operate higher than ring 0

Operating Mode |Full Form | Ring
-----|----|---------
IPMI | Intelligent Platform Management Interface | -3
SMM | System Management Mode | -2
BIOS | Basic Input/Output System | -1

- The original ```Multics``` had ```8``` rings!
	- had special register for ring #

###### Practical Rings model

- For fault tolerance, and security
- Provide different levels of access 

Rings | Apps
----------|--------------
4 | Sandboxed non-root user applications
3 | Normal non-root user applications
0 | Kernel / root user
-1 | BIOS
-2 | SMM
-3 | IPMI (Servers)
   | Physical Access

###### Vulnerability Research

- Privilege Escalation:
	- Type of attack resulting in higher (or more) permissions for user/attacker.
	- Goes beyond to -1, -2, -3 rings
	- Sometimes going from 3 to -3 is harder than 3 to 0
	![Image of ring](images/5/1.jpeg)
	
- Pivoting: Priv Esc to remote system

![Image of priv](images/5/2.jpeg)

- Pivoting: Lateral Movement

![Image of priv](images/5/3.jpeg)

![Image of priv](images/5/4.jpeg)



