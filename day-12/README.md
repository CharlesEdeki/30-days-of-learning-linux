# Day 12 - sysfs, udevadm, and dd

## Objective
Understand the difference between /dev and /sys, learn how to read
full device attributes using udevadm, and explore how dd moves data
between devices and files.

---

## What I Learned
- /dev and /sys serve different purposes. /dev is for interacting
  with a device. /sys is for reading information about it
- The sysfs path for a device is long because it maps the device's
  actual physical location through the hardware hierarchy
- The kernel assigns device names like sda in detection order. That
  order can change between reboots. The by-id symlink in /sys is
  stable and does not change regardless of detection order
- udevadm reveals the full identity of any device including its
  sysfs path, major and minor numbers, subsystem, vendor, and all
  stable symlinks
- /sys/block lists all block devices but the entries are symbolic
  links. ls -l /sys/block reveals the real paths underneath
- dd copies data in fixed size blocks from an input to an output.
  It can read from the middle of a file, skip sections, and stop
  at a precise point
- /dev/zero produces an endless stream of zero bytes. Without a
  count limit dd would run forever
- dd uses = syntax instead of the standard dash flag format because
  it is based on old IBM JCL style

---

## What I Built / Practiced

```bash
# read full device attributes
$ udevadm info --query=all --name=/dev/sda

# key output decoded:
# P:  full sysfs path through hardware hierarchy
# D:  b 8:0  (block device, major 8, minor 0)
# S:  disk/by-id/scsi-0QEMU_QEMU_HARDDISK_106145062 (stable id)
# E:  SCSI_VENDOR=QEMU (confirmed VM virtual disk)

# check block device symlinks
$ ls -l /sys/block

# create a 1KB file from /dev/zero
$ dd if=/dev/zero of=testfile bs=1024 count=1

# create a 100MB test file from random data
$ dd if=/dev/urandom of=testfile bs=1M count=100
```

---

## Challenges Faced
- The sysfs path looked overwhelming at first. Resolved by reading
  it left to right as a physical route: PCI bus, controller, host
  adapter, target, disk
- dd syntax with = instead of dashes was unfamiliar. It is based on
  IBM JCL style, not standard Unix convention

---

## Key Takeaways
- /dev/sda can point to a different disk after a reboot if device
  detection order changes. Always use the by-id path in scripts
  that reference specific disks
- udevadm is the right tool when /dev tells you a device exists but
  not what it actually is
- dd does not ask for confirmation. Swapping if and of by mistake
  can overwrite a disk with zeros. Always verify both paths before
  running it
- The SCSI_VENDOR=QEMU line in udevadm output confirmed the disk
  is virtualised. The abstraction is complete enough that only
  udevadm reveals what is underneath

---

## Commands Used

```bash
udevadm info --query=all --name=/dev/sda   # full device attributes
ls -l /sys/block                            # block devices with real paths
dd if=/dev/zero of=testfile bs=1024 count=1 # copy 1KB of zeros to file
dd if=/dev/urandom of=testfile bs=1M count=100 # create 100MB test file
ls -lh testfile                             # confirm file size
```

---

## Resources
- How Linux Works by Brian Ward, chapter 3.2 and 3.3
- [Medium: Day 12 write-up](https://medium.com/@charlesedeki093/day-12-looking-inside-a-device-sysfs-udevadm-and-dd-fc02fdd45541)

---

## Output
- Ran udevadm on /dev/sda and decoded full output
- Confirmed VM is running on QEMU virtualisation
- Identified stable by-id device path vs unstable sda naming
- Day 12 write-up published on Medium
