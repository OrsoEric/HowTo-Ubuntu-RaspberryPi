# HowTo UBUNTU 24 RPI4

Instruction how to make Ubuntu run on Raspberry Pi headless

![](images/2026-05-30_07_28_IMG_20260530_072810%20Raspberry%20Pi%204B%20Ubuntu%2024%20Server%20LTS%20Ethernet%20and%20WiFi.jpg)

Ubuntu has two beasts that need slaying to make Raspberry Pi Headless work:
- Netplan
- APT sources

The problem with netplan is if you touch it, it will brick, and your headless Raspberry Pi is dead in the water. And because it's an headless Raspberry Pi, you need to nail down the network configuration without KVM. It's a consistent problem I have with robots, this guide lists a number of workaround to keep the netplan from bricking, keep the Raspberry connected, and finding it on the network.

The problem with APT Sources is that they changed place and aren't there, bricking many packages for things like Raspicam and ROS2

## Materials

- Fast SD Card
- SD card burner
- Raspberry Pi 4B
- WiFi router

# Raspberry Pi Imager

https://www.raspberrypi.com/software/

- other OS
- Ubuntu 24 LTS Server
    - select hostname ```rpi4-orso-sda```
    - select localization
    - select user and password
    - select wifi SSID
    - enable SSH

<details>
<summary>Screenshots</summary>

![](/images/rpi-imager%20(1).png)

![](/images/rpi-imager%20(2).png)

![](/images/rpi-imager%20(3).png)

![](/images/rpi-imager%20(4).png)

![](/images/rpi-imager%20(5).png)

![](/images/rpi-imager%20(6).png)

![](/images/rpi-imager%20(7).png)

![](/images/rpi-imager%20(8).png)

![](/images/rpi-imager%20(9).png)

![](/images/rpi-imager%20(10).png)

![](/images/rpi-imager%20(11).png)

![](/images/rpi-imager%20(12).png)

</details>



# Bootup Raspberry Pi

Insert SD card into raspberry and power up (CARE: make sure it's inserted well)

A good sign is if the green light blinks intermittently, it's fine if it goes black later.

A bad sign is if the red light is intermittent, it may mean power issues.

# Connecting to Raspberry Pi Headless

There are three ways to do it

- hope that wifi worked and look at the dhcp of the wifi network
- connect via ethernet and hope that the dhcp of the ethernet worked
- connect KVM and hope that it boots into HDMI

None of the three ways are reliable, headless booting of Ubuntu into Raspberry is almost impossible because of the cloud init will brick when touched. E.g. it's convenient to have a static IP on the ethernet of the Raspberry Pi since I often connect with a computer point to point for dev, but it's easy to brick the network configuration. Netplan syntax changes constantly and an old way will brick it.

This guide will walk through ways to edit it more safely, but the most reliable way to do it, is to let RaspberryPi Imager compile the Netplan with a WiFi and DHCP, and never touch it. Using a DHCP on the host or a router if you use the ethernet

## Putty

[Use Putty for SSH connection](https://putty.org/index.html)

Find the IP address and punch it in Putty portable

![](/images/Screenshot%202026-05-23%20123156.png)

If you have the SSH it will go forward.

It may ask to add a SSH key, accept.

There may be issues with SSH keys, you may need to clear the local ssh storage if this gives error. 

![](/images/Screenshot%202026-05-23%20123208.png)

If this gets quick to the login page is good, use your credentials

![](/images/Screenshot%202026-05-23%20123220.png)

### DUPLICATED SSH KEY

```bash
[07:35:09.839] Generated SSH command: 'type "C:\Users\FATHER~1\AppData\Local\Temp\vscode-linux-multi-line-command-192.168.1.227-850984785.sh" | "C:\Windows\System32\OpenSSH\ssh.exe" -T -D 51080 "sona@192.168.1.227" sh'
[07:35:09.839] Using connect timeout of 17 seconds
[07:35:09.840] Terminal shell path: C:\Windows\System32\cmd.exe
[07:35:10.058] > 
[07:35:10.059] Got some output, clearing connection timeout
[07:35:10.068] > 
[07:35:10.136] > @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
> @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
> @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
> IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
> Someone could be eavesdropping on you right now (man-in-the-middle attack)!
> It is also possible that a host key has just been changed.
> The fingerprint for the ED25519 key sent by the remote host is
> SHA256:a...Y.
> Please contact your system administrator.
> Add correct host key in C:\\Users\\FatherOfMachines/.ssh/known_hosts to get rid of this message.
> Offending ECDSA key in C:\\Users\\FatherOfMachines/.ssh/known_hosts:15
> Host key for 192.168.1.227 has changed and you have requested strict checking.
> Host key verification failed.
> The process tried to write to a nonexistent pipe.
[07:35:10.436] "install" terminal command done
[07:35:10.437] Install terminal quit with output: Host key verification failed.
[07:35:10.437] Received install output: Host key verification failed.
[07:35:10.437] WARN: $PLATFORM is undefined in installation script output.  Errors may be dropped.
[07:35:10.437] Failed to parse remote port from server output
[07:35:10.438] Exec server for ssh-remote+7b...d failed: Error
[07:35:10.438] Error opening exec server for ssh-remote+7...d: Error
[07:35:10.438] No hints found in the recent session.
```

go to ```C:\Users\FatherOfMachines\.ssh``` open ```known_hosts``` and delete the entry associated with the IP address and SSH 

![](images/Screenshot%202026-05-30%20074015%20Windows%20SSH%20keys%20delete%20if%20IP%20already%20used.png)


## Use IP scanner to find Raspberry Pi IP

[I use Angry IP scanner](https://angryip.org/)

look at the hostname, if this worked, it appear twice.

once for the WiFI interface

once for the ETH interface

![](/images/Screenshot%202026-05-23%20123849.png)

## TFTPD64 - Point to Point DHCP Server

You can use a computer with a point to point ethernet, and a local DHCP server to use your PC as DHCP server without a router on the go

https://pjo2.github.io/tftpd64/

![](tftpd64/Screenshot%202025-11-24%20184544.jpg)

![](tftpd64/Screenshot%202025-11-24%20185005.jpg)

![](tftpd64/Screenshot%202025-11-24%20185029.jpg)


## Connect via SSH wifi

Look into your router to see if the Raspberry Pi connected to WiFi and got an address

![](/images/Screenshot%202026-05-30%20073002%20router%20pi%20ip%20address.png)

![](images/Screenshot%202026-05-23%20123240.png)

## Connect via Ethernet

Using one of the above methods to find the IP, if it worked 

![](images/Screenshot%202026-05-23%20123814.png)

# COMMANDS

## Update Ubuntu

```bash
sudo apt update
sudo apt upgrade
```

<details>
<summary>logs</summary>

```bash
sona@rpi4-orso-sda:~$ sudo apt update
[sudo] password for sona:
Hit:1 http://ports.ubuntu.com/ubuntu-ports noble InRelease
Hit:2 http://ports.ubuntu.com/ubuntu-ports noble-security InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
85 packages can be upgraded. Run 'apt list --upgradable' to see them.
sona@rpi4-orso-sda:~$ sudo apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
The following NEW packages will be installed:
  linux-image-6.8.0-1056-raspi linux-modules-6.8.0-1056-raspi
The following packages will be upgraded:
  bind9-dnsutils bind9-host bind9-libs bsdextrautils bsdutils curl dpkg eject fdisk gcc-14-base gir1.2-packagekitglib-1.0 jq kmod
  libarchive13t64 libblkid1 libcap2 libcap2-bin libcurl3t64-gnutls libcurl4t64 libexpat1 libfdisk1 libgcc-s1 libgnutls30t64 libjq1
  libkmod2 libmount1 libnghttp2-14 libnss-systemd libnss3 libntfs-3g89t64 libpackagekit-glib2-18 libpam-cap libpam-systemd
  libpng16-16t64 libpolkit-agent-1-0 libpolkit-gobject-1-0 libpython3.12-minimal libpython3.12-stdlib libpython3.12t64
  libsmartcols1 libssh-4 libssl3t64 libstdc++6 libsystemd-shared libsystemd0 libudev1 libuuid1 linux-firmware linux-image-raspi
  mount ntfs-3g openssh-client openssh-server openssh-sftp-server openssl packagekit packagekit-tools polkitd pollinate
  python3-cryptography python3-jwt python3-openssl python3-pyasn1 python3.12 python3.12-minimal rsync sed snapd sudo systemd
  systemd-dev systemd-resolved systemd-sysv systemd-timesyncd tzdata u-boot-tools udev util-linux uuid-runtime vim vim-common
  vim-runtime vim-tiny wireless-regdb xxd
85 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
85 standard LTS security updates
Need to get 0 B/840 MB of archives.
After this operation, 218 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Extracting templates from packages: 100%
Preconfiguring packages ...
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../bsdutils_1%3a2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking bsdutils (1:2.39.3-9ubuntu6.5) over (1:2.39.3-9ubuntu6.4) ...
Setting up bsdutils (1:2.39.3-9ubuntu6.5) ...
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../dpkg_1.22.6ubuntu6.6_arm64.deb ...
Unpacking dpkg (1.22.6ubuntu6.6) over (1.22.6ubuntu6.5) ...
Setting up dpkg (1.22.6ubuntu6.6) ...
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../sed_4.9-2ubuntu0.24.04.1_arm64.deb ...
Unpacking sed (4.9-2ubuntu0.24.04.1) over (4.9-2build1) ...
Setting up sed (4.9-2ubuntu0.24.04.1) ...
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../util-linux_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking util-linux (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Setting up util-linux (2.39.3-9ubuntu6.5) ...
fstrim.service is a disabled or a static unit not running, not starting it.
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../mount_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking mount (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Preparing to unpack .../libexpat1_2.6.1-2ubuntu0.4_arm64.deb ...
Unpacking libexpat1:arm64 (2.6.1-2ubuntu0.4) over (2.6.1-2ubuntu0.3) ...
Preparing to unpack .../libpython3.12t64_3.12.3-1ubuntu0.13_arm64.deb ...
Unpacking libpython3.12t64:arm64 (3.12.3-1ubuntu0.13) over (3.12.3-1ubuntu0.11) ...
Preparing to unpack .../libssl3t64_3.0.13-0ubuntu3.9_arm64.deb ...
Unpacking libssl3t64:arm64 (3.0.13-0ubuntu3.9) over (3.0.13-0ubuntu3.7) ...
Setting up libssl3t64:arm64 (3.0.13-0ubuntu3.9) ...
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../0-python3.12_3.12.3-1ubuntu0.13_arm64.deb ...
Unpacking python3.12 (3.12.3-1ubuntu0.13) over (3.12.3-1ubuntu0.11) ...
Preparing to unpack .../1-libpython3.12-stdlib_3.12.3-1ubuntu0.13_arm64.deb ...
Unpacking libpython3.12-stdlib:arm64 (3.12.3-1ubuntu0.13) over (3.12.3-1ubuntu0.11) ...
Preparing to unpack .../2-python3.12-minimal_3.12.3-1ubuntu0.13_arm64.deb ...
Unpacking python3.12-minimal (3.12.3-1ubuntu0.13) over (3.12.3-1ubuntu0.11) ...
Preparing to unpack .../3-libpython3.12-minimal_3.12.3-1ubuntu0.13_arm64.deb ...
Unpacking libpython3.12-minimal:arm64 (3.12.3-1ubuntu0.13) over (3.12.3-1ubuntu0.11) ...
Preparing to unpack .../4-tzdata_2026a-0ubuntu0.24.04.1_all.deb ...
Unpacking tzdata (2026a-0ubuntu0.24.04.1) over (2025b-0ubuntu0.24.04.1) ...
Preparing to unpack .../5-libcap2_1%3a2.66-5ubuntu2.4_arm64.deb ...
Unpacking libcap2:arm64 (1:2.66-5ubuntu2.4) over (1:2.66-5ubuntu2.2) ...
Setting up libcap2:arm64 (1:2.66-5ubuntu2.4) ...
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../libnss-systemd_255.4-1ubuntu8.14_arm64.deb ...
Unpacking libnss-systemd:arm64 (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../systemd-dev_255.4-1ubuntu8.14_all.deb ...
Unpacking systemd-dev (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../libblkid1_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking libblkid1:arm64 (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Setting up libblkid1:arm64 (2.39.3-9ubuntu6.5) ...
(Reading database ... 51745 files and directories currently installed.)
Preparing to unpack .../0-kmod_31+20240202-2ubuntu7.2_arm64.deb ...
Unpacking kmod (31+20240202-2ubuntu7.2) over (31+20240202-2ubuntu7.1) ...
Preparing to unpack .../1-libkmod2_31+20240202-2ubuntu7.2_arm64.deb ...
Unpacking libkmod2:arm64 (31+20240202-2ubuntu7.2) over (31+20240202-2ubuntu7.1) ...
Preparing to unpack .../2-systemd-timesyncd_255.4-1ubuntu8.14_arm64.deb ...
Unpacking systemd-timesyncd (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../3-systemd-resolved_255.4-1ubuntu8.14_arm64.deb ...
Unpacking systemd-resolved (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../4-libsystemd-shared_255.4-1ubuntu8.14_arm64.deb ...
Unpacking libsystemd-shared:arm64 (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../5-libsystemd0_255.4-1ubuntu8.14_arm64.deb ...
Unpacking libsystemd0:arm64 (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Setting up libsystemd0:arm64 (255.4-1ubuntu8.14) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../systemd-sysv_255.4-1ubuntu8.14_arm64.deb ...
Unpacking systemd-sysv (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../libpam-systemd_255.4-1ubuntu8.14_arm64.deb ...
Unpacking libpam-systemd:arm64 (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../systemd_255.4-1ubuntu8.14_arm64.deb ...
Unpacking systemd (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../udev_255.4-1ubuntu8.14_arm64.deb ...
Unpacking udev (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Preparing to unpack .../libudev1_255.4-1ubuntu8.14_arm64.deb ...
Unpacking libudev1:arm64 (255.4-1ubuntu8.14) over (255.4-1ubuntu8.12) ...
Setting up libudev1:arm64 (255.4-1ubuntu8.14) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../libmount1_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking libmount1:arm64 (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Setting up libmount1:arm64 (2.39.3-9ubuntu6.5) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../libuuid1_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking libuuid1:arm64 (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Setting up libuuid1:arm64 (2.39.3-9ubuntu6.5) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../libfdisk1_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking libfdisk1:arm64 (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Preparing to unpack .../libgnutls30t64_3.8.3-1.1ubuntu3.6_arm64.deb ...
Unpacking libgnutls30t64:arm64 (3.8.3-1.1ubuntu3.6) over (3.8.3-1.1ubuntu3.4) ...
Setting up libgnutls30t64:arm64 (3.8.3-1.1ubuntu3.6) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../ntfs-3g_1%3a2022.10.3-1.2ubuntu3.1_arm64.deb ...
Unpacking ntfs-3g (1:2022.10.3-1.2ubuntu3.1) over (1:2022.10.3-1.2ubuntu3) ...
Preparing to unpack .../libntfs-3g89t64_1%3a2022.10.3-1.2ubuntu3.1_arm64.deb ...
Unpacking libntfs-3g89t64:arm64 (1:2022.10.3-1.2ubuntu3.1) over (1:2022.10.3-1.2ubuntu3) ...
Preparing to unpack .../rsync_3.2.7-1ubuntu1.4_arm64.deb ...
Unpacking rsync (3.2.7-1ubuntu1.4) over (3.2.7-1ubuntu1.2) ...
Preparing to unpack .../libsmartcols1_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking libsmartcols1:arm64 (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Setting up libsmartcols1:arm64 (2.39.3-9ubuntu6.5) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../uuid-runtime_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking uuid-runtime (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Preparing to unpack .../openssh-sftp-server_1%3a9.6p1-3ubuntu13.16_arm64.deb ...
Unpacking openssh-sftp-server (1:9.6p1-3ubuntu13.16) over (1:9.6p1-3ubuntu13.14) ...
Preparing to unpack .../openssh-server_1%3a9.6p1-3ubuntu13.16_arm64.deb ...
Unpacking openssh-server (1:9.6p1-3ubuntu13.16) over (1:9.6p1-3ubuntu13.14) ...
Preparing to unpack .../openssh-client_1%3a9.6p1-3ubuntu13.16_arm64.deb ...
Unpacking openssh-client (1:9.6p1-3ubuntu13.16) over (1:9.6p1-3ubuntu13.14) ...
Preparing to unpack .../gcc-14-base_14.2.0-4ubuntu2~24.04.1_arm64.deb ...
Unpacking gcc-14-base:arm64 (14.2.0-4ubuntu2~24.04.1) over (14.2.0-4ubuntu2~24.04) ...
Setting up gcc-14-base:arm64 (14.2.0-4ubuntu2~24.04.1) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../libstdc++6_14.2.0-4ubuntu2~24.04.1_arm64.deb ...
Unpacking libstdc++6:arm64 (14.2.0-4ubuntu2~24.04.1) over (14.2.0-4ubuntu2~24.04) ...
Setting up libstdc++6:arm64 (14.2.0-4ubuntu2~24.04.1) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../libgcc-s1_14.2.0-4ubuntu2~24.04.1_arm64.deb ...
Unpacking libgcc-s1:arm64 (14.2.0-4ubuntu2~24.04.1) over (14.2.0-4ubuntu2~24.04) ...
Setting up libgcc-s1:arm64 (14.2.0-4ubuntu2~24.04.1) ...
(Reading database ... 51746 files and directories currently installed.)
Preparing to unpack .../00-eject_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking eject (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Preparing to unpack .../01-libpam-cap_1%3a2.66-5ubuntu2.4_arm64.deb ...
Unpacking libpam-cap:arm64 (1:2.66-5ubuntu2.4) over (1:2.66-5ubuntu2.2) ...
Preparing to unpack .../02-libcap2-bin_1%3a2.66-5ubuntu2.4_arm64.deb ...
Unpacking libcap2-bin (1:2.66-5ubuntu2.4) over (1:2.66-5ubuntu2.2) ...
Preparing to unpack .../03-openssl_3.0.13-0ubuntu3.9_arm64.deb ...
Unpacking openssl (3.0.13-0ubuntu3.9) over (3.0.13-0ubuntu3.7) ...
Preparing to unpack .../04-sudo_1.9.15p5-3ubuntu5.24.04.2_arm64.deb ...
Unpacking sudo (1.9.15p5-3ubuntu5.24.04.2) over (1.9.15p5-3ubuntu5.24.04.1) ...
Preparing to unpack .../05-vim_2%3a9.1.0016-1ubuntu7.13_arm64.deb ...
Unpacking vim (2:9.1.0016-1ubuntu7.13) over (2:9.1.0016-1ubuntu7.9) ...
Preparing to unpack .../06-vim-common_2%3a9.1.0016-1ubuntu7.13_all.deb ...
Unpacking vim-common (2:9.1.0016-1ubuntu7.13) over (2:9.1.0016-1ubuntu7.9) ...
Preparing to unpack .../07-vim-tiny_2%3a9.1.0016-1ubuntu7.13_arm64.deb ...
Unpacking vim-tiny (2:9.1.0016-1ubuntu7.13) over (2:9.1.0016-1ubuntu7.9) ...
Preparing to unpack .../08-vim-runtime_2%3a9.1.0016-1ubuntu7.13_all.deb ...
Unpacking vim-runtime (2:9.1.0016-1ubuntu7.13) over (2:9.1.0016-1ubuntu7.9) ...
Preparing to unpack .../09-xxd_2%3a9.1.0016-1ubuntu7.13_arm64.deb ...
Unpacking xxd (2:9.1.0016-1ubuntu7.13) over (2:9.1.0016-1ubuntu7.9) ...
Preparing to unpack .../10-libnghttp2-14_1.59.0-1ubuntu0.3_arm64.deb ...
Unpacking libnghttp2-14:arm64 (1.59.0-1ubuntu0.3) over (1.59.0-1ubuntu0.2) ...
Preparing to unpack .../11-bind9-dnsutils_1%3a9.18.39-0ubuntu0.24.04.5_arm64.deb ...
Unpacking bind9-dnsutils (1:9.18.39-0ubuntu0.24.04.5) over (1:9.18.39-0ubuntu0.24.04.2) ...
Preparing to unpack .../12-bind9-host_1%3a9.18.39-0ubuntu0.24.04.5_arm64.deb ...
Unpacking bind9-host (1:9.18.39-0ubuntu0.24.04.5) over (1:9.18.39-0ubuntu0.24.04.2) ...
Preparing to unpack .../13-bind9-libs_1%3a9.18.39-0ubuntu0.24.04.5_arm64.deb ...
Unpacking bind9-libs:arm64 (1:9.18.39-0ubuntu0.24.04.5) over (1:9.18.39-0ubuntu0.24.04.2) ...
Preparing to unpack .../14-bsdextrautils_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking bsdextrautils (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Preparing to unpack .../15-libpng16-16t64_1.6.43-5ubuntu0.6_arm64.deb ...
Unpacking libpng16-16t64:arm64 (1.6.43-5ubuntu0.6) over (1.6.43-5ubuntu0.4) ...
Preparing to unpack .../16-libssh-4_0.10.6-2ubuntu0.4_arm64.deb ...
Unpacking libssh-4:arm64 (0.10.6-2ubuntu0.4) over (0.10.6-2ubuntu0.2) ...
Preparing to unpack .../17-curl_8.5.0-2ubuntu10.9_arm64.deb ...
Unpacking curl (8.5.0-2ubuntu10.9) over (8.5.0-2ubuntu10.6) ...
Preparing to unpack .../18-libcurl4t64_8.5.0-2ubuntu10.9_arm64.deb ...
Unpacking libcurl4t64:arm64 (8.5.0-2ubuntu10.9) over (8.5.0-2ubuntu10.6) ...
Preparing to unpack .../19-fdisk_2.39.3-9ubuntu6.5_arm64.deb ...
Unpacking fdisk (2.39.3-9ubuntu6.5) over (2.39.3-9ubuntu6.4) ...
Preparing to unpack .../20-libpackagekit-glib2-18_1.2.8-2ubuntu1.5_arm64.deb ...
Unpacking libpackagekit-glib2-18:arm64 (1.2.8-2ubuntu1.5) over (1.2.8-2ubuntu1.4) ...
Preparing to unpack .../21-gir1.2-packagekitglib-1.0_1.2.8-2ubuntu1.5_arm64.deb ...
Unpacking gir1.2-packagekitglib-1.0 (1.2.8-2ubuntu1.5) over (1.2.8-2ubuntu1.4) ...
Preparing to unpack .../22-jq_1.7.1-3ubuntu0.24.04.2_arm64.deb ...
Unpacking jq (1.7.1-3ubuntu0.24.04.2) over (1.7.1-3ubuntu0.24.04.1) ...
Preparing to unpack .../23-libjq1_1.7.1-3ubuntu0.24.04.2_arm64.deb ...
Unpacking libjq1:arm64 (1.7.1-3ubuntu0.24.04.2) over (1.7.1-3ubuntu0.24.04.1) ...
Preparing to unpack .../24-libarchive13t64_3.7.2-2ubuntu0.7_arm64.deb ...
Unpacking libarchive13t64:arm64 (3.7.2-2ubuntu0.7) over (3.7.2-2ubuntu0.5) ...
Preparing to unpack .../25-libcurl3t64-gnutls_8.5.0-2ubuntu10.9_arm64.deb ...
Unpacking libcurl3t64-gnutls:arm64 (8.5.0-2ubuntu10.9) over (8.5.0-2ubuntu10.6) ...
Preparing to unpack .../26-libnss3_2%3a3.98-1ubuntu0.1_arm64.deb ...
Unpacking libnss3:arm64 (2:3.98-1ubuntu0.1) over (2:3.98-1build1) ...
Preparing to unpack .../27-polkitd_124-2ubuntu1.24.04.3_arm64.deb ...
Unpacking polkitd (124-2ubuntu1.24.04.3) over (124-2ubuntu1.24.04.2) ...
Preparing to unpack .../28-libpolkit-agent-1-0_124-2ubuntu1.24.04.3_arm64.deb ...
Unpacking libpolkit-agent-1-0:arm64 (124-2ubuntu1.24.04.3) over (124-2ubuntu1.24.04.2) ...
Preparing to unpack .../29-libpolkit-gobject-1-0_124-2ubuntu1.24.04.3_arm64.deb ...
Unpacking libpolkit-gobject-1-0:arm64 (124-2ubuntu1.24.04.3) over (124-2ubuntu1.24.04.2) ...
Preparing to unpack .../30-linux-firmware_20240318.git3b128b60-0ubuntu2.26_arm64.deb ...
Unpacking linux-firmware (20240318.git3b128b60-0ubuntu2.26) over (20240318.git3b128b60-0ubuntu2.23) ...
Preparing to unpack .../31-wireless-regdb_2025.10.07-0ubuntu1~24.04.1_all.deb ...
Unpacking wireless-regdb (2025.10.07-0ubuntu1~24.04.1) over (2025.07.10-0ubuntu1~24.04.1) ...
Selecting previously unselected package linux-modules-6.8.0-1056-raspi.
Preparing to unpack .../32-linux-modules-6.8.0-1056-raspi_6.8.0-1056.60_arm64.deb ...
Unpacking linux-modules-6.8.0-1056-raspi (6.8.0-1056.60) ...
Selecting previously unselected package linux-image-6.8.0-1056-raspi.
Preparing to unpack .../33-linux-image-6.8.0-1056-raspi_6.8.0-1056.60_arm64.deb ...
Unpacking linux-image-6.8.0-1056-raspi (6.8.0-1056.60) ...
Preparing to unpack .../34-linux-image-raspi_6.8.0-1056.60_arm64.deb ...
Unpacking linux-image-raspi (6.8.0-1056.60) over (6.8.0-1047.51) ...
Preparing to unpack .../35-packagekit-tools_1.2.8-2ubuntu1.5_arm64.deb ...
Unpacking packagekit-tools (1.2.8-2ubuntu1.5) over (1.2.8-2ubuntu1.4) ...
Preparing to unpack .../36-packagekit_1.2.8-2ubuntu1.5_arm64.deb ...
Unpacking packagekit (1.2.8-2ubuntu1.5) over (1.2.8-2ubuntu1.4) ...
Preparing to unpack .../37-pollinate_4.33-3.1ubuntu1.3_all.deb ...
Unpacking pollinate (4.33-3.1ubuntu1.3) over (4.33-3.1ubuntu1.1) ...
Preparing to unpack .../38-python3-cryptography_41.0.7-4ubuntu0.4_arm64.deb ...
Unpacking python3-cryptography (41.0.7-4ubuntu0.4) over (41.0.7-4ubuntu0.1) ...
Preparing to unpack .../39-python3-jwt_2.7.0-1ubuntu0.1_all.deb ...
Unpacking python3-jwt (2.7.0-1ubuntu0.1) over (2.7.0-1) ...
Preparing to unpack .../40-python3-openssl_23.2.0-1ubuntu0.1_all.deb ...
Unpacking python3-openssl (23.2.0-1ubuntu0.1) over (23.2.0-1) ...
Preparing to unpack .../41-python3-pyasn1_0.4.8-4ubuntu0.2_all.deb ...
Unpacking python3-pyasn1 (0.4.8-4ubuntu0.2) over (0.4.8-4ubuntu0.1) ...
Preparing to unpack .../42-snapd_2.73+ubuntu24.04.2_arm64.deb ...
Unpacking snapd (2.73+ubuntu24.04.2) over (2.73+ubuntu24.04) ...
Preparing to unpack .../43-u-boot-tools_2025.10-0ubuntu0.24.04.2_arm64.deb ...
Unpacking u-boot-tools (2025.10-0ubuntu0.24.04.2) over (2025.10-0ubuntu0.24.04.1) ...
Setting up libexpat1:arm64 (2.6.1-2ubuntu0.4) ...
Setting up bsdextrautils (2.39.3-9ubuntu6.5) ...
Setting up python3-jwt (2.7.0-1ubuntu0.1) ...
Setting up libntfs-3g89t64:arm64 (1:2022.10.3-1.2ubuntu3.1) ...
Setting up linux-firmware (20240318.git3b128b60-0ubuntu2.26) ...
Setting up libjq1:arm64 (1.7.1-3ubuntu0.24.04.2) ...
Setting up openssh-client (1:9.6p1-3ubuntu13.16) ...
Setting up wireless-regdb (2025.10.07-0ubuntu1~24.04.1) ...
Setting up libpython3.12-minimal:arm64 (3.12.3-1ubuntu0.13) ...
Setting up libnghttp2-14:arm64 (1.59.0-1ubuntu0.3) ...
Setting up libpackagekit-glib2-18:arm64 (1.2.8-2ubuntu1.5) ...
Setting up systemd-dev (255.4-1ubuntu8.14) ...
Setting up libnss3:arm64 (2:3.98-1ubuntu0.1) ...
Setting up xxd (2:9.1.0016-1ubuntu7.13) ...
Setting up ntfs-3g (1:2022.10.3-1.2ubuntu3.1) ...
Setting up tzdata (2026a-0ubuntu0.24.04.1) ...

Current default time zone: 'Europe/Rome'
Local time is now:      Sat May 23 13:04:14 CEST 2026.
Universal Time is now:  Sat May 23 11:04:14 UTC 2026.
Run 'dpkg-reconfigure tzdata' if you wish to change it.

Setting up libcap2-bin (1:2.66-5ubuntu2.4) ...
Setting up eject (2.39.3-9ubuntu6.5) ...
Setting up gir1.2-packagekitglib-1.0 (1.2.8-2ubuntu1.5) ...
Setting up vim-common (2:9.1.0016-1ubuntu7.13) ...
Setting up python3-cryptography (41.0.7-4ubuntu0.4) ...
Setting up libpng16-16t64:arm64 (1.6.43-5ubuntu0.6) ...
Setting up sudo (1.9.15p5-3ubuntu5.24.04.2) ...
Setting up libssh-4:arm64 (0.10.6-2ubuntu0.4) ...
Setting up libfdisk1:arm64 (2.39.3-9ubuntu6.5) ...
Setting up u-boot-tools (2025.10-0ubuntu0.24.04.2) ...
Setting up mount (2.39.3-9ubuntu6.5) ...
Setting up uuid-runtime (2.39.3-9ubuntu6.5) ...
uuidd.service is a disabled or a static unit not running, not starting it.
Setting up jq (1.7.1-3ubuntu0.24.04.2) ...
Setting up python3-pyasn1 (0.4.8-4ubuntu0.2) ...
Setting up vim-runtime (2:9.1.0016-1ubuntu7.13) ...
Setting up openssl (3.0.13-0ubuntu3.9) ...
Setting up libpam-cap:arm64 (1:2.66-5ubuntu2.4) ...
Setting up libarchive13t64:arm64 (3.7.2-2ubuntu0.7) ...
Setting up libpolkit-gobject-1-0:arm64 (124-2ubuntu1.24.04.3) ...
Setting up rsync (3.2.7-1ubuntu1.4) ...
rsync.service is a disabled or a static unit not running, not starting it.
Setting up libkmod2:arm64 (31+20240202-2ubuntu7.2) ...
Setting up python3.12-minimal (3.12.3-1ubuntu0.13) ...
Setting up openssh-sftp-server (1:9.6p1-3ubuntu13.16) ...
Setting up libpython3.12-stdlib:arm64 (3.12.3-1ubuntu0.13) ...
Setting up openssh-server (1:9.6p1-3ubuntu13.16) ...
Setting up libcurl4t64:arm64 (8.5.0-2ubuntu10.9) ...
Setting up bind9-libs:arm64 (1:9.18.39-0ubuntu0.24.04.5) ...
Setting up python3-openssl (23.2.0-1ubuntu0.1) ...
Setting up python3.12 (3.12.3-1ubuntu0.13) ...
Setting up libcurl3t64-gnutls:arm64 (8.5.0-2ubuntu10.9) ...
Setting up vim-tiny (2:9.1.0016-1ubuntu7.13) ...
Setting up kmod (31+20240202-2ubuntu7.2) ...
Setting up fdisk (2.39.3-9ubuntu6.5) ...
Setting up libpython3.12t64:arm64 (3.12.3-1ubuntu0.13) ...
Setting up libsystemd-shared:arm64 (255.4-1ubuntu8.14) ...
Setting up libpolkit-agent-1-0:arm64 (124-2ubuntu1.24.04.3) ...
Setting up curl (8.5.0-2ubuntu10.9) ...
Setting up bind9-host (1:9.18.39-0ubuntu0.24.04.5) ...
Setting up vim (2:9.1.0016-1ubuntu7.13) ...
Setting up systemd (255.4-1ubuntu8.14) ...
Setting up pollinate (4.33-3.1ubuntu1.3) ...
Installing new version of config file /etc/default/pollinate ...
Removing obsolete conffile /etc/pollinate/entropy.ubuntu.com.pem ...
Setting up systemd-timesyncd (255.4-1ubuntu8.14) ...
Setting up udev (255.4-1ubuntu8.14) ...
Setting up bind9-dnsutils (1:9.18.39-0ubuntu0.24.04.5) ...
Setting up systemd-resolved (255.4-1ubuntu8.14) ...
Setting up snapd (2.73+ubuntu24.04.2) ...
snapd.failure.service is a disabled or a static unit not running, not starting it.
snapd.gpio-chardev-setup.target is a disabled or a static unit not running, not starting it.
snapd.snap-repair.service is a disabled or a static unit not running, not starting it.
Setting up systemd-sysv (255.4-1ubuntu8.14) ...
Setting up libnss-systemd:arm64 (255.4-1ubuntu8.14) ...
Setting up libpam-systemd:arm64 (255.4-1ubuntu8.14) ...
Setting up polkitd (124-2ubuntu1.24.04.3) ...
Setting up linux-image-6.8.0-1056-raspi (6.8.0-1056.60) ...
I: /boot/vmlinuz is now a symlink to vmlinuz-6.8.0-1056-raspi
I: /boot/initrd.img is now a symlink to initrd.img-6.8.0-1056-raspi
Setting up linux-image-raspi (6.8.0-1056.60) ...
Setting up linux-modules-6.8.0-1056-raspi (6.8.0-1056.60) ...
Processing triggers for sgml-base (1.31) ...
Processing triggers for install-info (7.1-3build2) ...
Processing triggers for initramfs-tools (0.142ubuntu25.8) ...
update-initramfs: Generating /boot/initrd.img-6.8.0-1047-raspi
Using DTB: bcm2711-rpi-4-b.dtb
Installing /lib/firmware/6.8.0-1047-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/6.8.0-1047-raspi/./bcm2711-rpi-4-b.dtb
Installing new bcm2711-rpi-4-b.dtb.
Ignoring old or unknown version 6.8.0-1047-raspi (latest is 6.8.0-1056-raspi)
Processing triggers for libc-bin (2.39-0ubuntu8.7) ...
Processing triggers for ufw (0.36.2-6) ...
Processing triggers for man-db (2.12.0-4build2) ...
Processing triggers for dbus (1.14.10-4ubuntu4.1) ...
Setting up packagekit (1.2.8-2ubuntu1.5) ...
Setting up packagekit-tools (1.2.8-2ubuntu1.5) ...
Processing triggers for linux-image-6.8.0-1056-raspi (6.8.0-1056.60) ...
/etc/kernel/postinst.d/initramfs-tools:
update-initramfs: Generating /boot/initrd.img-6.8.0-1056-raspi
Using DTB: bcm2711-rpi-4-b.dtb
Installing /lib/firmware/6.8.0-1056-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/6.8.0-1056-raspi/./bcm2711-rpi-4-b.dtb
Installing new bcm2711-rpi-4-b.dtb.
flash-kernel: deferring update (trigger activated)
/etc/kernel/postinst.d/zz-flash-kernel:
Using DTB: bcm2711-rpi-4-b.dtb
Installing /lib/firmware/6.8.0-1056-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/6.8.0-1056-raspi/./bcm2711-rpi-4-b.dtb
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
flash-kernel: deferring update (trigger activated)
Processing triggers for flash-kernel (3.107ubuntu13~24.04.6) ...
Using DTB: bcm2711-rpi-4-b.dtb
Installing /lib/firmware/6.8.0-1056-raspi/device-tree/broadcom/bcm2711-rpi-4-b.dtb into /boot/dtbs/6.8.0-1056-raspi/./bcm2711-rpi-4-b.dtb
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
flash-kernel: installing version 6.8.0-1056-raspi
Taking backup of vmlinuz.
Installing new vmlinuz.
Taking backup of initrd.img.
Installing new initrd.img.
Generating boot script u-boot image... done.
Taking backup of boot.scr.
Installing new boot.scr.
Taking backup of start4.elf.
Installing new start4.elf.
Taking backup of fixup4db.dat.
Installing new fixup4db.dat.
Taking backup of start.elf.
Installing new start.elf.
Taking backup of start4x.elf.
Installing new start4x.elf.
Taking backup of start4db.elf.
Installing new start4db.elf.
Taking backup of fixup_x.dat.
Installing new fixup_x.dat.
Taking backup of fixup4cd.dat.
Installing new fixup4cd.dat.
Taking backup of start4cd.elf.
Installing new start4cd.elf.
Taking backup of fixup_db.dat.
Installing new fixup_db.dat.
Taking backup of bootcode.bin.
Installing new bootcode.bin.
Taking backup of fixup.dat.
Installing new fixup.dat.
Taking backup of fixup4x.dat.
Installing new fixup4x.dat.
Taking backup of fixup4.dat.
Installing new fixup4.dat.
Taking backup of start_db.elf.
Installing new start_db.elf.
Taking backup of fixup_cd.dat.
Installing new fixup_cd.dat.
Taking backup of start_cd.elf.
Installing new start_cd.elf.
Taking backup of start_x.elf.
Installing new start_x.elf.
Taking backup of bcm2710-rpi-3-b.dtb.
Installing new bcm2710-rpi-3-b.dtb.
Taking backup of bcm2712-rpi-cm5l-cm5io.dtb.
Installing new bcm2712-rpi-cm5l-cm5io.dtb.
Taking backup of bcm2712-rpi-5-b.dtb.
Installing new bcm2712-rpi-5-b.dtb.
Taking backup of bcm2711-rpi-400.dtb.
Installing new bcm2711-rpi-400.dtb.
Taking backup of bcm2711-rpi-cm4.dtb.
Installing new bcm2711-rpi-cm4.dtb.
Taking backup of bcm2710-rpi-cm3.dtb.
Installing new bcm2710-rpi-cm3.dtb.
Taking backup of bcm2712d0-rpi-5-b.dtb.
Installing new bcm2712d0-rpi-5-b.dtb.
Taking backup of bcm2712-rpi-500.dtb.
Installing new bcm2712-rpi-500.dtb.
Installing new hat_map.dtb.
Taking backup of bcm2711-rpi-cm4s.dtb.
Installing new bcm2711-rpi-cm4s.dtb.
Taking backup of bcm2710-rpi-zero-2-w.dtb.
Installing new bcm2710-rpi-zero-2-w.dtb.
Taking backup of bcm2711-rpi-4-b.dtb.
Installing new bcm2711-rpi-4-b.dtb.
Taking backup of bcm2712-rpi-cm5-cm4io.dtb.
Installing new bcm2712-rpi-cm5-cm4io.dtb.
Taking backup of bcm2710-rpi-3-b-plus.dtb.
Installing new bcm2710-rpi-3-b-plus.dtb.
Taking backup of bcm2710-rpi-zero-2.dtb.
Installing new bcm2710-rpi-zero-2.dtb.
Taking backup of bcm2710-rpi-2-b.dtb.
Installing new bcm2710-rpi-2-b.dtb.
Taking backup of bcm2712-rpi-cm5-cm5io.dtb.
Installing new bcm2712-rpi-cm5-cm5io.dtb.
Taking backup of bcm2712-rpi-cm5l-cm4io.dtb.
Installing new bcm2712-rpi-cm5l-cm4io.dtb.
Taking backup of i-sabre-q2m.dtbo.
Installing new i-sabre-q2m.dtbo.
Taking backup of highperi.dtbo.
Installing new highperi.dtbo.
Taking backup of ads7846.dtbo.
Installing new ads7846.dtbo.
Taking backup of waveshare-can-fd-hat-mode-a.dtbo.
Installing new waveshare-can-fd-hat-mode-a.dtbo.
Taking backup of ov5647.dtbo.
Installing new ov5647.dtbo.
Taking backup of dwc-otg.dtbo.
Installing new dwc-otg.dtbo.
Taking backup of audioinjector-bare-i2s.dtbo.
Installing new audioinjector-bare-i2s.dtbo.
Taking backup of jedec-spi-nor.dtbo.
Installing new jedec-spi-nor.dtbo.
Taking backup of midi-uart4.dtbo.
Installing new midi-uart4.dtbo.
Taking backup of vc4-kms-dsi-waveshare-panel.dtbo.
Installing new vc4-kms-dsi-waveshare-panel.dtbo.
Taking backup of uart2.dtbo.
Installing new uart2.dtbo.
Taking backup of spi5-2cs-pi5.dtbo.
Installing new spi5-2cs-pi5.dtbo.
Taking backup of pitft22.dtbo.
Installing new pitft22.dtbo.
Taking backup of imx327.dtbo.
Installing new imx327.dtbo.
Taking backup of spi2-3cs.dtbo.
Installing new spi2-3cs.dtbo.
Taking backup of ov2311.dtbo.
Installing new ov2311.dtbo.
Taking backup of vc4-kms-dpi-hyperpixel4.dtbo.
Installing new vc4-kms-dpi-hyperpixel4.dtbo.
Taking backup of media-center.dtbo.
Installing new media-center.dtbo.
Taking backup of spi5-2cs.dtbo.
Installing new spi5-2cs.dtbo.
Taking backup of i2c-mux.dtbo.
Installing new i2c-mux.dtbo.
Taking backup of adafruit-st7735r.dtbo.
Installing new adafruit-st7735r.dtbo.
Taking backup of watterott-display.dtbo.
Installing new watterott-display.dtbo.
Taking backup of imx462.dtbo.
Installing new imx462.dtbo.
Taking backup of adv7282m.dtbo.
Installing new adv7282m.dtbo.
Taking backup of vc4-kms-dsi-lt070me05000.dtbo.
Installing new vc4-kms-dsi-lt070me05000.dtbo.
Taking backup of ssd1306-spi.dtbo.
Installing new ssd1306-spi.dtbo.
Taking backup of rpi-dacpro.dtbo.
Installing new rpi-dacpro.dtbo.
Taking backup of spi2-2cs.dtbo.
Installing new spi2-2cs.dtbo.
Taking backup of balena-fin.dtbo.
Installing new balena-fin.dtbo.
Taking backup of adau7002-simple.dtbo.
Installing new adau7002-simple.dtbo.
Taking backup of ov9281.dtbo.
Installing new ov9281.dtbo.
Taking backup of dionaudio-loco-v2.dtbo.
Installing new dionaudio-loco-v2.dtbo.
Taking backup of vc4-kms-v3d-pi4.dtbo.
Installing new vc4-kms-v3d-pi4.dtbo.
Taking backup of mcp2515.dtbo.
Installing new mcp2515.dtbo.
Taking backup of pps-gpio.dtbo.
Installing new pps-gpio.dtbo.
Taking backup of vga666.dtbo.
Installing new vga666.dtbo.
Taking backup of pcie-32bit-dma-pi5.dtbo.
Installing new pcie-32bit-dma-pi5.dtbo.
Taking backup of i2c3.dtbo.
Installing new i2c3.dtbo.
Taking backup of camera-mux-4port.dtbo.
Installing new camera-mux-4port.dtbo.
Taking backup of sdio-pi5.dtbo.
Installing new sdio-pi5.dtbo.
Taking backup of piscreen.dtbo.
Installing new piscreen.dtbo.
Taking backup of sc16is752-i2c.dtbo.
Installing new sc16is752-i2c.dtbo.
Taking backup of dpi24.dtbo.
Installing new dpi24.dtbo.
Taking backup of ov64a40.dtbo.
Installing new ov64a40.dtbo.
Taking backup of ssd1327-spi.dtbo.
Installing new ssd1327-spi.dtbo.
Taking backup of i2c3-pi5.dtbo.
Installing new i2c3-pi5.dtbo.
Taking backup of cma.dtbo.
Installing new cma.dtbo.
Taking backup of hifiberry-amp3.dtbo.
Installing new hifiberry-amp3.dtbo.
Taking backup of arducam-pivariety.dtbo.
Installing new arducam-pivariety.dtbo.
Taking backup of udrc.dtbo.
Installing new udrc.dtbo.
Taking backup of rpi-sense-v2.dtbo.
Installing new rpi-sense-v2.dtbo.
Taking backup of spi3-1cs-pi5.dtbo.
Installing new spi3-1cs-pi5.dtbo.
Taking backup of imx378.dtbo.
Installing new imx378.dtbo.
Taking backup of i2c0-pi5.dtbo.
Installing new i2c0-pi5.dtbo.
Taking backup of goodix.dtbo.
Installing new goodix.dtbo.
Taking backup of gpio-no-irq.dtbo.
Installing new gpio-no-irq.dtbo.
Taking backup of midi-uart2-pi5.dtbo.
Installing new midi-uart2-pi5.dtbo.
Taking backup of mcp342x.dtbo.
Installing new mcp342x.dtbo.
Taking backup of dpi18cpadhi.dtbo.
Installing new dpi18cpadhi.dtbo.
Taking backup of piglow.dtbo.
Installing new piglow.dtbo.
Taking backup of pibell.dtbo.
Installing new pibell.dtbo.
Taking backup of imx258.dtbo.
Installing new imx258.dtbo.
Taking backup of allo-katana-dac-audio.dtbo.
Installing new allo-katana-dac-audio.dtbo.
Taking backup of audremap.dtbo.
Installing new audremap.dtbo.
Taking backup of draws.dtbo.
Installing new draws.dtbo.
Taking backup of i2c-gpio.dtbo.
Installing new i2c-gpio.dtbo.
Taking backup of allo-piano-dac-pcm512x-audio.dtbo.
Installing new allo-piano-dac-pcm512x-audio.dtbo.
Taking backup of rpi-codeczero.dtbo.
Installing new rpi-codeczero.dtbo.
Taking backup of gpio-key.dtbo.
Installing new gpio-key.dtbo.
Taking backup of imx219.dtbo.
Installing new imx219.dtbo.
Taking backup of rpi-ft5406.dtbo.
Installing new rpi-ft5406.dtbo.
Taking backup of hifiberry-digi.dtbo.
Installing new hifiberry-digi.dtbo.
Taking backup of disable-bt.dtbo.
Installing new disable-bt.dtbo.
Taking backup of pitft35-resistive.dtbo.
Installing new pitft35-resistive.dtbo.
Taking backup of midi-uart3.dtbo.
Installing new midi-uart3.dtbo.
Taking backup of uart1.dtbo.
Installing new uart1.dtbo.
Taking backup of rpi-digiampplus.dtbo.
Installing new rpi-digiampplus.dtbo.
Taking backup of vl805.dtbo.
Installing new vl805.dtbo.
Taking backup of pifi-mini-210.dtbo.
Installing new pifi-mini-210.dtbo.
Taking backup of i2c-rtc.dtbo.
Installing new i2c-rtc.dtbo.
Taking backup of imx519.dtbo.
Installing new imx519.dtbo.
Taking backup of rpi-poe-plus.dtbo.
Installing new rpi-poe-plus.dtbo.
Taking backup of at86rf233.dtbo.
Installing new at86rf233.dtbo.
Taking backup of vc4-kms-dsi-7inch.dtbo.
Installing new vc4-kms-dsi-7inch.dtbo.
Taking backup of interludeaudio-digital.dtbo.
Installing new interludeaudio-digital.dtbo.
Taking backup of mlx90640.dtbo.
Installing new mlx90640.dtbo.
Taking backup of exc3000.dtbo.
Installing new exc3000.dtbo.
Taking backup of edt-ft5406.dtbo.
Installing new edt-ft5406.dtbo.
Taking backup of papirus.dtbo.
Installing new papirus.dtbo.
Taking backup of iqaudio-digi-wm8804-audio.dtbo.
Installing new iqaudio-digi-wm8804-audio.dtbo.
Taking backup of hy28b.dtbo.
Installing new hy28b.dtbo.
Taking backup of googlevoicehat-soundcard.dtbo.
Installing new googlevoicehat-soundcard.dtbo.
Taking backup of uart2-pi5.dtbo.
Installing new uart2-pi5.dtbo.
Taking backup of hifiberry-dacplushd.dtbo.
Installing new hifiberry-dacplushd.dtbo.
Taking backup of seeed-can-fd-hat-v2.dtbo.
Installing new seeed-can-fd-hat-v2.dtbo.
Taking backup of gpio-shutdown.dtbo.
Installing new gpio-shutdown.dtbo.
Taking backup of spi-gpio40-45.dtbo.
Installing new spi-gpio40-45.dtbo.
Taking backup of akkordion-iqdacplus.dtbo.
Installing new akkordion-iqdacplus.dtbo.
Taking backup of vc4-kms-kippah-7inch.dtbo.
Installing new vc4-kms-kippah-7inch.dtbo.
Taking backup of sh1106-spi.dtbo.
Installing new sh1106-spi.dtbo.
Taking backup of dacberry400.dtbo.
Installing new dacberry400.dtbo.
Taking backup of w1-gpio-pullup-pi5.dtbo.
Installing new w1-gpio-pullup-pi5.dtbo.
Taking backup of mcp3202.dtbo.
Installing new mcp3202.dtbo.
Taking backup of spi0-0cs.dtbo.
Installing new spi0-0cs.dtbo.
Taking backup of mbed-dac.dtbo.
Installing new mbed-dac.dtbo.
Taking backup of adafruit18.dtbo.
Installing new adafruit18.dtbo.
Taking backup of wm8960-soundcard.dtbo.
Installing new wm8960-soundcard.dtbo.
Taking backup of proto-codec.dtbo.
Installing new proto-codec.dtbo.
Taking backup of spi6-2cs.dtbo.
Installing new spi6-2cs.dtbo.
Taking backup of vc4-fkms-v3d-pi4.dtbo.
Installing new vc4-fkms-v3d-pi4.dtbo.
Taking backup of imx477.dtbo.
Installing new imx477.dtbo.
Taking backup of vc4-kms-dsi-ili9881-7inch.dtbo.
Installing new vc4-kms-dsi-ili9881-7inch.dtbo.
Taking backup of i2s-dac.dtbo.
Installing new i2s-dac.dtbo.
Taking backup of pifi-dac-zero.dtbo.
Installing new pifi-dac-zero.dtbo.
Taking backup of ghost-amp.dtbo.
Installing new ghost-amp.dtbo.
Taking backup of spi3-2cs.dtbo.
Installing new spi3-2cs.dtbo.
Taking backup of iqs550.dtbo.
Installing new iqs550.dtbo.
Taking backup of w1-gpio-pi5.dtbo.
Installing new w1-gpio-pi5.dtbo.
Taking backup of spi6-1cs.dtbo.
Installing new spi6-1cs.dtbo.
Taking backup of tpm-slb9670.dtbo.
Installing new tpm-slb9670.dtbo.
Taking backup of sdhost.dtbo.
Installing new sdhost.dtbo.
Taking backup of ramoops-pi4.dtbo.
Installing new ramoops-pi4.dtbo.
Taking backup of hifiberry-dacplusadc.dtbo.
Installing new hifiberry-dacplusadc.dtbo.
Taking backup of spi0-2cs.dtbo.
Installing new spi0-2cs.dtbo.
Taking backup of pcie-32bit-dma.dtbo.
Installing new pcie-32bit-dma.dtbo.
Taking backup of spi2-2cs-pi5.dtbo.
Installing new spi2-2cs-pi5.dtbo.
Taking backup of rpi-dacplus.dtbo.
Installing new rpi-dacplus.dtbo.
Taking backup of spi3-1cs.dtbo.
Installing new spi3-1cs.dtbo.
Taking backup of smi-nand.dtbo.
Installing new smi-nand.dtbo.
Taking backup of pitft28-resistive.dtbo.
Installing new pitft28-resistive.dtbo.
Taking backup of gpio-charger.dtbo.
Installing new gpio-charger.dtbo.
Taking backup of pisound.dtbo.
Installing new pisound.dtbo.
Taking backup of justboom-digi.dtbo.
Installing new justboom-digi.dtbo.
Taking backup of dwc2.dtbo.
Installing new dwc2.dtbo.
Taking backup of spi0-1cs.dtbo.
Installing new spi0-1cs.dtbo.
Taking backup of hifiberry-dac8x.dtbo.
Installing new hifiberry-dac8x.dtbo.
Taking backup of gpio-ir.dtbo.
Installing new gpio-ir.dtbo.
Taking backup of uart5.dtbo.
Installing new uart5.dtbo.
Taking backup of ltc294x.dtbo.
Installing new ltc294x.dtbo.
Taking backup of hifiberry-amp4pro.dtbo.
Installing new hifiberry-amp4pro.dtbo.
Taking backup of ssd1351-spi.dtbo.
Installing new ssd1351-spi.dtbo.
Taking backup of hifiberry-dacplusadcpro.dtbo.
Installing new hifiberry-dacplusadcpro.dtbo.
Taking backup of hifiberry-amp100.dtbo.
Installing new hifiberry-amp100.dtbo.
Taking backup of apds9960.dtbo.
Installing new apds9960.dtbo.
Taking backup of tc358743.dtbo.
Installing new tc358743.dtbo.
Taking backup of pcf857x.dtbo.
Installing new pcf857x.dtbo.
Taking backup of sc16is750-i2c.dtbo.
Installing new sc16is750-i2c.dtbo.
Taking backup of mcp251xfd.dtbo.
Installing new mcp251xfd.dtbo.
Taking backup of uart0.dtbo.
Installing new uart0.dtbo.
Taking backup of midi-uart2.dtbo.
Installing new midi-uart2.dtbo.
Taking backup of disable-bt-pi5.dtbo.
Installing new disable-bt-pi5.dtbo.
Taking backup of i2c1-pi5.dtbo.
Installing new i2c1-pi5.dtbo.
Taking backup of allo-piano-dac-plus-pcm512x-audio.dtbo.
Installing new allo-piano-dac-plus-pcm512x-audio.dtbo.
Taking backup of pwm.dtbo.
Installing new pwm.dtbo.
Taking backup of disable-wifi.dtbo.
Installing new disable-wifi.dtbo.
Taking backup of sdio.dtbo.
Installing new sdio.dtbo.
Taking backup of applepi-dac.dtbo.
Installing new applepi-dac.dtbo.
Taking backup of midi-uart3-pi5.dtbo.
Installing new midi-uart3-pi5.dtbo.
Taking backup of vc4-kms-dpi-panel.dtbo.
Installing new vc4-kms-dpi-panel.dtbo.
Taking backup of mcp23s17.dtbo.
Installing new mcp23s17.dtbo.
Taking backup of vc4-kms-dpi-hyperpixel4sq.dtbo.
Installing new vc4-kms-dpi-hyperpixel4sq.dtbo.
Taking backup of crystalfontz-cfa050_pi_m.dtbo.
Installing new crystalfontz-cfa050_pi_m.dtbo.
Taking backup of rotary-encoder.dtbo.
Installing new rotary-encoder.dtbo.
Taking backup of fbtft.dtbo.
Installing new fbtft.dtbo.
Taking backup of qca7000-uart0.dtbo.
Installing new qca7000-uart0.dtbo.
Taking backup of mipi-dbi-spi.dtbo.
Installing new mipi-dbi-spi.dtbo.
Taking backup of hy28a.dtbo.
Installing new hy28a.dtbo.
Taking backup of hifiberry-dacplusdsp.dtbo.
Installing new hifiberry-dacplusdsp.dtbo.
Taking backup of imx296.dtbo.
Installing new imx296.dtbo.
Taking backup of i2c-pwm-pca9685a.dtbo.
Installing new i2c-pwm-pca9685a.dtbo.
Taking backup of audioinjector-ultra.dtbo.
Installing new audioinjector-ultra.dtbo.
Taking backup of i2c6.dtbo.
Installing new i2c6.dtbo.
Taking backup of seeed-can-fd-hat-v1.dtbo.
Installing new seeed-can-fd-hat-v1.dtbo.
Taking backup of enc28j60-spi2.dtbo.
Installing new enc28j60-spi2.dtbo.
Taking backup of spi-gpio35-39.dtbo.
Installing new spi-gpio35-39.dtbo.
Taking backup of smi-dev.dtbo.
Installing new smi-dev.dtbo.
Taking backup of i2c1.dtbo.
Installing new i2c1.dtbo.
Taking backup of justboom-dac.dtbo.
Installing new justboom-dac.dtbo.
Taking backup of superaudioboard.dtbo.
Installing new superaudioboard.dtbo.
Taking backup of tinylcd35.dtbo.
Installing new tinylcd35.dtbo.
Taking backup of mcp23017.dtbo.
Installing new mcp23017.dtbo.
Taking backup of camera-mux-2port.dtbo.
Installing new camera-mux-2port.dtbo.
Taking backup of spi5-1cs-pi5.dtbo.
Installing new spi5-1cs-pi5.dtbo.
Taking backup of cm-swap-i2c0.dtbo.
Installing new cm-swap-i2c0.dtbo.
Taking backup of sx150x.dtbo.
Installing new sx150x.dtbo.
Taking backup of sc16is752-spi1.dtbo.
Installing new sc16is752-spi1.dtbo.
Taking backup of hifiberry-amp.dtbo.
Installing new hifiberry-amp.dtbo.
Taking backup of upstream-pi4.dtbo.
Installing new upstream-pi4.dtbo.
Taking backup of vc4-kms-vga666.dtbo.
Installing new vc4-kms-vga666.dtbo.
Taking backup of tc358743-audio.dtbo.
Installing new tc358743-audio.dtbo.
Taking backup of gpio-poweroff.dtbo.
Installing new gpio-poweroff.dtbo.
Taking backup of overlay_map.dtb.
Installing new overlay_map.dtb.
Taking backup of piscreen2r.dtbo.
Installing new piscreen2r.dtbo.
Taking backup of vc4-kms-dpi-hyperpixel2r.dtbo.
Installing new vc4-kms-dpi-hyperpixel2r.dtbo.
Taking backup of arducam-64mp.dtbo.
Installing new arducam-64mp.dtbo.
Taking backup of ilitek251x.dtbo.
Installing new ilitek251x.dtbo.
Taking backup of allo-digione.dtbo.
Installing new allo-digione.dtbo.
Taking backup of dionaudio-loco.dtbo.
Installing new dionaudio-loco.dtbo.
Taking backup of i2c-rtc-gpio.dtbo.
Installing new i2c-rtc-gpio.dtbo.
Taking backup of hy28b-2017.dtbo.
Installing new hy28b-2017.dtbo.
Taking backup of qca7000.dtbo.
Installing new qca7000.dtbo.
Taking backup of midi-uart0-pi5.dtbo.
Installing new midi-uart0-pi5.dtbo.
Taking backup of gpio-fan.dtbo.
Installing new gpio-fan.dtbo.
Taking backup of vc4-kms-dsi-generic.dtbo.
Installing new vc4-kms-dsi-generic.dtbo.
Taking backup of miniuart-bt.dtbo.
Installing new miniuart-bt.dtbo.
Taking backup of uart4.dtbo.
Installing new uart4.dtbo.
Taking backup of pwm-2chan.dtbo.
Installing new pwm-2chan.dtbo.
Taking backup of ssd1331-spi.dtbo.
Installing new ssd1331-spi.dtbo.
Taking backup of vc4-kms-dpi-generic.dtbo.
Installing new vc4-kms-dpi-generic.dtbo.
Taking backup of spi4-2cs.dtbo.
Installing new spi4-2cs.dtbo.
Taking backup of spi3-2cs-pi5.dtbo.
Installing new spi3-2cs-pi5.dtbo.
Taking backup of smi.dtbo.
Installing new smi.dtbo.
Taking backup of midi-uart1.dtbo.
Installing new midi-uart1.dtbo.
Taking backup of act-led.dtbo.
Installing new act-led.dtbo.
Taking backup of gpio-hog.dtbo.
Installing new gpio-hog.dtbo.
Taking backup of vc4-kms-dsi-lt070me05000-v2.dtbo.
Installing new vc4-kms-dsi-lt070me05000-v2.dtbo.
Taking backup of mcp2515-can1.dtbo.
Installing new mcp2515-can1.dtbo.
Taking backup of spi4-1cs.dtbo.
Installing new spi4-1cs.dtbo.
Taking backup of gc9a01.dtbo.
Installing new gc9a01.dtbo.
Taking backup of i2c-sensor.dtbo.
Installing new i2c-sensor.dtbo.
Taking backup of bcm2712d0.dtbo.
Installing new bcm2712d0.dtbo.
Taking backup of rpi-poe.dtbo.
Installing new rpi-poe.dtbo.
Taking backup of spi1-1cs.dtbo.
Installing new spi1-1cs.dtbo.
Taking backup of upstream.dtbo.
Installing new upstream.dtbo.
Taking backup of rpi-backlight.dtbo.
Installing new rpi-backlight.dtbo.
Taking backup of chipdip-dac.dtbo.
Installing new chipdip-dac.dtbo.
Taking backup of uart3-pi5.dtbo.
Installing new uart3-pi5.dtbo.
Taking backup of i2c5.dtbo.
Installing new i2c5.dtbo.
Taking backup of pwm-ir-tx.dtbo.
Installing new pwm-ir-tx.dtbo.
Taking backup of allo-boss2-dac-audio.dtbo.
Installing new allo-boss2-dac-audio.dtbo.
Taking backup of dht11.dtbo.
Installing new dht11.dtbo.
Taking backup of uart0-pi5.dtbo.
Installing new uart0-pi5.dtbo.
Taking backup of ov7251.dtbo.
Installing new ov7251.dtbo.
Taking backup of disable-emmc2.dtbo.
Installing new disable-emmc2.dtbo.
Taking backup of gpio-no-bank0-irq.dtbo.
Installing new gpio-no-bank0-irq.dtbo.
Taking backup of hifiberry-dacplus.dtbo.
Installing new hifiberry-dacplus.dtbo.
Taking backup of i2c0.dtbo.
Installing new i2c0.dtbo.
Taking backup of cirrus-wm5102.dtbo.
Installing new cirrus-wm5102.dtbo.
Taking backup of gpio-led.dtbo.
Installing new gpio-led.dtbo.
Taking backup of ramoops.dtbo.
Installing new ramoops.dtbo.
Taking backup of i2c2-pi5.dtbo.
Installing new i2c2-pi5.dtbo.
Taking backup of wittypi.dtbo.
Installing new wittypi.dtbo.
Taking backup of w1-gpio.dtbo.
Installing new w1-gpio.dtbo.
Taking backup of i2s-gpio28-31.dtbo.
Installing new i2s-gpio28-31.dtbo.
Taking backup of sainsmart18.dtbo.
Installing new sainsmart18.dtbo.
Taking backup of tpm-slb9673.dtbo.
Installing new tpm-slb9673.dtbo.
Taking backup of justboom-both.dtbo.
Installing new justboom-both.dtbo.
Taking backup of pifi-dac-hd.dtbo.
Installing new pifi-dac-hd.dtbo.
Taking backup of audioinjector-addons.dtbo.
Installing new audioinjector-addons.dtbo.
Taking backup of spi1-3cs.dtbo.
Installing new spi1-3cs.dtbo.
Taking backup of pitft28-capacitive.dtbo.
Installing new pitft28-capacitive.dtbo.
Taking backup of audioinjector-wm8731-audio.dtbo.
Installing new audioinjector-wm8731-audio.dtbo.
Taking backup of irs1125.dtbo.
Installing new irs1125.dtbo.
Taking backup of sc16is752-spi0.dtbo.
Installing new sc16is752-spi0.dtbo.
Taking backup of vc4-kms-dsi-ili9881-5inch.dtbo.
Installing new vc4-kms-dsi-ili9881-5inch.dtbo.
Taking backup of spi1-2cs.dtbo.
Installing new spi1-2cs.dtbo.
Taking backup of pisound-pi5.dtbo.
Installing new pisound-pi5.dtbo.
Taking backup of hdmi-backlight-hwhack-gpio.dtbo.
Installing new hdmi-backlight-hwhack-gpio.dtbo.
Taking backup of fe-pi-audio.dtbo.
Installing new fe-pi-audio.dtbo.
Taking backup of i2c-bcm2708.dtbo.
Installing new i2c-bcm2708.dtbo.
Taking backup of pifi-40.dtbo.
Installing new pifi-40.dtbo.
Taking backup of hd44780-lcd.dtbo.
Installing new hd44780-lcd.dtbo.
Taking backup of vc4-kms-v3d.dtbo.
Installing new vc4-kms-v3d.dtbo.
Taking backup of fsm-demo.dtbo.
Installing new fsm-demo.dtbo.
Taking backup of adau1977-adc.dtbo.
Installing new adau1977-adc.dtbo.
Taking backup of si446x-spi0.dtbo.
Installing new si446x-spi0.dtbo.
Taking backup of waveshare-can-fd-hat-mode-b.dtbo.
Installing new waveshare-can-fd-hat-mode-b.dtbo.
Taking backup of rra-digidac1-wm8741-audio.dtbo.
Installing new rra-digidac1-wm8741-audio.dtbo.
Taking backup of maxtherm.dtbo.
Installing new maxtherm.dtbo.
Taking backup of uart3.dtbo.
Installing new uart3.dtbo.
Taking backup of midi-uart5.dtbo.
Installing new midi-uart5.dtbo.
Taking backup of hifiberry-digi-pro.dtbo.
Installing new hifiberry-digi-pro.dtbo.
Taking backup of imx290.dtbo.
Installing new imx290.dtbo.
Taking backup of cutiepi-panel.dtbo.
Installing new cutiepi-panel.dtbo.
Taking backup of audioinjector-isolated-soundcard.dtbo.
Installing new audioinjector-isolated-soundcard.dtbo.
Taking backup of midi-uart0.dtbo.
Installing new midi-uart0.dtbo.
Taking backup of rpi-tv.dtbo.
Installing new rpi-tv.dtbo.
Taking backup of disable-wifi-pi5.dtbo.
Installing new disable-wifi-pi5.dtbo.
Taking backup of i2c-fan.dtbo.
Installing new i2c-fan.dtbo.
Taking backup of anyspi.dtbo.
Installing new anyspi.dtbo.
Taking backup of mcp2515-can0.dtbo.
Installing new mcp2515-can0.dtbo.
Taking backup of midi-uart4-pi5.dtbo.
Installing new midi-uart4-pi5.dtbo.
Taking backup of ugreen-dabboard.dtbo.
Installing new ugreen-dabboard.dtbo.
Taking backup of gpio-ir-tx.dtbo.
Installing new gpio-ir-tx.dtbo.
Taking backup of adv728x-m.dtbo.
Installing new adv728x-m.dtbo.
Taking backup of dpi18.dtbo.
Installing new dpi18.dtbo.
Taking backup of enc28j60.dtbo.
Installing new enc28j60.dtbo.
Taking backup of pifacedigital.dtbo.
Installing new pifacedigital.dtbo.
Taking backup of audiosense-pi.dtbo.
Installing new audiosense-pi.dtbo.
Taking backup of midi-uart1-pi5.dtbo.
Installing new midi-uart1-pi5.dtbo.
Taking backup of vc4-kms-v3d-pi5.dtbo.
Installing new vc4-kms-v3d-pi5.dtbo.
Taking backup of mcp3008.dtbo.
Installing new mcp3008.dtbo.
Taking backup of iqaudio-dac.dtbo.
Installing new iqaudio-dac.dtbo.
Taking backup of vc4-fkms-v3d.dtbo.
Installing new vc4-fkms-v3d.dtbo.
Taking backup of rpi-sense.dtbo.
Installing new rpi-sense.dtbo.
Taking backup of spi2-1cs-pi5.dtbo.
Installing new spi2-1cs-pi5.dtbo.
Taking backup of merus-amp.dtbo.
Installing new merus-amp.dtbo.
Taking backup of ads1115.dtbo.
Installing new ads1115.dtbo.
Taking backup of ssd1306.dtbo.
Installing new ssd1306.dtbo.
Taking backup of i2c4.dtbo.
Installing new i2c4.dtbo.
Taking backup of allo-boss-dac-pcm512x-audio.dtbo.
Installing new allo-boss-dac-pcm512x-audio.dtbo.
Taking backup of iqaudio-codec.dtbo.
Installing new iqaudio-codec.dtbo.
Taking backup of w1-gpio-pullup.dtbo.
Installing new w1-gpio-pullup.dtbo.
Taking backup of mmc.dtbo.
Installing new mmc.dtbo.
Taking backup of interludeaudio-analog.dtbo.
Installing new interludeaudio-analog.dtbo.
Taking backup of ads1015.dtbo.
Installing new ads1015.dtbo.
Taking backup of max98357a.dtbo.
Installing new max98357a.dtbo.
Taking backup of imx708.dtbo.
Installing new imx708.dtbo.
Taking backup of spi5-1cs.dtbo.
Installing new spi5-1cs.dtbo.
Taking backup of pca953x.dtbo.
Installing new pca953x.dtbo.
Taking backup of mz61581.dtbo.
Installing new mz61581.dtbo.
Taking backup of pwm1.dtbo.
Installing new pwm1.dtbo.
Taking backup of iqaudio-dacplus.dtbo.
Installing new iqaudio-dacplus.dtbo.
Taking backup of hifiberry-dac.dtbo.
Installing new hifiberry-dac.dtbo.
Taking backup of spi-rtc.dtbo.
Installing new spi-rtc.dtbo.
Taking backup of spi2-1cs.dtbo.
Installing new spi2-1cs.dtbo.
Taking backup of cap1106.dtbo.
Installing new cap1106.dtbo.
Taking backup of w5500.dtbo.
Installing new w5500.dtbo.
Taking backup of uart4-pi5.dtbo.
Installing new uart4-pi5.dtbo.
Taking backup of minipitft13.dtbo.
Installing new minipitft13.dtbo.
Taking backup of dionaudio-kiwi.dtbo.
Installing new dionaudio-kiwi.dtbo.
Taking backup of uart1-pi5.dtbo.
Installing new uart1-pi5.dtbo.
Taking backup of README.
Installing new README.
Scanning processes...
Scanning candidates...
Scanning processor microcode...
Scanning linux images...

Pending kernel upgrade!
Running kernel version:
  6.8.0-1047-raspi
Diagnostics:
  The currently running kernel version is not the expected kernel version 6.8.0-1056-raspi.

Restarting the system to load the new kernel will not be handled automatically, so you should consider rebooting.

The processor microcode seems to be up-to-date.

Restarting services...
 systemctl restart avahi-daemon.service fwupd.service netplan-wpa-wlan0.service rsyslog.service udisks2.service

Service restarts being deferred:
 /etc/needrestart/restart.d/dbus.service
 systemctl restart getty@tty1.service
 systemctl restart serial-getty@ttyS0.service
 systemctl restart systemd-logind.service
 systemctl restart unattended-upgrades.service
 systemctl restart wpa_supplicant.service

No containers need to be restarted.

User sessions running outdated binaries:
 sona @ session #2: apt[3029], sshd[2321]
 sona @ user manager service: systemd[2326]

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

</details>


## Ubuntu Version

```bash
lsb_release -a
```

```bash
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.4 LTS
Release:        24.04
Codename:       noble
```

## Resource Monitor

```bash
htop
```

## Network interfaces

list interfaces name and addresses, the names are used in netplan, and change depending on hardware and driver. should be consistent on raspberries

```ip a```

```bash
sona@rpi4-orso-sda:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether e4:5f:01:7f:ef:3e brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.220/23 metric 100 brd 192.168.1.255 scope global dynamic eth0
       valid_lft 97526sec preferred_lft 97526sec
    inet6 fe80::e65f:1ff:fe7f:ef3e/64 scope link
       valid_lft forever preferred_lft forever
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether e4:5f:01:7f:ef:3f brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.240/23 metric 600 brd 192.168.1.255 scope global dynamic wlan0
       valid_lft 97728sec preferred_lft 97728sec
    inet6 fe80::e65f:1ff:fe7f:ef3f/64 scope link
       valid_lft forever preferred_lft forever
```

## Netplan

This is the bane of ubuntu, an incredibly brittle config network file that will brick any headless SBC if anything goes wrong, and can stall for minutes the boot

```optional``` if this is true for ALL interfaces, it'll skip the dumb delay at ubnuntu startup that takes 5 minutes.

### Visualize netplan

```bash
sudo cat /etc/netplan/50-cloud-init.yaml
```

```yaml
network:
  version: 2
  ethernets:
    eth0:
      optional: true
      dhcp4: true
      dhcp6: true
  wifis:
    wlan0:
      optional: true
      dhcp4: true
      regulatory-domain: "IT"
      access-points:
        "SSID":
          auth:
            key-management: "psk"
            password: "PWD"
```

### netplan static IP

```yaml
  ethernets:
    eth0:
      optional: true
      dhcp4: false
      addresses:
        - 192.168.0.200/24
```

## test netplan before bricking

This tells you lots of things

```bash
sudo netplan --debug generate
```

This tries and rolls back afrer 3 minutes, but doesn't tell you

```bash
sudo netplan try
```

This is dangerous:

```bash
sudo netplan apply
```

## APT SOURCE 

Another bane of ubuntu is the apt source, that too changes constantly and bricks many guides you try to follow. [E.g. ROS2 guide was bricked because of a apt source changed](https://github.com/ros2/ros2_documentation/issues/6853) 

There are two config files for apt source, both work, and don't.

### /etc/apt/sources.list.d/ubuntu.sources

As far as I can tell, this is the desirable apt source

```bash
cat /etc/apt/sources.list.d/ubuntu.sources
```

```bash
sona@rpi-orso-sdb:~/2026-05-29-picamera2$ cat /etc/apt/sources.list.d/ubuntu.sources
## Ubuntu distribution repository
##
## The following settings can be adjusted to configure which packages to use from Ubuntu.
## Mirror your choices (except for URIs and Suites) in the security section below to
## ensure timely security updates.
##
## Types: Append deb-src to enable the fetching of source package.
## URIs: A URL to the repository (you may add multiple URLs)
## Suites: The following additional suites can be configured
##   <name>-updates   - Major bug fix updates produced after the final release of the
##                      distribution.
##   <name>-backports - software from this repository may not have been tested as
##                      extensively as that contained in the main release, although it includes
##                      newer versions of some applications which may provide useful features.
##                      Also, please note that software in backports WILL NOT receive any review
##                      or updates from the Ubuntu security team.
## Components: Aside from main, the following components can be added to the list
##   restricted  - Software that may not be under a free license, or protected by patents.
##   universe    - Community maintained packages. Software in this repository receives maintenance
##                 from volunteers in the Ubuntu community, or a 10 year security maintenance
##                 commitment from Canonical when an Ubuntu Pro subscription is attached.
##   multiverse  - Community maintained of restricted. Software from this repository is
##                 ENTIRELY UNSUPPORTED by the Ubuntu team, and may not be under a free
##                 licence. Please satisfy yourself as to your rights to use the software.
##                 Also, please note that software in multiverse WILL NOT receive any
##                 review or updates from the Ubuntu security team.
##
## See the sources.list(5) manual page for further settings.
Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

## Ubuntu security updates. Aside from URIs and Suites,
## this should mirror your choices in the previous section.
Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```

### /etc/apt/sources.list

As far as I can tell, this second source works poorly and it's better to comment it if uncommented

```bash
cat /etc/apt/sources.list
```

```bash
(.venv) sona@rpi-orso-sdb:~/2026-05-29-picamera2$ cat /etc/apt/sources.list
#deb http://ports.ubuntu.com/ubuntu-ports noble main restricted universe multiverse
#deb http://ports.ubuntu.com/ubuntu-ports noble-updates main restricted universe multiverse
#deb http://ports.ubuntu.com/ubuntu-ports noble-security main restricted universe multiverse
#deb http://ports.ubuntu.com/ubuntu-ports noble-backports main restricted universe multiverse
```

### Fix Broken Apt Dependency Error by Adding more Sources

For reason I do not understand, when installed, the source will just list ```Suites: noble```

But so many packages will require more sources, and fail with oblique errors that will not lead you to apt source. E.g. below you see that apt tells you something is broken. But the true problem is that apt sources are missing in ubuntu by default.

```bash
The following packages have unmet dependencies:
 libidn2-dev : Depends: libidn2-0 (= 2.3.7-2build1) but 2.3.7-2build1.1 is to be installed
 libp11-kit-dev : Depends: libp11-kit0 (= 0.25.3-4ubuntu2) but 0.25.3-4ubuntu2.1 is to be installed
 libzstd-dev : Depends: libzstd1 (= 1.5.5+dfsg2-2build1) but 1.5.5+dfsg2-2build1.1 is to be installed
 nettle-dev : Depends: libnettle8t64 (= 3.9.1-2.2build1) but 3.9.1-2.2build1.1 is to be installed
              Depends: libhogweed6t64 (= 3.9.1-2.2build1) but 3.9.1-2.2build1.1 is to be installed
              Depends: libgmp-dev but it is not going to be installed
 zlib1g-dev : Depends: zlib1g (= 1:1.3.dfsg-3.1ubuntu2) but 1:1.3.dfsg-3.1ubuntu2.1 is to be installed
E: Unable to correct problems, you have held broken packages.
```

I found some useful addition to fix some of the bricked apt packages are: ```Suites: noble noble-updates noble-backports```

**You must edit this by hand to add more apt sources**

```bash
sudo nano /etc/apt/sources.list.d/ubuntu.sources

#add noble-updates noble-backports

sudo apt update 

sudo apt full-upgrade

sudo reboot
```

Then if you try to install the packages, this time it should work


# APPLICATIONS

## PYTHON UV

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh

source $HOME/.local/bin/env 

uv venv .venv --python 3.13

source .venv/bin/activate
```


# VS Code Remote

It's very convenient to use the ```remote``` plugin of VS Code to connect headless to the raspberry Pi via SSH and develop from a host machine, while running code on the raspberry

## Install VS Code Remote Plugin

![](/images/Screenshot%202026-05-30%20074737%20VS%20Code%20Remote.png)

## Connect to Raspberry

A new icon appear bottom left ```><```

![](/images/Screenshot%202026-05-30%20074801%20bottom%20left%20remote%20icon.png)

Top center appear a dropdown. Select connect host same window, otherwise will open a new VS Code window

![](images/Screenshot%202026-05-30%20074925%20top%20center%20dropdown%20-%20select%20connect%20host%20same%20window.png)

connect ```user@ipaddress```

![](/images/Screenshot%202026-05-30%20074816%20user%20ip.png)

select OS of remote machine, for the raspberry ubuntu it's linux

![](/images/Screenshot%202026-05-30%20075009%20select%20os.png)

confirm fingerprint and  type password of remote machine

![](/images/Screenshot%202026-05-30%20075147%20fingerprint%20continue.png)

![](/images/Screenshot%202026-05-30%20075158%20type%20remote%20password.png)

Now VS Code is connected with remote machine, and terminal is the remote terminal, letting you run all commands like with ```putty```

![](/images/Screenshot%202026-05-30%20075400%20connected.png)

## Git Clone and Run Code

VS Code is connected but no particular project folders are opened

It's convenient to create a project in github, git clone and sync. This way the work done in the remote machine is going to be conveniently backed up on github for replication

First git clone a repo on the terminal. E.g.

```git clone https://github.com/OrsoEric/HowTo-Ubuntu24S-Raspicam-Webserver-Streaming.git```

![](/images/Screenshot%202026-05-30%20075532%20git%20clone%20project.png)

Next open the folder like you would normally do, VS Code will list all remote folders

![](/images/Screenshot%202026-05-30%20075542%20open%20project%20folder.png)

![](/images/Screenshot%202026-05-30%20075618%20connected%20to%20remote%20folder.png)

## Install Remote extensions on RaspberryPi

VS Code has installed a VS Code server on the remote, you need to add plugins there as well. E.g. it's convenient to add ```gitgraph```

![](/images/Screenshot%202026-05-30%20081357%20install%20gitgraph.png)

![](/images/Screenshot%202026-05-30%20081624%20remote%20git.png)

From gitgraph it's easy to setup the user details that will sign the commit

![](/images/Screenshot%202026-05-30%20081825%20gitgtaph%20gear.png)
![](/images/Screenshot%202026-05-30%20081814%20gitgraph%20set%20user.png)

Now you can commit from the raspberry pi remote machine to github and have your work backed up, so you can git clone it on another machine

![](/images/Screenshot%202026-05-30%20082200%20first%20commit.png)

![](/images/Screenshot%202026-05-30%20082221%20remote%20sync.png)

![](/images/Screenshot%202026-05-30%20082248%20github.png)

## Expand Terminal 

The default terminal is too short for apt commands and the likes to make terminal longer go into settings and expand to 10000

![](/images/Screenshot%202026-05-30%20102002%20settings.png)

![](/images/Screenshot%202026-05-30%20101944%20expand%20scrollback.png)

# File Transfer from Raspberry to Host

Simplest way is to use [Filezilla](https://filezilla-project.org/download.php?show_all=1&type=server), with ftp over ssh sftp protocol

![](/images/Screenshot%202026-05-30%20112438filezilla%20connect.png)

![](/images/Screenshot%202026-05-30%20112459%20connect.png)

![](/images/Screenshot%202026-05-30%20112512%20raspberry%20files.png)

You can just drag and drop files from right (raspberry) to left (host) and vice versa. Very convenient and without setting up a file server since it uses SSH.

# EOL

<details>
<summary>xxx</summary>

```cmd
xxx
```

</details>