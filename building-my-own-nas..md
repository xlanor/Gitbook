# Building my own NAS.

I've had an old i5-4560 lying around for awhile and I don't really enjoy throwing old computing hardware out for no good reason. I'd donate it, but I couldn't find anyone to adopt it.

So I thought to myself: what if I use it to run my own secondary NAS?

At that time, I was using an old i5-3540 as my server for my homelab. (I only very recently deprecated it, so it was more than suitable for my needs, but was struggling in recent years to keep up. I'm now running a 7980xe that was very generously donated to me, but that's a story for another time.) At that point of time, I was mainly running an assortment of 3tb drives as a singular LVM in my server, mainly collected throughout the years (I was saving my pocket money and skipping lunch just to scrunch up enough to get a WD Green 3tb at the next IT show as a kid)

At that kind of density, it becomes harder and harder to find average desktop cases (I still havent moved to rackmounts) that can fit the number of disks that I was collecting. I'd begun upgrading the disks by deprecating them and moving them over to 10tb disks, but it was a painful process (mainly because of the way I set my server up with a singular lvm).

Well it was a pain, until fate decided to speed things up for me and nuke my entire lvm when a single disk failed.

Since that was the case, I decided to make use of a donated LIAN LI PC-Q26B mini-ITX from my friend Andrew, who runs mirror.0x.sg, to build my home NAS.

I explored several options before settling on my current build in terms of NAS software

* Unraid.&#x20;

This is almost always promoted by linustechtips as the easiest way to setup a NAS quickly, and it comes with a nice web interface for you to remotely control. It has a whole ton of features that I don't need, since I have no intention of making the NAS remotely accessible from outside of my internal network. Even if I needed to, I could always opt to ssh jump via my server, or run a vpn on my server to access the NAS.&#x20;

Neither am I inclined to pay a hundred bucks to connect unlimited storage devices, but I was willing to spend a little more time configuring it the way I wanted it to be.

* TrueNAS

I actually did install and try this one for a little while. However, it all came down to this - I simply did not need the features that came bundled with it. I did not need things like a web interface for my nas, a file browser, or every other kind of configuration that a typical NAS could have. The only thing I needed, was a smb interface which I could reliably use across MacOS, linux, and windows devices at home.

So after speaking to my other friend, Likang, I settled on the final option.

* Proxmox

Proxmox is not typically something that comes to mind when one thinks about NAS software - Proxmox is a Type-1 hypervisor and not your typical NAS software. However, what I used it for was a little different from what it may have originally been intended to do

I created a ZFS pool on the command line and added my disks in mirrored pairs. Essentially, this meant that I would only have 1/2 of the avaliable space to use, but I'd been bitten once and did not want to get bitten again, which means that I would have to stomach the cost inefficency that this would entail.

```
❯ ssh -l root 192.168.2.9
root@192.168.2.9's password:
Linux proxmox 5.11.22-2-pve #1 SMP PVE 5.11.22-3 (Sun, 11 Jul 2021 13:45:15 +0200) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Dec  4 15:48:19 2021 from 192.168.1.30
root@proxmox:~# zpool status
  pool: tank
 state: ONLINE
  scan: scrub repaired 0B in 16:26:36 with 0 errors on Sun Nov 14 16:50:38 2021
config:

        NAME                                    STATE     READ WRITE CKSUM
        tank                                    ONLINE       0     0     0
          mirror-0                              ONLINE       0     0     0
            wwn-0x5000c500c93954a1              ONLINE       0     0     0
            wwn-0x5000c500c95050a0              ONLINE       0     0     0
          mirror-1                              ONLINE       0     0     0
            ata-WDC_WD140EDGZ-11B1PA0_9MH18S9J  ONLINE       0     0     0
            ata-WDC_WD140EDGZ-11B1PA0_9MGZVVBJ  ONLINE       0     0     0
          mirror-2                              ONLINE       0     0     0
            ata-WDC_WD100EFAX-68LHPN0_JEJY61GN  ONLINE       0     0     0
            ata-HGST_HDN721010ALE604_1SGVP1EZ   ONLINE       0     0     0

errors: No known data errors

root@proxmox:~# zpool list
NAME   SIZE  ALLOC   FREE  CKPOINT  EXPANDSZ   FRAG    CAP  DEDUP    HEALTH  ALTROOT
tank  32.7T  20.0T  12.7T        -         -     8%    61%  1.00x    ONLINE  -
```

The thing about this is that it is inherently expensive to set up. Basically, I had 76Tb of disks sitting in my NAS, yet I was only able to utilise 32.7Tb of it. Whether or not that's an acceptable solution is debatable, but in my case, I felt that it was.

Once that was done, I created a seperate VM through the proxmox web interface. This was not strictly required, but I wanted to seperate the cifs servicefrom the actual host, which would make it easier to either log access or remove (if I wanted to replace it with say, nfs).

Of course, this is not strictly necessary, but I have a bad habit of not documenting my homelab, and coming back to it a year later wondering: "Where the fuck did I do this?"

It really does help to have a VM labelled "CIFS SERVICE" staring at you in your face when this happens.

The ZFS Pool was then mounted into the VM, and the cifs service exposed it to the rest of my network.

```
Last login: Sat Dec  4 07:45:38 2021 from 192.168.2.207
jingkai@kaolin:~$ cat /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-PliPlTiR40MmQXQUEG4qZg95y8T00ZTTGR1JF5HJVkoCjDs2BrMXTZCSo4TgjnrG / ext4 defaults 0 0
# /boot was on /dev/nvme0n1p2 during curtin installation
/dev/disk/by-uuid/8ded28ab-5cfc-4b60-9988-2640fb9a21b1 /boot ext4 defaults 0 0
# /boot/efi was on /dev/nvme0n1p1 during curtin installation
/dev/disk/by-uuid/7892-D1F5 /boot/efi vfat defaults 0 0
//192.168.<SOME IP ON MY NETWORK>/proxmox-zfs /mnt/nas cifs username=<I REMOVED MY USERNAME>,password=<I REMOVED MY PASSWORD>,uid=1000,gid=1000,file_mode=0777,dir_mode=0777,vers=3.0,x-systemd.automount,mfsymlinks,defaults 0 0
/swap.img       none    swap    sw      0       0
```

Now, within my homelab, I was able to mount this via cifs and expose it to my docker containers.

During my stint when I was running k3s at home (and I may very well switch back, currently me and Likang are thinking about building a cluster across our homes if I remain in Singapore, this part was not even necessary because I directly mounted it via the storage CIFS CRD).





