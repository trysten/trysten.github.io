---
layout: post
title: SOHO router tutorial
categories: debian

---
* table of contents
{:toc}

This is a documentation of the steps to configure a debian system as a router. Assuming the device has two or more network ports, it provides NAT, firewalling/forwarding, DHCP, and DNS forwarding.

### debian
Assuming you have an up-to-date debian or close derivative (raspbian), you'll need to install the necessary software. Mark packages [`firewalld`](https://packages.debian.org/bullseye/firewalld) [`dnsmaq`](https://packages.debian.org/bullseye/dnsmasq) for installation.
#### ip
I like to set aliases for the network devices so I don't need to remember which is which. Internal/External to correspond to the firewalld zones.
`ip link set <device> alias internal`
`ip link set <other_device> alias external`
### firewalld
#### Zones
    we need to change the zones

#### NAT
Here's a recipe I found for NAT.
```
firewall-cmd --direct --add-rule ipv4 nat POSTROUTING 0 -o eth_ext -j MASQUERADE
firewall-cmd --direct --add-rule ipv4 filter FORWARD 0 -i eth_int -o eth_ext -j ACCEPT
firewall-cmd --direct --add-rule ipv4 filter FORWARD 0 -i eth_ext -o eth_int -m state --state RELATED,ESTABLISHED -j ACCEPT
```
Converted to firewall-cmd switches:
```
firewall-cmd
```
##### Port Forwards
###### firewalld.service
    <?xml version="1.0" encoding="utf-8"?>
    <service>
    <short>My Service</short>
    <description>description</description>
    <port port="137" protocol="tcp"/>
    <protocol value="igmp"/>
    <module name="nf_conntrack_netbios_ns"/>
    <destination ipv4="224.0.0.251" ipv6="ff02::fb"/>
    <include service="ssdp"/>
    <helper name="ftp"/>
    </service>
### dnsmasq
##### DNSSEC
