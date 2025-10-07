---
layout: post
title: Use systemd to automount webDAV sites
categories: systemd webdav debian
tags: explanation
---
*update: I no longer use this method, preferring userland tools. See [rclone](https://rclone.org)*

You may have heard about about systemd's automount units. This is a guide to "writing" your own units to mount davfs2 shares that assumes you know how to edit fstab. Debian and other systemd distros come with [`systemd-fstab-generator`](https://manpages.debian.org/stretch/systemd/systemd-fstab-generator.8.en.html) that will generate units for us, so we just need to write a line in [`/etc/fstab`](https://manpages.debian.org/stretch/mount/fstab.5.en.html).
## Configuration
### /etc/fstab
| keyword | description|
|---------|------------|
`user` | may only come in handy when you are not automounting.
`noauto` | do not mount at boot (or mount -a)
`uid` | is necessary to set a non-root owner of the filesystem after it is mounted by root.
`x-systemd-automount` | generate automount units to be along with the normal mount units.
`x-systemd.idle-timeout` | optional. defaults to no timeout.

```fstab
https://webdav.example.com /home/<user>/mnt/example.com davfs user,noauto,uid=<user>,x-systemd-automount,x-systemd.idle-timeout=5m 0 0
```

## Authentication
### /etc/davfs/secrets
Using systemd's automount feature necessitates storing passwords in the system-wide configuration. I found that davfs2 can use a per-user secrets file, but only when running as a non-root user. Specifying a UID the system-wide configuration doesn't cause that users configuration to be read. I looked pretty hard for some way to keep the secrets file in my user directory, but couldn't find an elegant solution that used systemd's automounting configuration. From systemd.mount(5):

> Note that the options User= and Group= are not useful for mount units. systemd passes two parameters to mount(8); the values of What= and Where=. When invoked in this way, mount(8) does not read any options from /etc/fstab, and must be run as UID 0.

So we add a line to `/etc/davfs2/secrets`:

`/home/<user>/mnt/example.com <webdav user> <webdav password>`

Make sure you enter the username accurately. There's no built-in way to hash the password file for davfs use, so this is shoulder surfable.

