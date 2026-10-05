# Linux Interview Questions for DevOps Engineers (3–5 Years Experience)

Detailed Answers in README Format

---

# 🐧 Linux Fundamentals

## 1. What happens internally when you execute a Linux command?

When a Linux command is executed, the shell first reads and parses the command entered by the user. The shell then checks whether the command is a built-in command or an external executable. If it is an external command, the shell searches for the executable using the PATH environment variable.

The shell creates a new process using the `fork()` system call. After that, the child process replaces itself with the actual command using the `exec()` system call. The Linux kernel then loads the executable into memory, allocates resources, and schedules the process for execution.

Once execution completes, the process exits and returns an exit status back to the shell.

---

## 2. Explain the Linux boot process in detail.

The Linux boot process starts when the system is powered on.

1. BIOS/UEFI initializes hardware.
2. Bootloader (GRUB) loads the Linux kernel.
3. Kernel initializes CPU, memory, and drivers.
4. initramfs mounts temporary root filesystem.
5. Kernel starts the init/systemd process.
6. systemd starts services and targets.
7. Login prompt or GUI becomes available.

In modern systems, `systemd` is responsible for managing services and boot targets.

---

## 3. Difference between kernel space and user space?

Kernel space is reserved for the Linux kernel and critical system components. It has full access to hardware resources, memory, and devices.

User space is where user applications run with restricted permissions. Applications cannot directly access hardware and must interact with the kernel using system calls.

This separation improves system stability and security.

---

## 4. What is an inode and why is it important?

An inode is a metadata structure used by Linux filesystems to store information about files.

An inode contains:

* File permissions
* Ownership
* File size
* Timestamps
* Disk block locations

It does not store the filename itself.

Inodes are important because every file requires one inode. If inode usage reaches 100%, new files cannot be created even if disk space is available.

---

## 5. Difference between process, program, and thread?

A program is a static executable file stored on disk.

A process is a running instance of a program with its own memory and resources.

A thread is the smallest execution unit within a process. Multiple threads can exist within the same process and share memory.

Processes are isolated, while threads are lightweight and share resources.

---

## 6. What are daemons in Linux?

Daemons are background processes that run continuously and provide system or application services.

Examples include:

* sshd
* crond
* httpd
* systemd

Daemons usually start during system boot and run without user interaction.

---

## 7. Explain virtual memory in Linux.

Virtual memory allows Linux to use disk space as an extension of RAM.

It enables:

* Running large applications
* Process isolation
* Efficient memory management

Linux uses paging and swap space to manage virtual memory.

Virtual memory improves multitasking but excessive swapping can reduce performance.

---

## 8. Difference between BIOS and UEFI?

BIOS is the traditional firmware interface used to initialize hardware during boot.

UEFI is the modern replacement for BIOS.

Advantages of UEFI:

* Faster boot
* Supports larger disks
* Better security
* GUI support
* Secure Boot

Most modern Linux systems use UEFI.

---

## 9. What is swap memory and when is it used?

Swap memory is disk space used as virtual memory when physical RAM becomes insufficient.

Linux moves inactive memory pages from RAM to swap.

Swap is used during:

* High memory utilization
* Large workloads
* Memory pressure situations

Excessive swap usage can severely impact performance because disk access is slower than RAM.

---

## 10. How does Linux handle multitasking?

Linux uses preemptive multitasking.

The kernel scheduler allocates CPU time to processes using scheduling algorithms.

Each process gets a time slice, enabling multiple processes to run seemingly simultaneously.

Linux supports:

* Multithreading
* Multiprocessing
* Process prioritization

This allows efficient resource utilization.

---

# 📂 File System & Storage

## 1. Difference between soft link and hard link?

A hard link directly points to the inode of a file.

A soft link, or symbolic link, points to the file path.

Hard link characteristics:

* Cannot span filesystems
* Works even if original filename is deleted

Soft link characteristics:

* Can span filesystems
* Breaks if original file is removed

---

## 2. How do you check disk usage and inode usage?

Disk usage:

```bash
df -h
```

Inode usage:

```bash
df -i
```

Directory-level disk usage:

```bash
du -sh *
```

These commands help identify storage and inode issues.

---

## 3. Difference between ext4, xfs, and btrfs?

ext4 is stable, widely used, and suitable for general workloads.

XFS is optimized for high-performance and large filesystems.

Btrfs supports advanced features such as:

* Snapshots
* Compression
* Deduplication
* RAID-like functionality

Enterprise environments commonly use ext4 and XFS.

---

## 4. What is LVM and why is it used?

LVM stands for Logical Volume Manager.

It provides flexible disk management by abstracting physical storage into logical volumes.

Benefits:

* Online resizing
* Easier storage management
* Snapshot support
* Flexible partitioning

LVM is commonly used in enterprise Linux systems.

---

## 5. How do you extend a filesystem in Linux?

Typical steps:

1. Add disk space
2. Extend partition or logical volume
3. Resize filesystem

For XFS:

```bash
xfs_growfs
```

For ext4:

```bash
resize2fs
```

LVM environments simplify filesystem expansion.

---

## 6. How do you identify large files consuming disk space?

Commands used:

```bash
du -sh *
find / -type f -size +1G
```

Useful tools:

* du
* find
* ncdu

These help identify storage-heavy files and directories.

---

## 7. What happens when inode usage reaches 100%?

When inode usage reaches 100%, new files cannot be created even if free disk space exists.

This usually happens when millions of small files exist.

Troubleshooting involves:

* Identifying directories with excessive files
* Removing unnecessary files
* Increasing inode count during filesystem creation

---

## 8. Difference between mount and unmount?

Mount attaches a filesystem to the Linux directory tree.

Unmount safely detaches it.

Example:

```bash
mount /dev/sdb1 /data
umount /data
```

Unmounting is important before removing storage devices.

---

## 9. What is fstab and why is it important?

`/etc/fstab` is a configuration file that defines filesystems mounted automatically during boot.

It contains:

* Device information
* Mount points
* Filesystem type
* Mount options

Incorrect fstab entries can cause boot failures.

---

## 10. Explain RAID levels in Linux.

RAID improves performance, redundancy, or both.

Common RAID levels:

* RAID 0 → Performance only
* RAID 1 → Mirroring
* RAID 5 → Parity with fault tolerance
* RAID 10 → Performance + redundancy

RAID is commonly used in production storage systems.

---

# ⚙️ Process Management

## 1. How do you check running processes?

Commands used:

```bash
ps -ef
top
htop
```

These commands help monitor CPU, memory, and process activity.

---

## 2. Difference between foreground and background processes?

Foreground processes interact directly with the terminal.

Background processes run independently and do not block terminal usage.

Background execution:

```bash
command &
```

---

## 3. What is a zombie process and orphan process?

A zombie process has completed execution but still exists in the process table because the parent has not collected its exit status.

An orphan process continues running after its parent process exits. It gets adopted by init/systemd.

---

## 4. How do you troubleshoot high CPU utilization?

Commands used:

```bash
top
htop
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu
```

Steps:

* Identify high CPU processes
* Check logs
* Verify recent deployments
* Analyze thread usage
* Restart or optimize application if required

---

## 5. Explain process states in Linux.

Common process states:

* R → Running
* S → Sleeping
* D → Uninterruptible sleep
* Z → Zombie
* T → Stopped

These states help understand process behavior.

---

## 6. What is process scheduling?

Process scheduling is the mechanism by which the Linux kernel allocates CPU time to processes.

Linux uses scheduling algorithms to ensure fairness and efficient CPU utilization.

Real-time and normal scheduling policies are supported.

---

## 7. Difference between nice and renice?

`nice` starts a process with a specific priority.

`renice` changes the priority of an already running process.

Lower nice value means higher priority.

---

## 8. How do you identify memory-consuming processes?

Commands:

```bash
top
ps aux --sort=-%mem
```

These help identify processes consuming excessive memory.

---

## 9. What happens when a process is killed using kill -9?

`kill -9` sends SIGKILL.

The process is terminated immediately without cleanup.

Applications cannot intercept SIGKILL.

This may cause:

* Data corruption
* Incomplete transactions
* Resource leaks

---

## 10. Explain parent-child process relationship.

Processes created using fork() become child processes.

The creating process is called the parent process.

Child processes inherit resources and environment from the parent.

Linux organizes processes hierarchically.

---

# 🔥 Performance Troubleshooting

## 1. How do you troubleshoot a slow Linux server?

I follow a layered troubleshooting approach.

Steps include:

* Check CPU usage
* Check memory utilization
* Verify disk I/O
* Analyze load average
* Check network latency
* Review logs

Commands commonly used:

```bash
top
vmstat
iostat
sar
free -m
```

---

## 2. What commands do you use for performance analysis?

Common commands:

```bash
top
htop
vmstat
iostat
sar
free -m
netstat
ss
```

These help analyze CPU, memory, disk, and network performance.

---

## 3. Explain load average with real-time examples.

Load average represents the number of processes waiting for CPU.

Example:

```bash
load average: 2.00 1.50 1.20
```

Values represent averages over:

* 1 minute
* 5 minutes
* 15 minutes

If an 8-core server shows load 2, the system is healthy.

If load exceeds CPU core count consistently, performance degradation may occur.

---

## 4. How do you troubleshoot high memory utilization?

Commands:

```bash
free -m
top
vmstat
```

Steps:

* Identify memory-heavy processes
* Check swap usage
* Analyze memory leaks
* Verify caching behavior

Excessive swap usage indicates memory pressure.

---

## 5. How do you identify disk bottlenecks?

Commands:

```bash
iostat -x
iotop
```

Key indicators:

* High await time
* High %util
* Long queue size

These indicate storage performance issues.

---

## 6. Difference between CPU wait and I/O wait?

CPU wait means processes are waiting for CPU resources.

I/O wait means CPU is idle while waiting for disk operations.

High I/O wait usually indicates slow storage.

---

## 7. What is swapping and how does it affect performance?

Swapping occurs when Linux moves memory pages from RAM to disk swap space.

Heavy swapping significantly slows applications because disk access is much slower than RAM.

---

## 8. How do you analyze performance using vmstat?

`vmstat` provides:

* CPU statistics
* Memory usage
* Swap activity
* I/O metrics

Example:

```bash
vmstat 1
```

Useful fields:

* si/so → swap activity
* wa → I/O wait
* us/sy → CPU usage

---

## 9. Explain iostat metrics like await, svctm, and %util.

`await` → Average wait time for I/O requests.

`svctm` → Average service time.

`%util` → Disk utilization percentage.

High await and %util values indicate storage bottlenecks.

---

## 10. How do you troubleshoot kernel panic issues?

Steps:

* Check system logs
* Analyze crash dumps
* Verify hardware health
* Review recent kernel updates
* Boot using older kernel if required

Useful logs:

```bash
/var/log/messages
journalctl
```

Kernel panic often relates to hardware failure, driver issues, or kernel bugs.

---

# 🌐 Networking

## 1. Difference between TCP and UDP?

TCP is connection-oriented and reliable.

UDP is connectionless and faster but unreliable.

TCP is used for:

* HTTP
* SSH
* Databases

UDP is used for:

* DNS
* Streaming
* Gaming

---

## 2. Explain TCP 3-way handshake.

TCP connection establishment steps:

1. Client sends SYN
2. Server responds SYN-ACK
3. Client sends ACK

After this, the connection becomes established.

---

## 3. How do you troubleshoot network latency?

Commands:

```bash
ping
traceroute
mtr
```

Steps:

* Verify DNS
* Check packet loss
* Analyze routing path
* Check firewall rules
* Monitor bandwidth usage

---

## 4. How do you check open ports in Linux?

Commands:

```bash
ss -tulnp
netstat -tulnp
```

These show listening ports and associated processes.

---

## 5. Difference between netstat and ss?

`ss` is the modern replacement for `netstat`.

Advantages of `ss`:

* Faster
* Better performance
* More detailed socket information

Most modern Linux systems prefer `ss`.

---

## 6. Explain DNS resolution flow.

DNS resolution steps:

1. Browser cache
2. OS cache
3. Resolver query
4. Recursive DNS server
5. Root server
6. TLD server
7. Authoritative DNS server

Finally, the IP address is returned.

---

## 7. What is NAT and port forwarding?

NAT translates private IP addresses to public IP addresses.

Port forwarding redirects incoming traffic to internal systems.

These are commonly used in routers and cloud networking.

---

## 8. How do you troubleshoot DNS issues?

Commands:

```bash
nslookup
dig
host
```

Steps:

* Verify DNS server
* Check resolution
* Validate network connectivity
* Review firewall settings

---

## 9. What happens when ping works but application is inaccessible?

Possible causes:

* Application service down
* Firewall blocking port
* Incorrect application binding
* Reverse proxy issue
* SSL problems

Ping only verifies ICMP connectivity, not application health.

---

## 10. Difference between public IP and private IP?

Public IP addresses are internet routable.

Private IP addresses are used internally within networks.

Private ranges include:

* 10.0.0.0/8
* 172.16.0.0/12
* 192.168.0.0/16

---

# 🔐 Security & Permissions

## 1. Explain Linux file permissions with examples.

Linux permissions define access levels for:

* Owner
* Group
* Others

Permissions:

* r → read
* w → write
* x → execute

Example:

```bash
-rwxr-xr--
```

---

## 2. Difference between 777, 755, and 644 permissions?

777 → Full access to everyone.

755 → Owner full access, others read/execute.

644 → Owner read/write, others read-only.

777 is insecure and should generally be avoided.

---

## 3. What are SUID, SGID, and sticky bit?

SUID allows execution with file owner's privileges.

SGID allows execution with group privileges.

Sticky bit prevents users from deleting files they do not own.

Common example:

```bash
/tmp directory
```

---

## 4. What is umask?

umask defines default permission masks for newly created files and directories.

Example:

```bash
umask 022
```

This removes write permission for group and others.

---

## 5. How do you secure SSH access?

Security best practices:

* Disable root login
* Use SSH keys
* Change default port
* Disable password authentication
* Enable MFA
* Restrict users using AllowUsers

---

## 6. What is SELinux and AppArmor?

SELinux and AppArmor are Linux security frameworks.

They enforce mandatory access controls and restrict application permissions.

SELinux is common in RHEL/CentOS.

AppArmor is common in Ubuntu.

---

## 7. Difference between SELinux enforcing and permissive mode?

Enforcing mode actively blocks policy violations.

Permissive mode logs violations but does not block them.

Permissive mode is useful during troubleshooting.

---

## 8. How do you check failed login attempts?

Commands:

```bash
lastb
journalctl -u sshd
```

These help identify unauthorized access attempts.

---

## 9. What are common Linux security best practices?

Best practices include:

* Regular patching
* Least privilege access
* Firewall configuration
* SSH hardening
* Monitoring and logging
* MFA implementation
* Disabling unused services

---

## 10. How do you disable password authentication in SSH?

Edit:

```bash
/etc/ssh/sshd_config
```

Set:

```bash
PasswordAuthentication no
```

Then restart SSH service:

```bash
systemctl restart sshd
```

This enforces SSH key-based authentication.
