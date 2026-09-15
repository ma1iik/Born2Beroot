# Born2Beroot (Linux Systems Administration)

A systems administration project establishing a hardened Debian Linux server environment in VirtualBox. Implements LVM storage partitioning, SSH hardening, UFW firewall rules, password security policies, sudo logging, and automated system monitoring scripts.

![OS](https://img.shields.io/badge/OS-Debian%20Linux-red?logo=debian&logoColor=white)
![Security](https://img.shields.io/badge/Security-PAM%20%7C%20UFW%20%7C%20Sudo-blue)
![Monitoring](https://img.shields.io/badge/Scripting-Bash%20%7C%20Cron-green)
![42 Score](https://img.shields.io/badge/42_Score-125%2F100-success)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Overview

`Born2Beroot` introduces system administration and server security primitives. It involves configuring a Virtual Machine without a graphical user interface (GUI), setting up strict security protocols, user authorization groups, encrypted LVM storage, and automated monitoring.

---

## System Architecture & Specifications

### 1. Storage & Partitioning (LVM)
Configured using Logical Volume Manager (LVM) over an encrypted volume:

```
/dev/sda
├── sda1 -> /boot (ext4, ~500MB)
└── sda2 -> Encrypted LVM Volume Group (LVM2)
    ├── root -> / (ext4, ~10GB)
    ├── swap -> [SWAP] (~2GB)
    ├── home -> /home (ext4, ~5GB)
    ├── var  -> /var (ext4, ~3GB)
    ├── tmp  -> /tmp (ext4, ~3GB)
    └── srv  -> /srv (ext4, ~3GB)
```

---

### 2. Network & SSH Hardening
- **SSH Port**: Changed from default port 22 to port `4242`.
- **Root Protection**: `PermitRootLogin no` enforced in `/etc/ssh/sshd_config`.
- **UFW Firewall**:
  ```bash
  sudo ufw default deny incoming
  sudo ufw allow 4242/tcp
  sudo ufw enable
  ```

---

### 3. Password Security Policy (`libpam-pwquality`)
Configured in `/etc/pam.d/common-password` and `/etc/login.defs`:
- **Length**: Minimum 10 characters.
- **Complexity**: Must contain at least 1 uppercase letter, 1 lowercase letter, and 1 number.
- **Duplicates**: Maximum 3 consecutive identical characters.
- **Username Check**: Cannot contain the user's login name.
- **Expiration**: Max password age of 30 days, min change delay of 2 days, expiration warning 7 days prior.

---

### 4. Sudo Security Configuration (`/etc/sudoers`)
Strict sudo policy enforced in `/etc/sudoers.d/sudo_config`:
- **Authentication**: Limited to 3 failed attempts per command.
- **Custom Error Message**: Custom message on incorrect password entry.
- **TTY Enforcement**: `Defaults requiretty` (sudo only callable from real TTY).
- **Log Archiving**: All sudo invocations logged to `/var/log/sudo/sudo.log`.

---

## System Monitoring Script (`monitoring.sh`)

A Bash script scheduled via `cron` (`*/10 * * * *`) that broadcasts system health to all open terminals via `wall`:

```bash
#!/bin/bash

# Architecture & Kernel Version
arch=$(uname -a)

# Physical & Virtual Processors
cpup=$(grep "physical id" /proc/cpuinfo | sort -u | wc -l)
cpuv=$(grep "processor" /proc/cpuinfo | wc -l)

# RAM Usage
ram_total=$(free -m | awk '$1 == "Mem:" {print $2}')
ram_used=$(free -m | awk '$1 == "Mem:" {print $3}')
ram_percent=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')

# Disk Space Usage
disk_total=$(df -Bg | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}')
disk_used=$(df -Bm | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}')
disk_percent=$(df -Bm | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} {ut += $3} END {printf("%d"), ut/ft*100}')

# CPU Utilization
cpul=$(top -bn1 | grep "^%Cpu" | cut -c 9- | awk '{printf("%.1f%%"), $1 + $3}')

# System Uptime & Active Connections
lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')
lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi)
tcpc=$(ss -ta | grep ESTAB | wc -l)
userlog=$(users | wc -w)
ip=$(hostname -I)
mac=$(ip link show | grep "link/ether" | awk '{print $2}')
cmnd=$(journalctl _COMM=sudo | grep COMMAND | wc -l)

wall "	#Architecture: $arch
	#CPU physical : $cpup
	#vCPU : $cpuv
	#Memory Usage: $ram_used/${ram_total}MB ($ram_percent%)
	#Disk Usage: $disk_used/${disk_total}Gb ($disk_percent%)
	#CPU load: $cpul
	#Last boot: $lb
	#LVM use: $lvmu
	#Connections TCP : $tcpc ESTABLISHED
	#User log: $userlog
	#Network: IP $ip ($mac)
	#Sudo : $cmnd cmd"
```

---

## Verification Commands

### Check LVM Partition Layout
```bash
lsblk
```

### Check UFW Status
```bash
sudo ufw status numbered
```

### Check SSH Service Status
```bash
sudo systemctl status ssh
```

### Check Password Expiry Policy
```bash
chage -l <username>
```

### View Sudo Audit Logs
```bash
sudo cat /var/log/sudo/sudo.log
```

---

## License

Distributed under the MIT License. See `LICENSE` for details.
