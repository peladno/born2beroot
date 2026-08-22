# Born2beroot Defense Guide — Simple Answers

## English + 日本語

> **Project context:** Debian + Lighttpd + WordPress bonus + Fail2Ban extra service.
>
> This document is a study guide for the Born2aberoot defense. The Japanese explanations are intentionally simple so they are easier to say during the defense.

---

# 0. Preliminary Checks / 事前確認

## 0.1 Clone the repository

### Command

```bash
git clone <repo_url>
```

### English

I clone the student's Git repository on the evaluation machine.

### 日本語

学生のGitリポジトリを評価するマシンにcloneします。

**Simple answer:**

> リポジトリをcloneして、提出されたファイルを確認します。

---

## 0.2 Check `signature.txt`

### Command

```bash
ls -l
cat signature.txt
```

### English

The `signature.txt` file must exist at the root of the repository.

### 日本語

`signature.txt` はリポジトリのルートに存在する必要があります。

**Simple answer:**

> signature.txtは仮想マシンのディスクのsignatureを確認するために使います。

---

## 0.3 Compare the VM disk signature

The evaluation requires comparing the signature in `signature.txt` with the signature of the VM disk. The subject specifically says that `diff` can be used for this check.

If `signature.txt` contains only the SHA-1 hash, calculate the hash of the `.vdi` first:

```bash
sha1sum /path/to/disk.vdi | awk '{print $1}' > /tmp/vdi_signature.txt
```

Then:

```bash
diff signature.txt /tmp/vdi_signature.txt
```

### English

If `diff` shows nothing, the two signatures are identical.

### 日本語

`diff` に何も表示されなければ、2つのsignatureは同じです。

**Simple answer:**

> signature.txtのsignatureと、現在のVMディスクのsignatureを比較します。同じである必要があります。

---

## 0.4 Snapshots

Check that no snapshots exist.

### English

A snapshot can change the state of the virtual machine and can make the disk used for evaluation different from the original disk.

### 日本語

Snapshotがあると、仮想マシンの状態が変わる可能性があります。そのため、評価ではSnapshotがないことを確認します。

**Simple answer:**

> Snapshotがないことを確認します。

---

# 1. README.md

The README must be at the root of the repository.

The first line must follow this format:

```text
This project has been created as part of the 42 curriculum by <login>
```

It must also contain:

- Project description
- OS choice and pros/cons
- Partitioning
- Security policies
- User management
- Services
- Debian vs Rocky
- AppArmor vs SELinux
- UFW vs Firewalld
- VirtualBox vs UTM

### 日本語

READMEにはプロジェクトの説明、OSの選択、partition、security、user management、servicesなどを書く必要があります。

**Simple answer:**

> READMEにはプロジェクトの説明と、OS、partition、security、user、servicesについて説明しています。

---

# 2. Project Overview / プロジェクト概要

# 2.1 How does a Virtual Machine work?

### English

A virtual machine is a computer created by software. It uses the physical computer's CPU, RAM, storage and network through a virtualization system.

The VM behaves like an independent computer and can have its own operating system.

### 日本語

Virtual Machineは、ソフトウェアで作るコンピューターです。

ホストコンピューターのCPU、RAM、storage、networkなどを使って、独立したコンピューターのように動きます。

**Simple answer:**

> Virtual Machineはソフトウェアで作ったコンピューターです。ホストのCPUやRAMなどを使って、別のOSを動かします。

---

# 2.2 Why did I choose Debian?

### English

I chose Debian because it is stable, lightweight and has a large package repository. It is also well documented and widely used for servers.

### 日本語

Debianを選んだ理由は、stableでlightweightだからです。また、packageが多く、serverとしてよく使われています。

**Simple answer:**

> Debianはstableでlightweightだから選びました。Serverにもよく使われています。

---

# 2.3 Debian vs Rocky Linux

| Debian                 | Rocky Linux         |
| ---------------------- | ------------------- |
| Debian-based           | RHEL-compatible     |
| Uses APT               | Uses DNF            |
| Uses AppArmor commonly | Uses SELinux        |
| Stable and lightweight | Enterprise-oriented |
| `.deb` packages        | `.rpm` packages     |

### 日本語

> DebianはAPTとAppArmorを使います。Rocky LinuxはDNFとSELinuxを使います。

**Simple answer:**

> DebianはAPTを使い、RockyはDNFを使います。また、DebianではAppArmor、RockyではSELinuxが一般的です。

---

# 2.4 What is the purpose of a Virtual Machine?

### English

A VM provides isolation. We can run an operating system without installing it directly on the physical computer. It is useful for testing, development, security and learning.

### 日本語

VMは環境を分離するために使います。物理PCに直接OSをインストールしなくても、別のOSを安全に動かせます。

**Simple answer:**

> VMは環境を分離して、安全に別のOSを動かすために使います。

---

# 2.5 Apt vs Aptitude

### English

Both are package management tools for Debian.

`apt` is the standard command-line interface commonly used to install, remove and update packages.

`aptitude` is another package manager with more advanced dependency handling and an interactive interface.

### 日本語

どちらもDebianのpackage managerです。

`apt`は、install、remove、updateなどで一般的に使います。

`aptitude`は別のpackage managerで、dependencyを扱う機能やinteractive interfaceがあります。

**Simple answer:**

> aptは一般的なpackage管理に使います。aptitudeもpackage managerですが、dependencyの管理やinteractiveな機能があります。

---

# 2.6 What is AppArmor?

### English

AppArmor is a Linux security system. It restricts what programs are allowed to do by using security profiles.

For example, a profile can limit which files a program can read or write.

### 日本語

AppArmorはLinuxのsecurity systemです。

Programごとにprofileを作り、そのprogramができることを制限します。

**Simple answer:**

> AppArmorはprogramの動作をprofileで制限するsecurity systemです。

---

# 2.7 AppArmor vs SELinux

| AppArmor                     | SELinux                           |
| ---------------------------- | --------------------------------- |
| Uses security profiles       | Uses security labels and policies |
| Path-oriented approach       | Label-oriented approach           |
| Common on Debian/Ubuntu      | Common on RHEL/Rocky              |
| Usually easier to understand | More complex and very powerful    |

### 日本語

> AppArmorはprofileを使ってprogramを制限します。SELinuxはlabelとpolicyを使います。

**Simple answer:**

> AppArmorはprofileで制限し、SELinuxはlabelとpolicyで制限します。

---

# 2.8 UFW vs Firewalld

### English

Both are firewall management tools.

UFW is designed to be simple and easy to configure.

Firewalld provides zones and more dynamic firewall management.

My project uses UFW because I chose Debian.

### 日本語

どちらもfirewallを管理するためのtoolです。

UFWはsimpleで使いやすいです。

Firewalldはzoneなどを使って、よりdynamicにfirewallを管理できます。

**Simple answer:**

> UFWはsimpleで使いやすいfirewall toolです。Firewalldはzoneを使って管理できます。私はDebianなのでUFWを使っています。

---

# 2.9 VirtualBox vs UTM

### English

VirtualBox and UTM are virtualization applications.

VirtualBox is a widely used cross-platform virtualization solution.

UTM is commonly used on macOS and can use Apple's virtualization or emulation technologies depending on the configuration.

### 日本語

VirtualBoxとUTMはどちらもvirtualization softwareです。

VirtualBoxは多くのOSで使われています。

UTMは特にmacOSでよく使われ、configurationによってvirtualizationやemulationを使用します。

**Simple answer:**

> どちらもVirtual Machineを動かすsoftwareです。VirtualBoxは多くのOSで使われ、UTMはmacOSでよく使われます。

---

# 3. Simple Setup / 基本設定

## 3.1 No graphical interface

### English

The machine must start in a terminal environment without a graphical desktop.

### 日本語

VMはGUIではなく、terminalで起動する必要があります。

**Simple answer:**

> GUIは使わず、terminalだけで起動します。

---

# 3.2 Login with a non-root user

### English

The evaluator must log in with a normal user, not root.

The password must respect the password policy.

### 日本語

Evaluatorはrootではなくnormal userでloginします。

Passwordは設定したpassword policyに従う必要があります。

**Simple answer:**

> rootではなくnormal userでloginします。Passwordはpolicyに従います。

---

# 3.3 Check Debian

```bash
cat /etc/os-release
```

### Simple answer

> `/etc/os-release`でOSの情報を確認できます。

---

# 3.4 Check UFW

```bash
sudo ufw status
```

### Simple answer

> UFWがactiveかどうかと、firewall ruleを確認します。

---

# 3.5 Check SSH

```bash
sudo systemctl status ssh
```

### Simple answer

> SSH serviceが動いているか確認します。

---

# 4. User Step 1 / User管理

## 4.1 Check my user

```bash
id <login>
```

The user must belong to:

- `sudo`
- `user42`

### 日本語

> 自分のuserがsudoとuser42 groupに入っていることを確認します。

---

# 4.2 Create a new user

```bash
sudo adduser testuser
```

Then:

```bash
sudo passwd testuser
```

### English

I create a new user and assign a password that follows the password policy.

### 日本語

新しいuserを作って、password policyに従ったpasswordを設定します。

**Simple answer:**

> adduserでuserを作って、passwdでpasswordを設定します。

---

# 4.3 Password Policy

## Debian files

The main files are:

```text
/etc/login.defs
/etc/pam.d/common-password
```

### English

`login.defs` contains account and password-related settings.

`common-password` is part of PAM and controls password authentication rules.

### 日本語

`login.defs`にはaccountやpasswordに関する設定があります。

`common-password`はPAMの設定で、passwordのルールを管理します。

**Simple answer:**

> login.defsでpasswordの有効期限などを設定し、PAMのcommon-passwordでpasswordの複雑さなどを設定します。

---

# 4.4 Why is the password policy useful?

### English

The password policy improves security by making passwords harder to guess. It helps reduce the risk of brute-force and weak-password attacks.

### 日本語

Password policyは、弱いpasswordを防ぎ、securityを高めます。

### Advantages

- Stronger passwords
- Better protection against guessing
- Better security

### Disadvantages

- Strong passwords can be harder to remember.
- Users may need to change passwords regularly.
- Strict rules can reduce convenience.

**Simple answer:**

> Password policyはsecurityを高め、weak passwordやguessing attackを防ぎます。ただし、userには少し不便になります。

---

# 5. User Step 2 / Group Management

## 5.1 Create the group

```bash
sudo groupadd evaluating
```

## 5.2 Add the user

```bash
sudo usermod -aG evaluating testuser
```

## 5.3 Verify

```bash
groups testuser
```

### Simple answer

> groupaddでevaluating groupを作ります。そのあとusermodでtestuserをgroupに追加します。最後にgroupsで確認します。

---

# 6. Hostname and Partitions

## 6.1 Check hostname

```bash
hostname
```

The expected format is:

```text
login42
```

### 日本語

> hostnameは自分のloginに42を付けた形式です。

---

# 6.2 Change hostname

Edit:

```bash
sudo nano /etc/hostname
sudo nano /etc/hosts
```

Then reboot:

```bash
sudo reboot
```

### Simple answer

> `/etc/hostname`と`/etc/hosts`を変更して、rebootします。再起動してもhostnameが残っていることを確認します。

---

# 6.3 Show partitions

Useful commands:

```bash
lsblk
df -h
sudo fdisk -l
```

### English

`lsblk` shows block devices and partitions.

`df -h` shows filesystem usage.

`fdisk -l` shows detailed disk and partition information.

### 日本語

> lsblkはdiskとpartitionを表示します。df -hはdisk usageを表示します。fdisk -lはpartitionの詳細を表示します。

---

# 6.4 What is LVM?

### English

LVM means Logical Volume Manager.

The basic structure is:

```text
Physical Volume (PV)
        ↓
Volume Group (VG)
        ↓
Logical Volume (LV)
```

A PV is physical storage.

A VG groups storage together.

An LV is the logical storage volume used by the filesystem.

The advantage is flexible storage management and easier resizing.

### 日本語

LVMはLogical Volume Managerです。

```text
PV → VG → LV
```

PVはphysical storageです。

VGはstorageをまとめたgroupです。

LVはfilesystemで使うlogical volumeです。

LVMのメリットはstorageをflexibleに管理できることです。

**Simple answer:**

> LVMはdisk spaceをflexibleに管理する仕組みです。PV、VG、LVの3つのlayerがあります。

---

# 7. SUDO

## 7.1 Check sudo

```bash
sudo -V
```

## 7.2 Add user to sudo

```bash
sudo usermod -aG sudo testuser
```

---

# 7.3 What is sudo?

### English

`sudo` allows a normal user to execute specific commands with administrator privileges.

It is safer than always logging in as root because access can be controlled and commands can be logged.

### 日本語

sudoはnormal userが必要なときだけadministrator privilegeでcommandを実行するためのtoolです。

rootで常にloginするより安全です。

**Simple answer:**

> sudoはnormal userが必要なときだけroot privilegeでcommandを実行するために使います。

---

# 7.4 Sudo rules

Check:

```bash
sudo nano /etc/sudoers
```

And:

```bash
sudo nano /etc/sudoers.d/<yourfile>
```

### English

The sudo configuration defines who can use sudo and which privileges they have.

### 日本語

sudo configurationでは、誰がsudoを使えるか、どのprivilegeを持つかを設定します。

---

# 7.5 Sudo logging

Check:

```bash
ls -l /var/log/sudo/
```

Then:

```bash
cat /var/log/sudo/<file>
```

Run:

```bash
sudo ls /
```

Then check the log again.

### Simple answer

> sudo commandを実行すると、commandのhistoryがsudo logに記録されます。

---

# 8. UFW / Firewall

## 8.1 What is UFW?

### English

UFW means Uncomplicated Firewall.

It is a simple interface for managing Linux firewall rules.

### 日本語

UFWはUncomplicated Firewallの略です。

Linuxのfirewall ruleをsimpleに管理するためのtoolです。

**Simple answer:**

> UFWはLinuxのfirewallを簡単に管理するtoolです。

---

# 8.2 Check UFW

```bash
sudo ufw status
```

List rules:

```bash
sudo ufw status numbered
```

There must be a rule for port `4242`.

---

# 8.3 Add port 8080

```bash
sudo ufw allow 8080
```

Check:

```bash
sudo ufw status numbered
```

---

# 8.4 Delete port 8080

```bash
sudo ufw delete allow 8080
```

### Simple answer

> まず8080をallowして、ruleを確認します。そのあと8080のruleをdeleteします。

---

# 9. SSH

## 9.1 What is SSH?

### English

SSH means Secure Shell.

It provides encrypted remote access to another computer.

### 日本語

SSHはSecure Shellの略です。

Networkを使って、encrypted connectionでremote computerにloginできます。

**Simple answer:**

> SSHはencrypted connectionを使ってremote computerにloginするためのprotocolです。

---

# 9.2 Check SSH

```bash
sudo systemctl status ssh
```

---

# 9.3 SSH port

Check:

```bash
sudo nano /etc/ssh/sshd_config
```

The SSH service must use port:

```text
Port 4242
```

Root login must be disabled:

```text
PermitRootLogin no
```

### Simple answer

> SSHはport 4242を使います。Root loginは禁止しています。

---

# 9.4 Test SSH

From the host:

```bash
ssh testuser@<VM_IP> -p 4242
```

### Japanese answer

> testuserでSSH loginします。portは4242です。rootではloginできません。

---

# 10. Monitoring Script

## 10.1 Show the script

```bash
cat /usr/local/bin/monitoring.sh
```

### English

The monitoring script collects information about the system and displays it periodically.

The exact commands depend on the implementation of my script.

### 日本語

Monitoring scriptはsystemの情報を取得して、定期的に表示します。

**Simple answer:**

> Monitoring scriptはsystemの情報を取得して、定期的に表示します。

---

# 10.2 What is cron?

### English

Cron is a scheduler in Linux. It runs commands automatically at specified times.

### 日本語

CronはLinuxのschedulerです。

決められた時間にcommandやscriptを自動で実行します。

**Simple answer:**

> Cronは決められた時間にscriptを自動実行するschedulerです。

---

# 10.3 Run every 10 minutes

Check root's crontab:

```bash
sudo crontab -u root -l
```

Expected:

```cron
*/10 * * * * /usr/local/bin/monitoring.sh
```

### Explanation

```text
*/10  = every 10 minutes
*     = every hour
*     = every day
*     = every month
*     = every day of the week
```

### 日本語

> `*/10`は10分ごとという意味です。

**Simple answer:**

> rootのcronからmonitoring scriptを10分ごとに実行しています。

---

# 10.4 Change to every minute

Edit:

```bash
sudo crontab -u root -e
```

Use:

```cron
* * * * * /usr/local/bin/monitoring.sh
```

### Simple answer

> Defenseではscriptを1分ごとに実行するように変更します。

---

# 10.5 Stop the script after startup without modifying it

The evaluator asks you to make the script stop running when the server starts, without changing the script itself.

One simple method is to disable or comment out the cron entry.

For example:

```bash
sudo crontab -u root -e
```

Then comment the entry:

```cron
# * * * * * /usr/local/bin/monitoring.sh
```

### Important

Do not modify the script itself.

After reboot, verify:

```bash
ls -l /usr/local/bin/monitoring.sh
```

The script must still:

- Exist
- Have the same permissions
- Have the same contents
- Not run automatically

### 日本語

> Script自体は変更しません。Cronの設定をcomment outして、scriptが自動実行されないようにします。

---

# 11. Bonus / ボーナス

The bonus is evaluated **only after the mandatory part is completely and perfectly finished**.

For this project, the relevant bonus items include:

- Additional partitions
- WordPress
- One free-choice service

NGINX and Apache2 are prohibited for the free-choice service according to the evaluation document.

---

# 11.1 WordPress Bonus

### English

I installed WordPress using the services required by the project.

My web server is Lighttpd because NGINX and Apache2 are not allowed.

### 日本語

WordPressをbonusとして設定しました。

Web serverにはLighttpdを使っています。NGINXとApache2は使用していません。

**Simple answer:**

> BonusでWordPressを設定しました。Web serverにはLighttpdを使っています。

---

# 11.2 Free Choice Service — Fail2Ban

### English

I chose Fail2Ban as my extra service.

Fail2Ban monitors log files and can temporarily ban IP addresses that show suspicious behavior, such as repeated failed authentication attempts.

### 日本語

Extra serviceとしてFail2Banを使っています。

Fail2Banはlog fileをmonitorして、SSHなどでfailed authenticationが何度も発生したIP addressを一時的にbanできます。

**Simple answer:**

> Fail2Banはlogをmonitorして、failed loginが何度も発生したIPをtemporaryにbanするsecurity toolです。

### Why is it useful?

### English

It helps protect the server against repeated automated login attempts and brute-force attacks.

### 日本語

> Brute-force attackからserverを守るために役立ちます。

---

# 12. Questions You Should Be Ready For

## Q: Why Debian?

### Answer

> Debian is stable, lightweight and well documented. It is also good for a server environment.

### 日本語

> Debianはstableでlightweightです。Documentationも多く、server environmentに向いていると思ったので選びました。

---

## Q: What is a VM?

### Answer

> A VM is a computer created by software. It uses resources from the host machine.

### 日本語

> VMはsoftwareで作ったcomputerです。Host machineのresourceを使って動きます。

---

## Q: What is LVM?

### Answer

> LVM is a system for managing storage flexibly using PV, VG and LV.

### 日本語

> LVMはPV、VG、LVを使ってstorageをflexibleに管理するsystemです。

---

## Q: What is sudo?

### Answer

> Sudo allows a normal user to execute commands with administrator privileges.

### 日本語

> sudoはnormal userがadministrator privilegeでcommandを実行するために使います。

---

## Q: What is UFW?

### Answer

> UFW is a simple tool for managing firewall rules.

### 日本語

> UFWはfirewall ruleを簡単に管理するtoolです。

---

## Q: Why port 4242?

### Answer

> The project requires SSH to use port 4242 instead of the default SSH port.

### 日本語

> ProjectのrequirementでSSHにport 4242を使う必要があるからです。

---

## Q: What is SSH?

### Answer

> SSH is a protocol for secure and encrypted remote access.

### 日本語

> SSHはsecureでencryptedなremote accessのためのprotocolです。

---

## Q: Why disable root SSH login?

### Answer

> It reduces the risk of direct root access. A normal user can log in and use sudo when necessary.

### 日本語

> Rootで直接loginするriskを減らすためです。Normal userでloginして、必要なときsudoを使います。

---

## Q: What is AppArmor?

### Answer

> AppArmor restricts what programs can do using security profiles.

### 日本語

> AppArmorはsecurity profileを使ってprogramの動作を制限します。

---

## Q: AppArmor vs SELinux?

### Answer

> AppArmor mainly uses profiles, while SELinux uses labels and security policies.

### 日本語

> AppArmorは主にprofileを使います。SELinuxはlabelとsecurity policyを使います。

---

## Q: UFW vs Firewalld?

### Answer

> Both manage firewall rules. UFW is simpler, while Firewalld provides features such as zones and dynamic management.

### 日本語

> どちらもfirewallを管理します。UFWはsimpleで、Firewalldはzoneなどの機能があります。

---

## Q: apt vs aptitude?

### Answer

> Both manage Debian packages. Apt is the common command-line tool, while aptitude provides additional dependency handling and an interactive interface.

### 日本語

> どちらもDebianのpackage managerです。aptは一般的なcommand-line toolで、aptitudeにはdependency管理やinteractive interfaceがあります。

---

## Q: What is cron?

### Answer

> Cron is a scheduler that automatically runs commands at specified times.

### 日本語

> Cronは決められた時間にcommandを自動実行するschedulerです。

---

## Q: What does your monitoring script do?

### Answer

> It collects system information and displays it periodically. I configured cron to execute it every 10 minutes.

### 日本語

> Systemのinformationを取得して、定期的に表示します。Cronで10分ごとに実行しています。

---

## Q: Why Fail2Ban?

### Answer

> I chose Fail2Ban because it provides an additional layer of security against repeated failed authentication attempts.

### 日本語

> Failed loginを繰り返すIPをbanできるので、securityを強化するために選びました。

---

# 13. Useful Commands / よく使うコマンド

## System

```bash
cat /etc/os-release
hostname
uname -a
```

## Users

```bash
id <login>
groups <login>
cat /etc/passwd
```

## Groups

```bash
groups testuser
getent group evaluating
```

## Partitions

```bash
lsblk
df -h
sudo fdisk -l
```

## LVM

```bash
sudo pvs
sudo vgs
sudo lvs
```

## Sudo

```bash
sudo -V
sudo ls /
ls -l /var/log/sudo/
```

## Firewall

```bash
sudo ufw status
sudo ufw status numbered
```

## SSH

```bash
sudo systemctl status ssh
sudo ss -tlnp
```

## Cron

```bash
sudo crontab -u root -l
sudo crontab -u root -e
```

## Monitoring script

```bash
cat /usr/local/bin/monitoring.sh
ls -l /usr/local/bin/monitoring.sh
```

## Fail2Ban

```bash
sudo systemctl status fail2ban
sudo fail2ban-client status
```

---

# 14. Defense Strategy / Defenseでの話し方

Do not try to give a very complicated explanation.

Use this structure:

```text
What is it?
↓
Why do I use it?
↓
How did I configure it?
↓
Show the command/configuration
```

### Example — UFW

> UFW is a firewall management tool.
>
> I use it to control network access.
>
> I configured port 4242 for SSH.
>
> I can show the rules with `sudo ufw status numbered`.

### 日本語

> UFWはfirewallを管理するtoolです。
>
> Network accessをcontrolするために使います。
>
> SSH用にport 4242を設定しています。
>
> `sudo ufw status numbered`でruleを確認できます。

---

# 15. Important Defense Checklist

Before the defense, make sure you can explain all of these without reading a long explanation:

- [ ] Virtual Machine
- [ ] Debian
- [ ] Debian vs Rocky
- [ ] apt vs aptitude
- [ ] AppArmor
- [ ] AppArmor vs SELinux
- [ ] UFW
- [ ] UFW vs Firewalld
- [ ] VirtualBox vs UTM
- [ ] Password policy
- [ ] PAM
- [ ] Users
- [ ] Groups
- [ ] sudo
- [ ] sudo logging
- [ ] Hostname
- [ ] Partitions
- [ ] LVM
- [ ] SSH
- [ ] Port 4242
- [ ] Why root SSH is disabled
- [ ] Monitoring script
- [ ] Cron
- [ ] Why cron runs every 10 minutes
- [ ] How to change it to every minute
- [ ] How to stop it without modifying the script
- [ ] WordPress
- [ ] Lighttpd
- [ ] Fail2Ban
- [ ] Why Fail2Ban is useful

---

# 16. Final Short Japanese Summary

If you forget something during the defense, keep the explanation simple.

### Debian

> DebianはstableでlightweightなLinux distributionです。

### VM

> VMはsoftwareで作ったvirtual computerです。

### LVM

> LVMはstorageをflexibleに管理するsystemです。

### sudo

> sudoはnormal userが必要なときadministrator privilegeを使うためのtoolです。

### UFW

> UFWはfirewallを簡単に管理するtoolです。

### SSH

> SSHはencrypted connectionでremote loginするprotocolです。

### AppArmor

> AppArmorはprofileを使ってprogramの動作を制限します。

### Cron

> Cronはcommandやscriptを決められた時間に自動実行するschedulerです。

### Monitoring

> Monitoring scriptはsystem informationを取得して定期的に表示します。

### Fail2Ban

> Fail2Banはfailed loginなどをmonitorして、怪しいIPをtemporaryにbanします。

### WordPress

> WordPressをbonusとして設定しました。Web serverにはLighttpdを使っています。

### Security

> このprojectではfirewall、SSH、sudo、password policy、Fail2Banなどを使ってsecurityを高めています。
