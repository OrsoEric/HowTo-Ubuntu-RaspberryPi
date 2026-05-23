# HowTo UBUNTU 24 RPI4

Instruction how to make Ubuntu run on Raspberry Pi headless

Ubuntu's netplan makes this extremely difficult

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

None of the three ways are reliable, headless booting of Ubuntu into Raspberry is almost impossible because of the cloud init that brick it constantly

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

![](/images/Screenshot%202026-05-23%20123012.png)

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

# APPLICATIONS

## PYTHON UV

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh

source $HOME/.local/bin/env 

uv venv .venv --python 3.13

source .venv/bin/activate
```


# EOL

<details>
<summary>xxx</summary>

```cmd
xxx
```

</details>