---
title: Linux - Setup
tags: 
 - linux
description: Setting up your Linux server
---

# Linux - Setting up your server
<br>

While an application developer's dream is to merge changes into production and have their features immediately implemented, an SRE's goal is to ensure the application continues to function at normal capacity after every upgrade and deployment. Apart from internal updates & bug fixes which might potentially break services, our application is susceptible to external attacks as well. Here are some <a href="https://hostingtribunal.com/blog/hacking-statistics/#gref" target="_blank">statistics</a>:

- There is a hacker attack **every 39 seconds**
- **300,000 new malware** are created every day
- **Multi-factor authentication and encryption** are the biggest hacker obstacles
- The average cost of data breaches will be about **$150 million in 2020**. 


<br>

To ensure an application stays healthy through thick and thin, we should configure our server securely. By checking the following steps off on new servers, you can ensure they have at least basic protection against the most common attacks. 

<br>

## Steps
1. User configuration - Protect your credentials
2. Network configuration - Establish communications
3. Package management - Add what you need, remove what you don't
4. Update installation - Patch your vulnerabilities
5. NTP configuration - Prevent clock drift
6. Firewalls and iptables - Minimize your external footprint
7. Securing SSH - Harden remote sessions
8. Daemon configuration - Minimize your attack surface
9. SELinux and further hardening - Protect the kernel and applications
10. Logging - Knowing what's happening

<br><br>

---

## i) User configuration 
<br>

The very first thing you're going to want to do is change the root password. The password should be at least 8 characters, using a combination of upper and lower case letters, numbers and symbols. You should also set a password policy that specifies aging, locking, history and complexity requirements. In most cases, you should disable the root user entirely and create non-privileged user accounts with sudo access for those that require elevated rights. 

<br>

<ins>Change root password</ins>

To change the password of the `root` account, run the following command and enter the default password, you'll be prompted to re-enter the new password:

```bash
sudo passwd root
```

<br>

<ins>Setup password policy - Aging, locking, history, complexity requirements</ins>

a. <a href="https://www.networkworld.com/article/2726217/how-to-enforce-password-complexity-on-linux.html" target="_blank">Change password complexity</a> to require min length `12`, a digit, an uppercase letter, a lowercase letter and a special character. Passwords cannot be reused and `3` old passwords will be remembered:

Debian: **/etc/pam.d/common-password**

RedHat: **/etc/security/pwquality.conf**

- At the end of the first uncommented line, add `minlen=12 dcredit=-1 ucredit=-1 lcredit=-1 ocredit=-1 remember=3`, one space after `sha256`.
- Save the file

<br><br>

b. Change maximum password age to 180 days and the minimum password age to 3 days:

- Run the command `sudo vi /etc/login.defs`
- Replace `PASS_MAX_DAYS 99999` with `PASS_MAX_DAYS 180`
- On the next line replace `0` with `3`
- Save the file

<br><br>

c. Configure accounts to <a href="https://www.linuxtechi.com/lock-user-account-incorrect-login-attempts-linux/" target="_blank">lockout</a> after 3 failed attempts and remain locked out for 10 minutes:

Debian:
- Run the command `sudo vi /etc/pam.d/common-auth`
- Add a line above the first uncommented line with the following code `auth required pam_tally2.so onerr=fail deny=3 unlock_time=600 audit`

RedHat:
- Add the following lines in two files `/etc/pam.d/password-auth` & `/etc/pam.d/system-auth`:
```bash
auth     required       pam_faillock.so preauth silent audit deny=3 unlock_time=600
auth     [default=die]  pam_faillock.so authfail audit deny=3 unlock_time=600
account  required       pam_faillock.so
```


After making any changes, restart the `sshd` service to apply changes:
```bash
sudo systemctl restart sshd
```


<br>

<ins>Create non-privileged user with sudo access</ins>



To lock the root account password, use the following syntax:
```bash
sudo passwd -l root
```

<br><br>

## ii) Network configuration
<br>

One of the most basic configurations you'll need to make is to enable network connectivity by assigning the server an IP address and hostname. For most servers you'll want a static IP so your clients can always find the resource at the same address. If you don't user IPv6, turn it off. Set the hostname, domain and DNS server information. 

- Enable network connectivity by assigning ip address, hostname and DNS server information
- Turn IPv6 off if unused

<br><br>

## iii) Package management
<br>

Install whatever packages you might need if they aren't part of the distribution you're using. These could be application packages like PHP, MongoDB, nginx or supporting packages like pear. Likewise, any extraneous packages that can be removed should be removed. All of this should be done through your distribution's package management solution, such as yum or apt for easier management down the road. 


- Install packages required by application
- Remove packages not required by application

<br><br>

## iv) Update installation and configuration
<br>

Once you have the right packages installed, you should make sure everything is updated. Not just the packages you installed, but the kernel and default packages as well. Unless you have a requirement for a specific version, you should always use the latest production release to keep your system secure. Usually your package management solution will deliver the newest supported version. You should also consider setting up automatic updates within the package management tool. 

- Update packages (Kernel and default packages)
- Set up automatic updates

<br><br>

## v) NTP configuration
<br>

Configure your server to sync its time to NTP servers. These could be internal NTP servers, or <a href="https://tf.nist.gov/tf-cgi/servers.cgi" target="_blank">external time servers</a> that are available to anyone. What's important is to prevent clock drift where the server's clock skews from the actual time. This can cause a lot of problems, including authentication issues where time skew between the server and authenticating infrastructure is measured before granting access. A simple tweak, albeit a critical bit of reliable infrastructure. 

<br><br>

## vi) Firewalls and iptables 
<br>








<br><br>

---

## Acknowledgements

- <a href="https://www.upguard.com/blog/10-essential-steps-for-configuring-a-new-server" target="_blank">10 Essential Steps for Configuring a New Server</a>
- <a href="https://phoenixnap.com/kb/how-to-change-root-password-linux" target="_blank">Changing your Root password in Ubuntu</a>
- <a href="" target="_blank"></a>