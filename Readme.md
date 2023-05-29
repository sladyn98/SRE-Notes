# Systems Interview Preperation

This repository is notes for linux, networking and operating systems concepts

## Linux Troubleshooting


**uptime** : Before learning about uptime you need to konw what 
System load is a measurement of the computational work the system is performing. This measurement is displayed as a number. A completely idle computer has a load average of 0. Each running process either using or waiting for CPU resources adds 1 to the load average. So, if your system has a load of 5, five processes are either using or waiting for the CPU.Unix systems traditionally just counted processes waiting for the CPU, but Linux also counts processes waiting for other resources — for example, processes waiting to read from or write to the disk.That’s why Unix-like systems don’t display the current load. They display the load average — an average of the computer’s load over several periods of time. This allows you to see how much work your computer has been performing.

`load average: 1.05, 0.70, 5.09`

over the last 1 minute: The computer was overloaded by 5% on average. On average, .05 processes were waiting for the CPU. (1.05)

over the last 5 minutes: The CPU idled for 30% of the time. (0.70)

over the last 15 minutes: The computer was overloaded by 409% on average. On average, 4.09 processes were waiting for the CPU. (5.09)


**vmstat** : Virtual memory statistics reporter, also known as vmstat, is a Linux command-line tool that reports various bits of system information. Things like memory, paging, processes, IO, CPU, and disk scheduling are all included in the array of information provided.

* vmstat 5 : interval for displaying vmstat information

* vmstat -s : Information about the system changes since the last time the system was booted.

* **r (runqueue)** The runqueue value shows the number of tasks executing and waiting for CPU resources. When this number exceeds the number of CPUs on the server, a CPU bottleneck exists, and some tasks are waiting for execution.

* **free**: Free memory in kilobytes. If there are too many digits to count, you have enough free memory. The “free -m” command, included as command 7, better explains the state of free memory.

* **si, so**: When you load a file or program, the file is stored in the random access memory (RAM). Since RAM is finite, some files cannot fit on it. These files are stored in a special section of the hard drive called the "swap file". swapping is a mechanism in which a process can be swapped temporarily out of memory to a backing store (Swap OUt) and then brought back into memory for continued execution.(Swap In). In other words, when the amount of physical memory (RAM) is full. If the system(say one process) needs more memory resources and the RAM is full, inactive pages in memory are moved to the swap space(part of hard disk) & these inactive pages will be brought back for further execution.

* **pi (page in)** A page-in operation occurs when the server is experiencing a shortage of RAM memory. While all virtual memory servers will page out to the swap disk, page-in operations show that the servers has exceeded the available RAM storage. Any nonzero value for pi indicates excessive activity as RAM memory contents are read in from the swap disk.

* **us (user CPU)** This is the amount of CPU that is servicing user tasks.

* **sy (system CPU)** This is the percentage of CPU being used to service system tasks.

* **id (idle)** This is the percentage of CPU that is idle.

* **wa** (wait?IBM-AIX only) This shows the percentage of CPU that is waiting on external operations such as disk I/O.

If the r(runque) value is more than the number of processes on the CPU there is something blocking the number of resources

The CPU time breakdowns will confirm if the CPUs are busy, by adding user + system time. A constant degree of wait I/O points to a disk bottleneck; this is where the CPUs are idle, because tasks are blocked waiting for pending disk I/O

**mpstat**
It is used for multiprocessor statistics

  * mpstat -P ALL 1 : This command prints CPU time breakdowns per CPU, which can be used to check for an imbalance. A single hot CPU can be evidence of a single-threaded application. 

**pidstat 1** 

`pidstat 1` --> pidstat output per second

`pidstat -t 1` ---> per thread breakdowns

`pidstat -d ` --> block device IO

Pidstat is a little like top’s per-process summary, but prints a rolling summary instead of clearing the screen. This can be useful for watching patterns over time, and also recording what you saw (copy-n-paste) into a record of your investigation

**iostat** 

`iostat -x 1`

* r/s, w/s, rkB/s, wkB/s: These are the delivered reads, writes, read Kbytes, and write Kbytes per second to the device. Use these for workload characterization. A performance problem may simply be due to an excessive load applied.
* await: The average time for the I/O in milliseconds. This is the time that the application suffers, as it includes both time queued and time being serviced. Larger than expected average times can be an indicator of device saturation, or device problems.
* avgqu-sz: The average number of requests issued to the device. Values greater than 1 can be evidence of saturation (although devices can typically operate on requests in parallel, especially virtual devices which front multiple back-end disks.)
* %util: Device utilization. This is really a busy percent, showing the time each second that the device was doing work. Values greater than 60% typically lead to poor performance (which should be seen in await), although it depends on the device. Values close to 100% usually indicate saturation.

**ps** 

* ps -ef f : used to status list

**sar**

This is a system activity report

`sar -n DEV 1`

**netstat**

`netstat -s`:  To view TCP/UDP statistics

**nicstat**

**swapon** 

**strace**

`strace -p PID`: gives you the system trace call for the device


**You can use the cat /procinfo/cpuinfo to get the values of the number of processors**

* ifconfig : ifconfig (interface configurator) command is use to initialize an interface, assign IP Address to interface and enable or disable interface on demand. With this command you can view IP Address and Hardware / MAC address assign to interface and also MTU (Maximum transmission unit) size.

* Find out the size of a system:  `df -h`

* Modify access control: `setfacl -m u:oamsys:rwx secret` - The setfacl modifies the username oamsys with the following permissiongs rwx. 


### Deep dive on tcpdump

Tcpdump can be used as a network analyser tool to detect network traffic 

-i any : Listen on all interfaces just to see if you’re seeing any traffic.

### What is perf

### What is strace

iostat, to monitor the service times of your disks

iotop (if your kernel supports it), to monitor the breakdown of IO requests per process

strace, to look at the actual operations issued by a process

#### What is an inode

An inode is a file structure 

#### How to drop cache in linux

You basically send an echo command to proc filesystem 

```
$ sudo sh -c 'echo 1 >/proc/sys/vm/drop_caches'
$ sudo sh -c 'echo 2 >/proc/sys/vm/drop_caches'
$ sudo sh -c 'echo 3 >/proc/sys/vm/drop_caches'
```


#### How to debug out of memory errors in linux

* You can check the `var/logs` to search for an out of memory error: 
`sudo grep -i -r 'out of memory' /var/log/`

* You can search for the oom killer as well : `dmesg | grep oom-killer`

* You can then use the `free -h` command to see how much of memory is available and to see if its less which might be causing an out of memory error or even slower IO operations.

* You can even use `top` to check for the memory usage and see which process is using it.

* Decrease the swappiness value which means the kernel will aggressively swap in and out of cache


#### Learnings

a) First check the system load of the server. You can use `uptime` for this. This will give you the average system load for time intervals, you can then see if the load is increasing or decreasing.

b) You can then check the lines of `dmesg` to see the last messages and check if the system has sent something important.

c) You can then check for the system is waiting for resources or where exactly is the system spending time on. You can do this using `vmstat`. You can check the runqueue to see if the system is waiting too long or you can check the us+sy time. Or you can see the WaitIO to see if its waiting for some IO disk operation.

d) You can then check if an individual process is consuming memory eg : `pidstat`



# Troubleshooting networking issues

1) Check the available interfaces using `ifconfig`

2) Then inspect the `eth0` using `ifconfig eth0`, basically look for the interface to be configured correctl.y. If I can't seem to get the interface to come up, and this host gets its IP from DHCP, I might have to move my troubleshooting focus to the DHCP server.

3) If the machine is on the same subnet we can try and ping the gateway router to check if we can communicate with it. If not then how will we communicate with hosts outside our subnet. So figure out the gateway using `sudo route -n`. the flag will give the values in numeric form.

4) Once we get the gateway address we can ping it 
`ping -c 5 address` to see if its coming back with the packets. If we cant talk it could be ICMP is blocked. If not its possible there is a VLAN issue.

5) If the machine you are trying to communicate is on the same subnet you dont need the gateway configured to be able to communicate to it.

6) Now you can check if the port is open on the remote machine using telnet. So the command is `telnet IP port` and if you get smtp commands it means something is wrong with your smtp configuration. If it comes back with connection refused it could be that the port is down or you are being blocked with a firewall, usually firewalls drop the packet and we can use nmap to do a port scan.

7) `nmap -p port IP ` In this case, nmap says the port is filtered, which tells me there is a firewall blocking this port. If these machines were on different subnets, there might be a firewall in between the networks restricting access. Because I know these machines are on the same subnet, I would assume that there is some iptables firewall configured on bill that needs to be checked.

8) Now we need to examine the remote machine directly 
`sudo netstat -lnp | grep :25`. Check the port 25, and see the column where it states it is listening to local addresses. If it is not listening to 0.0.0.0.25

# Troubleshooting networking issues - 2

1) We can use the `nslookup` to find the IP address, it can be used to resolve issues with the dns

2) 
```
$ nslookup www.linuxjournal.com
;; connection timed out; no servers could be reached
```
This error tells me that nslookup couldn't communicate with my DNS server. That could be because either I don't have any name servers configured on my system or I just can't reach them. To see whether I have any name servers configured, I would check my /etc/resolv.conf file. This file keeps track of what name servers I should use. In my case, it would look like this:

3) If you aren't sure what port a service uses, check the /etc/services file on your system. It lists most of the common services you will use.

4) If the dns server responds but does not find a record If you try nslookup against the fully qualified domain name and you still get the same NXDOMAIN error above, your problem is with the name server itself. Troubleshooting the full range of DNS server problems is a bit beyond what I could reasonably fit in this column, but here are a few steps to get you started. If you know your DNS server is configured to have the record you are looking for itself, you need to examine its zone records to make sure that particular hostname exists. If, on the other hand, you are searching for a domain for which you know it doesn't have a record (say, www.linuxjournal.com), it's possible your DNS server isn't allowing recursive queries from your host or at all. You can test that by trying to resolve some other remote host on the Internet. If it doesn't resolve, it's probably a recursion setting. If it does resolve, the problem might very well be with that remote site's DNS server.


5) Do a traceroute and search for ***** it could be a problem with the remote port



## Difference between VM and containers
  * Virtual machines offer hardware level isolation and containers offer software level isolation.
  * Virtual machines require a hypervisor to manage resources whereas containers use kernel features like namespaces and cgroups to share the host OS resources
  * High overhead because of duplication of OS components


## How does the linux kernel boot up 

The first step in the boot process is the POST (Power on self test), This involves sending power to the hardware to check if everything is working fine. If this fails that means the hardware is not working correctly and the boot program aborts.
If POST succeeds it sends an INT 13H interrupt to the kernel to locate the boot program.It sends information like the sector the kernel head etc from which to read the boot process from. The boot program that linux uses which is very famous is GRUB.

**GRUB stages**
The primary function of either GRUB is to get the Linux kernel loaded into memory and running. 

Stage 1: During the BIOS POST process, the BIOS searches for a boot record located in the Master Boot Record (MBR) of attached disks. The small bootstrap code in GRUB stage 1, occupying 446 bytes, is loaded into memory along with the partition table. Stage 1's main purpose is to locate and load stage 1.5. Stage 1.5, located between the boot record and the first partition, is responsible for understanding filesystem structures. Once loaded into RAM, stage 1 hands control over to stage 1.5, allowing further execution of the boot process.

Stage 1.5: Stage 1.5 of GRUB (core.img) must be located between the boot record and the first partition on the disk drive. This space, historically left unused, provides enough room (31,744 bytes) for core.img (25,389 bytes). Stage 1.5 contains additional code and common filesystem drivers, enabling it to access file systems like EXT, FAT, and NTFS. Stage 2 of GRUB2 is typically located in the /boot filesystem, specifically /boot/grub2, which must be on a supported filesystem. Stage 1.5 executes with the necessary filesystem drivers to locate and load the stage 2 files from /boot.

Stage 2: GRUB2 stage 2, located in the /boot/grub2 directory, does not have an image file like stages 1 and 2. It consists of runtime kernel modules loaded from /boot/grub2/i386-pc as needed. The main function of stage 2 is to locate and load a Linux kernel into RAM and hand over control to the kernel. The /boot directory contains the kernel files, typically named starting with vmlinuz. GRUB2 supports booting from multiple kernel versions, allowing fallback options in case of issues. Stage 2 loads the selected kernel into memory and transfers control to it.

Kernel: During the boot process, the kernels in the /boot directory are stored in a compressed format to save space. When a selected kernel is loaded into memory, it needs to extract itself from the compressed file before proceeding. An initial RAM disk image and device maps of the hard drives are also located in /boot. Once the kernel is extracted, it loads systemd, the successor to SysV init, and hands over control to it. At this stage, the Linux kernel and systemd are running, but no user tasks are active yet.


Systemd is a system and service manager in Linux that handles the boot process, manages services, and provides advanced features like parallelization and dependency management. It uses targets to represent different system states, manages service units, and offers runtime configuration options. With its logging system and system control utilities, systemd enhances system initialization, service management, and control in Linux environments.
Systemd has checkpoints in the startup process represented by targets like sysinit.target and basic.target. Sysinit.target ensures that low-level services and units required for system functionality are completed. Basic.target starts units necessary for subsequent targets. The user-level targets, multi-user.target and graphical.target, can then be initialized. These targets mark the completion of the startup process, with multi-user.target leading to a text mode login and graphical.target leading to a graphical login.


**System Shutdown**
systemd provides various system services, such as systemd-poweroff.service, systemd-halt.service, systemd-reboot.service, and systemd-kexec.service, which are triggered by respective targets like poweroff.target, halt.target, reboot.target, and kexec.target. These services handle the actual power-off, halt, reboot, and kexec operations.

When these services are run, they replace PID 1 with the systemd-shutdown tool, responsible for the shutdown process. Before shutting down, systemd-shutdown attempts to unmount file systems, disable swap devices, detach storage devices, and terminate processes.

To ensure system compatibility during upgrades, the shutdown functionality is separated into a separate binary, as the running PID 1 might depend on libraries that are no longer available.

Before performing the actual power-off, halt, reboot, or kexec actions, systemd-shutdown executes all executables found in the /usr/lib/systemd/system-shutdown/ directory, passing the corresponding action as an argument. These executables run in parallel, and the action is only completed when all executables finish.

It is important to trigger system shutdown using commands like "systemctl poweroff" instead of executing systemd-poweroff.service directly.

