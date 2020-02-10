---
layout: post
title: "Shrinking a logical volume"

---

## What's the plan? ##
I was using an ext3 partition LVM2 to hold some files I was working on. It allowed me to make read/write snapshots to try things out, then throw away the changes on huge sets of binary files. Now that I'm done working with these files I want to tuck them away for a while before I delete them. The LV was bigger than it needed to be, so I wanted to minimize it.

### fsck the lv

Before you resize the filesystem, check it for consistency: `e2fsck -f <dev>`. This is not only a good idea but resize2fs will complain if you do not.

### find out how much space is used (optional)

`resize2fs -P <lv_dev>` will print the minimum number of blocks needed for the filesystem. If you want to learn more, run with `-d 32` to get information about the minimizing calculations. I thought trying to remember **-P**redict smallest size was clever.

### shrink the ext partition

`resize2fs -M <lv_dev>` will resize the filesystem to the number of blocks indicated by the -P command. I presume the **-M** is an abbrevation of **M**inimize but it is not indicated in the man page. On [my system](https://www.debian.org/) the default blocksize for most applications is 4 KiB. I would assume block-size = 4KiB for any filesystem over 1GiB.

### shrink the logical volume

`lvreduce --size [+,-]n[k,m,g,t]` 

Now that the partition has shrunk inside the LV, we can free up the space for use by other logical volumes. This is the tricky step because you need to convert used blocks to a binary size. Dump the details of your shrunken partition with `dumpe2fs -h <lv_dev>`. Calculate `(Block count - Free Blocks) * Block size` for the exact size in KiB. I add some number of extra bytes because I'm paranoid, but there's no reason I know not to use the minimum size. 

### expand the ext partition (optional)

If you added extra bytes in the last step, run `resize2fs <lv_dev>` to expand the partition to the limits of the LV. Done! You can go back and check the sizes with commands you already used.
