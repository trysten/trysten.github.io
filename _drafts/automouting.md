---
layout: post
title: Use systemd to automount webdav sites
categories: systemd webdav debian

---

You may have read somewhere about systemd's automount units. This is a guide to "writing" your own units to mount davfs2 shares, assuming you have edited fstab before. Debian comes with [systemd-fstab-generator](https://manpages.debian.org/stretch/systemd/systemd-fstab-generator.8.en.html) that will generate units for us, so we just need to write a line in [/etc/fstab](https://manpages.debian.org/stretch/mount/fstab.5.en.html).
## Configuration
### /etc/fstab
Including `x-systemd-automount` option in fstab entries causes automount units to be generated along with the normal mount units. A line that looks like the following is all you need.

`https://webdav.example.com /home/<user>/mnt/example.com davfs user,noauto,uid=<user>,x-systemd-automount,x-systemd.idle-timeout=5m 0 0`

## Authentication
### /etc/davfs/secrets
Using systemd's automount feature necessitates storing passwords in the system-wide configuration. Investigating this drawback, I found that davfs2 can use a per-user secrets file, but only when running as a non-root user. Specifying a UID the system-wide configuration doesn't cause that users configuration to be read. I looked pretty hard for some way to keep the secrets file in my user directory, but couldn't find an elegant solution that kept a normal systemd automounting configuration. So, add a line to `/etc/davfs2/secrets`:

`/home/<user>/mnt/example.com <webdav user> <webdav password>`

Make sure you enter the username accurately. There's no way to encrypt the password file for davfs use, so this setup is only as secure as your /etc

[oguya]: <https://oguya.ch/posts/2015-09-01-systemd-mount-partition/> "Mounting Partition Using Systemd" 
