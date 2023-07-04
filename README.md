# isoManager

## About

This is a script made for my http-mirror/repo/iPXE guest to automatically detect any number of ISOs as they're attached/ejected and mount them to a directory on-demand.

It mounts them to a subdirectory taking after LABEL for easy recognition and presentation on a webserver frontend (e.g. `/mywebroot/isos/Rocky-9-2-x86_64-dvd`). It also removes the directories when an ISO is no longer present - aiming to only list ISOs mount dirs for those it has available at any given time.

As a general overview it installs two configuration files - One to autofs.master.d for its self-managed cdrom mounts, and another in udev.d to call the script whenever a CDROM event is detected.

When called it scans /dev/disk/by-label for CD's with a label and automounts them to their own subdirectory named after the label - In my case this is for easy zero-configuration HTTP mirroring access without consuming additional space or needing a process for every new random ISO I attach.

It helps me avoid:

1. Wasting hypervisor storage space by extracting ISOs into guests.
2. Bloating my guest virtual disk's because of the above (for backup purposes - Some of these ISOs are close to 10GB!).
3. Doing the above manually on a per-ISO basis
4. Managing autofs/fstab entries on a per-ISO basis (ANd manually removing stale mounts when the CD gets ejected too.)

This script solves these problems for me by making a mounted directory magically appear in the webserver of my repo/mirror guest after attaching it from the hypervisor without consuming any addutional space OR time to manually mount them. 

[I couldn't find a zero-configuration solution like this in the 68 seconds I spent searching online so I spent a lot longer making this script instead.](https://xkcd.com/1319/) Hopefully it's resourceful to others and perhaps even in other use-cases.

## Usage

Run `main` and it'll place its autofs and udev configs then run. It will exit before doing anything if it doesn't think autofs is installed so make sure to install that first!

## What's supported / tested?

This script was written generically and will work on any modern distro (And perhaps even old ones) where autofs is available. It auto-detects the path to auto.master.d to handle subtle distro differences.

It was written for a Rocky 9.2 guest on VMware with Hot-added CD/DVD drive's and the `eject` command but also works tested on an Archlinux guest with QEMU, whose AUR autofs package uses a subdirectory of /etc.

I would expect this to also work on any physical servers with CD drives. This tool could also prove useful in other contexts.

## Future plans

1. Direct ISO directory support? Being able to point the script right at an ISO dir/mount and forget - would be nice. But monitoring for changes would require more than just udev event checks.

2. cdrom eject & hypervisor cdrom-drive removal check to avoid fighting/locking conditions on active mounts.
