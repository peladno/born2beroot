# Born2beRoot

_This project has been created as part of the 42 curriculum by javier._

## Description

Born2beRoot is a system administration project from the 42 curriculum. The goal of the project is to introduce virtualization and the fundamentals of system administration by creating and configuring a secure Linux server inside a virtual machine.

For this project, I chose **Debian** as the operating system and configured the server according to the mandatory requirements of the subject.

The project focuses on:

- Virtualization with VirtualBox
- Linux system administration
- User and group management
- SSH configuration
- Firewall configuration
- Password policies
- Sudo configuration
- LVM and encrypted partitions
- AppArmor
- System monitoring
- Cron jobs
- Server security

For the bonus part, I additionally configured a complete WordPress website using:

- Lighttpd
- MariaDB
- PHP

As an additional service, I installed and configured **Fail2ban** to help protect the server against repeated authentication attempts.

---

## Operating System Choice

I chose **Debian** as the operating system for this project.

### Why Debian?

Debian is a stable and lightweight Linux distribution that is well suited for servers. It provides a large software repository, strong community support, and a relatively simple package management system.

Debian was also recommended by the subject for students who are new to system administration.

### Advantages

- Stable and reliable
- Large software repositories
- Extensive documentation and community support
- Lightweight
- Good server-oriented ecosystem
- Uses AppArmor for mandatory access control

### Disadvantages

- Packages can be older than those found in some other distributions
- Some software may require additional configuration
- Stable releases prioritize reliability over having the newest versions

---

# System Architecture

The server is running inside a VirtualBox virtual machine.

```bash
lsblk             ## to check disk layout
```

The main components are:

```text
                    Virtual Machine
                         │
                    Debian Linux
                         │
        ┌────────────────┼────────────────┐
        │                │                │
       SSH              UFW            AppArmor
      :4242            Firewall         Security
        │                │
        └────────────┬───┘
                     │
                   Users
                     │
                    Sudo

Bonus:

       HTTP
        │
        ▼
    Lighttpd
        │
        ├──────── PHP
        │
        └──────── MariaDB
                    │
                    ▼
                WordPress

Additional service:

      Fail2ban
```

---

# Installation and Configuration

## Virtual Machine

The server was created using **VirtualBox**.

The virtual machine runs Debian without a graphical environment, as required by the project.

No graphical server such as X.org or Wayland was installed.

---

## Partitioning and LVM

The system uses encrypted partitions with LVM.

LVM allows logical volumes to be managed independently from the physical disk layout.

The encrypted setup provides an additional layer of protection for data stored on the virtual disk.

The partition layout follows the structure required by the Born2beRoot subject.

---

# User Management

A user corresponding to my 42 login was created.

The user belongs to:

- `user42`
- `sudo`

The root account is also available but direct SSH access as root is disabled.

During the evaluation, additional users can be created and assigned to groups.

---

# Password Policy

A strong password policy was configured.

The policy includes:

- Password expiration every 30 days
- Minimum 2 days between password changes
- Warning 7 days before expiration
- Minimum password length of 10 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- No more than 3 consecutive identical characters
- Password cannot contain the username
- Previous password requirements are enforced where applicable

These settings are configured using Linux password and PAM-related configuration.

---

# Sudo Configuration

Sudo was configured according to the security requirements of the project.

The configuration includes:

- Maximum of 3 authentication attempts
- Custom message when an incorrect password is entered
- Logging of sudo commands
- Logging stored under `/var/log/sudo/`
- TTY mode enabled
- Restricted secure paths

Sudo allows the regular user to perform administrative operations without using the root account directly.

---

# SSH

SSH is configured to listen on port:

```text
4242
```

The root user is not allowed to connect directly through SSH.

Example configuration:

```text
Port 4242
PermitRootLogin no
```

The SSH service allows remote administration of the Debian server while reducing the attack surface compared to allowing direct root access.

---

# UFW Firewall

UFW (Uncomplicated Firewall) is used to manage the firewall.

The firewall is enabled when the system starts.

For the mandatory part, SSH access is allowed through port:

```text
4242
```

Example:

```bash
sudo ufw allow 4242
sudo ufw enable
```

For the WordPress bonus, additional ports are opened when necessary.

The firewall rules must always correspond to the services actually running on the server.

---

# AppArmor

Debian uses **AppArmor** as its mandatory access control system.

AppArmor restricts what applications are allowed to access by applying security profiles.

It provides an additional security layer on top of traditional Linux permissions.

AppArmor is enabled at system startup.

---

# Monitoring Script

A Bash script called:

```text
monitoring.sh
```

was created to monitor the server.

The script displays information such as:

- Operating system architecture
- Kernel version
- Number of physical CPUs
- Number of virtual CPUs
- RAM usage
- Disk usage
- CPU usage
- Last reboot
- LVM status
- Number of active TCP connections
- Number of logged-in users
- Server IPv4 address
- MAC address
- Number of sudo commands

The script is executed periodically using `cron`.

The `wall` command is used to display the monitoring information on the server terminals.

---

# Bonus

## WordPress

For the bonus part, I configured a functional WordPress website.

The WordPress installation uses:

```text
WordPress
   │
   ├── Lighttpd
   │
   ├── PHP
   │
   └── MariaDB
```

### Lighttpd

Lighttpd is used as the web server.

It was chosen instead of Apache or NGINX because the Born2beRoot subject specifically excludes NGINX and Apache2 for the additional service requirement.

Lighttpd is lightweight and well suited for a small server environment.

### PHP

PHP is used to execute WordPress server-side code.

Lighttpd was configured to work with PHP through CGI.

### MariaDB

MariaDB is used as the database server for WordPress.

A dedicated database was created for the WordPress installation.

WordPress stores its posts, pages, users, settings and other data in MariaDB.

### WordPress

WordPress provides the web application running on top of the Lighttpd, PHP and MariaDB stack.

The installation was configured and tested locally from the virtual machine.

---

# Additional Service: Fail2ban

As an additional service, I installed **Fail2ban**.

Fail2ban monitors log files and can temporarily ban IP addresses that repeatedly perform suspicious or failed authentication attempts.

For example, it can protect SSH against repeated failed login attempts.

The general mechanism is:

```text
Failed authentication
        │
        ▼
      Logs
        │
        ▼
    Fail2ban
        │
        ▼
 Detect repeated attempts
        │
        ▼
     IP banned
```

Fail2ban provides an additional security layer on top of the firewall and SSH configuration.

I chose Fail2ban because protecting authentication services is particularly useful on a server exposed to network connections.

---

# Debian vs Rocky Linux

| Debian                                   | Rocky Linux                        |
| ---------------------------------------- | ---------------------------------- |
| Debian-based distribution                | RHEL-compatible distribution       |
| Uses APT                                 | Uses DNF                           |
| Uses AppArmor                            | Uses SELinux                       |
| Large community                          | Enterprise-oriented ecosystem      |
| Stable and lightweight                   | Strong enterprise compatibility    |
| Recommended by the subject for beginners | More complex initial configuration |

I chose Debian because it provides a simpler environment for learning the fundamentals of Linux system administration while still providing the tools required by the project.

---

# AppArmor vs SELinux

Both AppArmor and SELinux provide mandatory access control.

### AppArmor

AppArmor uses profiles to restrict applications based mainly on their paths and allowed operations.

Advantages:

- Easier to understand
- Easier to configure for beginners
- Profile-based configuration

### SELinux

SELinux uses security labels and a policy-based model to control access.

Advantages:

- Very powerful access-control system
- Widely used in enterprise Linux environments
- Fine-grained security policies

For this project, Debian uses AppArmor.

---

# UFW vs firewalld

### UFW

UFW stands for **Uncomplicated Firewall**.

It provides a simple interface for configuring firewall rules.

Example:

```bash
sudo ufw allow 4242
```

### firewalld

firewalld provides dynamic firewall management and uses concepts such as zones and services.

It is commonly used on Red Hat-based distributions.

For Debian, I chose UFW as required by the project.

---

# VirtualBox vs UTM

### VirtualBox

VirtualBox is a cross-platform virtualization solution that supports many operating systems and provides a graphical interface for managing virtual machines.

### UTM

UTM is a virtualization and emulation application particularly useful on macOS and Apple Silicon systems.

For this project, I used VirtualBox because it provides the virtualization environment required by the project and was available on my system.

---

# Instructions

## Start the Virtual Machine

Start the Debian virtual machine using VirtualBox.

After booting, log in using the configured user.

---

## Check SSH

SSH should be running on port `4242`.

```bash
sudo systemctl status ssh
```

Check the listening port:

```bash
sudo ss -lntp | grep 4242
```

---

## Check Firewall

```bash
sudo ufw status
```

The firewall should be active and configured with the required ports.

---

## Check AppArmor

```bash
sudo aa-status
```

---

## Check Lighttpd

For the WordPress bonus:

```bash
sudo systemctl status lighttpd
```

---

## Check MariaDB

```bash
sudo systemctl status mariadb
```

---

## Check PHP

```bash
php -v
```

---

## Check Fail2ban

```bash
sudo systemctl status fail2ban
```

To check its jails:

```bash
sudo fail2ban-client status
```

For SSH:

```bash
sudo fail2ban-client status sshd
```

---

# Useful Commands

### Check listening ports

```bash
sudo ss -lntp
```

### Check running services

```bash
systemctl --type=service --state=running
```

### Check IP address

```bash
hostname -I
```

### Check disk usage

```bash
df -h
```

### Check LVM

```bash
lsblk
sudo vgs
sudo lvs
```

### Check users

```bash
getent passwd
```

### Check groups

```bash
getent group
```

## Create user and add to group

```bash
sudo adduser <your_login>
sudo groupadd <group name>
sudo usermod -aG sudo <your_login>
sudo usermod -aG user42 <your_login>

## sudo whoami -> to check if is root
```

## Verify Group Membership

```bash
groups <your_login>

getent group sudo
getent group user42
```

---

# Resources

## Official Documentation

- Debian Documentation
- Linux manual pages
- UFW documentation
- AppArmor documentation
- OpenSSH documentation
- MariaDB documentation
- PHP documentation
- Lighttpd documentation
- WordPress documentation
- Fail2ban documentation
- VirtualBox documentation

## Topics Studied

During this project I studied and practiced:

- Linux system administration
- Virtual machines
- Debian
- LVM
- Disk encryption
- SSH
- UFW
- AppArmor
- PAM
- Sudo
- Cron
- Bash scripting
- Lighttpd
- PHP
- MariaDB
- WordPress
- Fail2ban

---

# AI Usage

AI tools were used as a learning and troubleshooting aid during the project.

They were used mainly for:

- Understanding Linux and system administration concepts
- Clarifying configuration files and commands
- Troubleshooting configuration issues
- Understanding Lighttpd, PHP, MariaDB and WordPress integration
- Reviewing Bash commands and scripts
- Understanding firewall and security configurations
- Debugging problems encountered during the setup

AI was not used as a replacement for understanding the project. All configurations and commands used in the final system were tested and verified on the virtual machine.

---

# Conclusion

Born2beRoot provided practical experience with Linux system administration, virtualization and server security.

The project involved configuring a secure Debian server from the ground up, including users, permissions, SSH, firewall rules, password policies, sudo, AppArmor, LVM and system monitoring.

The bonus part extended the server with a complete WordPress stack using Lighttpd, PHP and MariaDB.

Finally, Fail2ban was added as an additional security service to protect authentication services against repeated failed login attempts.
