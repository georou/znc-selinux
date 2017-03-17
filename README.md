# ZNC Bouncer SELinux Basic Module

This is a policy module built on CentOS 7.2 64-bit. Using Reference policy. For systemd.

This is a very basic policy for a ZNC Bouncer setup to confine it with SELinux. Since I couldn't find anything handy on the internet, I wrote it for my own use and hopefully other's will find it useful. This will allow you get off the ground quickly and start using ZNC. You can add more functionality if you want but this should be a good base.

Since this is only a very basic policy module, it needs care if something gets denied if you go outside just using the basic functions. Remember to check the audit logs regularly.

## Assumptions
* This was writen for CentOS 7. Therefore, no assumptions are made for other Operating Systems.
* Only using it for one user(personal bouncer).
* Using the default location for CentOS; /var/lib/znc and **not** in a home directory.

## Installation
```sh
# Follow the ZNC CentOS installation method:
http://wiki.znc.in/Installation#Fedora.2FCentOS.2FRed_Hat_Enterprise_Linux

# Clone the repo
git clone https://github.com/georou/znc-selinux.git

# Install the SELinux policy module. Optionally compile it before hand to ensure proper compatibility - see below
semodule -i zncd.pp

# Restore all the correct context labels
restorecon -v /usr/lib/systemd/system/znc.service
restorecon -Rv /var/lib/znc
restorecon -v /usr/bin/znc

# Start the server
systemctl enable znc.service
systemctl start znc.service
```
## How To Compile The Module Locally(Recommended before installing)
Ensure you have the `selinux-policy-devel` package installed.
```sh
# Change to the directory containing the .fc & .te files
cd znc-selinux
make -f /usr/share/selinux/devel/Makefile zncd.pp
```

## Debugging and Troubleshooting

* If you're getting permission errors, uncomment permissive in the .te file and try again. Re-check logs for any issues.

* Ease way to add in allow rules is the below command, then copy or redirect into the .te module. Rebuild and re-install:
* Don't forget to actually look at what is suggested. audit2allow will most likely go for a coarse grained permission!
* Check man audit2allow for different switches. Either use ref policy or not.
```sh
ausearch -m avc -ts recent | audit2allow -R
```
