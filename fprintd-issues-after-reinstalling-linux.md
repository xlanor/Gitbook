# fprintd issues after reinstalling linux

Previously, my laptop was running Fedora, until I accidentally borked the bootloader when trying to enable hibernation.

As I was about to fly for my new job soon, I didn't want to fiddle around with it, so I went back to Ubuntu  for my daily driver, which was almost entirely working out of the box.

Except for the fingerprint sensor.

```
~ <SUDO>💪
🕙20:40:41 ❯ sudo fprintd-enroll jingkai

Using device /net/reactivated/Fprint/Device/0
Enrolling right-index-finger finger.
Enroll result: enroll-unknown-error
```

If we look at journalctl logs (`journalctl -e -u fprintd`), the reason immediately becomes obvious

```
Mar 28 20:40:00 framework fprintd[130514]: Device reported an error during enroll: Finger is too similar to another, try use>
Mar 28 20:40:05 framework fprintd[130514]: Device reported an error during enroll: Finger is too similar to another, try use>
Mar 28 20:40:36 framework fprintd[130514]: Device reported an error during enroll: Finger is too similar to another, try use>
Mar 28 20:40:46 framework fprintd[130514]: Device reported an error during enroll: Finger is too similar to another, try use>
```

The main reason for this is because the fingerprint sensor device itself has a tiny bit of onboard memory which retained the previously enrolled fingerprint in fedora. Thus, even though we reinstalled ubuntu over fedora, fprintd rejects our new fingerprint

Luckily, someone else has encountered this problem and wrote a quick python script to call the device directly and flush all onboard memory. The script is avaliable here ([https://gitlab.freedesktop.org/libfprint/libfprint/-/issues/415#note\_1063158](https://gitlab.freedesktop.org/libfprint/libfprint/-/issues/415#note\_1063158)). Please take note that you must run as root and install `gir1.2-fprint-2.0` from apt repositories first.

```
sudo ./delete_fp.py 
<gi.FpiDeviceGoodixMoc object at 0x7f99f5cdff40 (FpiDeviceGoodixMoc at 0xe6f140)> 
goodixmoc 
UID3BFA0E87_XXXX_MOC_B0 libusb: error [udev_hotplug_event] ignoring udev action change
 libusb: error [udev_hotplug_event] ignoring udev action change 
libusb: error [udev_hotplug_event] ignoring udev action change 
libusb: error [udev_hotplug_event] ignoring udev action change 
num prints stored: 1 
deleting print: 2022-01-10 
valid: 1 jingkai 
FP1-20220110-7-4DC86AA1-jingkai deleted
```

Even though I have no prints registered on ubuntu, you can see that it deletes the fingerprint previously registered on fedora.

```
~ via 🐍 v3.8.10 <SUDO>💪
🕙20:43:00 ❯ sudo fprintd-enroll jingkai

Using device /net/reactivated/Fprint/Device/0
Enrolling right-index-finger finger.
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-remove-and-retry
Enroll result: enroll-remove-and-retry
Enroll result: enroll-stage-passed
Enroll result: enroll-remove-and-retry
Enroll result: enroll-remove-and-retry
Enroll result: enroll-stage-passed
Enroll result: enroll-remove-and-retry
Enroll result: enroll-stage-passed
Enroll result: enroll-remove-and-retry
Enroll result: enroll-remove-and-retry
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-retry-scan
Enroll result: enroll-stage-passed
Enroll result: enroll-stage-passed
Enroll result: enroll-remove-and-retry
Enroll result: enroll-remove-and-retry
Enroll result: enroll-completed

```

Once that has been done, re-enrolling a fingerprint should work seamlessly.

```
~ via 🐍 v3.8.10
🕙20:49:30 ❯ sudo -s
Place your finger on the fingerprint reader
framework#
```
