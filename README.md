# Born2beRoot

## Description

**Born2beRoot** is a system administration project from the École 42 curriculum, aimed at introducing students to virtualization and server management. Through this project, students learn to set up a virtual machine using VirtualBox or UTM, configure an operating system with security measures, and implement essential services with strict requirements.

---

## Features

### Mandatory Part

- **Virtual Machine Setup**: Use VirtualBox (or UTM if VirtualBox is unavailable) to create a virtual machine with either Debian or Rocky Linux as the OS. Debian is recommended for beginners in system administration.

- **Partitioning**: Create at least two encrypted partitions using Logical Volume Manager (LVM).

- **Firewall Configuration**: Configure UFW (Debian) or firewalld (Rocky) to allow only SSH on port 4242.

- **SSH Configuration**: Ensure SSH service is running on port 4242, with root login disabled for security. SSH connections will be tested during the defense.

- **User and Group Setup**:
  - Create a user with your login as the username, and add them to the `sudo` and `user42` groups.
  - Implement a strong password policy:
    - Password expires every 30 days.
    - Minimum 10 characters with upper/lowercase letters and numbers.
    - Warn users 7 days before password expiry.

- **Sudo Configuration**:
  - Limit sudo authentication attempts to 3.
  - Set a custom error message for incorrect password attempts.
  - Log all sudo commands to `/var/log/sudo/`.
  - Enable TTY mode for security, and restrict paths used by sudo.

- **Monitoring Script**: Develop a `monitoring.sh` script in bash that displays system information every 10 minutes. The output should include:
  - System architecture and kernel version.
  - Physical and virtual CPU count.
  - RAM and disk usage.
  - CPU load.
  - Last boot date.
  - LVM status.
  - Active TCP connections.
  - User login count.
  - Server IP and MAC address.
  - Number of sudo commands executed.

### Bonus Part (Optional)

Additional optional features include:

- **Partition Setup**: Create a specific partition structure as outlined in the project.
- **WordPress Setup**: Set up a WordPress site with Lighttpd, MariaDB, and PHP.
- **Custom Service**: Install a useful service of your choice (excluding NGINX/Apache2). The firewall configuration must be adjusted to accommodate the new service.

> **Note**: The bonus part will only be evaluated if the mandatory part is fully completed and functional.

---

## Submission

Submit a `signature.txt` file in the root of your Git repository containing the SHA-1 signature of your virtual machine's disk image. The signature will be verified against your virtual machine during the defense.

---

## Author

Project developed by [Malik](https://github.com/ma1iik) as part of the École 42 curriculum.
