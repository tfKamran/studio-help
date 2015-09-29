# Disk images

* This will become a table of contents (this text will be scraped).
{:toc}


## Writing image to USB flash drives (in Linux)

To store the appliance you've created on a bootable USB stick, select
the disk image type in the build tab, then build and download the
gzip-compressed appliance.
[Uncompress and untar the appliance][uncompress-blog].


### The GUI way

Install [imagewriter from the SUSE:SUSEStudio
repository][studio-repo-imagewriter].
Then run `imagewriter` as root and write your image to
your chosen USB device.


### The dd way

To write your appliance to a USB stick, you need to find
the device to write to. After inserting the USB stick, open a terminal
and type:
  
       df

You will see output similar to this:

       /dev/sda2             30969600  15533336  13863100  53% /
       udev                   1997904       108   1997796   1% /dev
       /dev/sda5             92888248  85548000   2621560  98% /home
       /dev/sda6             23671572    935276  21533836   5% /var
       /dev/sdb1              7816228      1492   7814736   1% /media/disk
       
The last entry should be the USB stick you just plugged in.
If you're in doubt, remove it, run df again and check that the
line disappears.
The left column in df's output is the partition, and the path up to the
number is the path to the device.
In our example, '/dev/sdb1' is the partition, and '/dev/sdb' is the
**path to the device**.

**Important note: It is really, really important that you get the device path
right - you can cause irreparable damage to your system if you don't.**

After finding the device path, use the dd command to write your
appliance to the USB stick. dd needs two arguments: the input file (your
appliance), and the output file (the path to your USB device). In our
example, the input file is named "/home/suse/myappliance.raw" and the
path to the device is "/dev/sdb", so we run the following command in a
terminal:
    
       sudo dd if=/home/suse/myappliance.raw of=/dev/sdb bs=4k

The last argument (`bs=4k`) is optional, but adding it speeds up writing
to the USB device.

**Bear in mind that this will completely overwrite the USB device.
Make sure you don't have any important data on it first!** 

Writing to a USB stick is usually quite slow, so don't be alarmed if it
takes a long time. When dd has finished, it will tell you some
statistics about how much data it has written to the USB stick. If your
USB stick has a light on it that blinks when data is being written, wait
until it stops blinking before removing it.

**Note: As writing images blockwise is a critical process, compare the
md5sums of the raw image and the newly created device:**

       md5sum /home/suse/myappliance.raw
       md5sum /dev/sdb
       
Now you have a custom software appliance ready to be booted from your
USB stick!

To get persistence on a live hybrid USB stick system, you have to create
a second partition on the stick:

      fdisk /dev/sdb
      The number of cylinders for this disk is set to 3935.
      There is nothing wrong with that, but this is larger than 1024,
      and could in certain setups cause problems with:
      1) software that runs at boot time (e.g., old versions of LILO)
      2) booting and partitioning software from other OSs
      (e.g., DOS FDISK, OS/2 FDISK)
      
      Command (m for help): n
      Command action
      e extended
      p primary partition (1-4)
      p
      Partition number (1-4): 2
      First cylinder (661-3935, default 661):
      Using default value 661
      Last cylinder, +cylinders or +size{K,M,G} (661-3935, default 3935):
      Using default value 3935
      
      Command (m for help): w
      The partition table has been altered!
      
      Calling ioctl() to re-read partition table.
      Syncing disks.
      

## Writing image to USB flash drives (in Windows)


### The GUI way

On Microsoft Windows, install
[SUSE Studio ImageWriter for Microsoft Windows][win-imagewriter].


### The hard way

**Danger! This instruction is for experts!  
You can cause irreparable damage to your system if you make errors here.**

To store the appliance you've created on a bootable USB stick, select
the **disk image** type in the build tab, then build and download
the gzip-compressed appliance.
[Uncompress and untar][uncompress-blog] the appliance.
Perhaps you can use [7-zip][win-7zip], a tool unter GNU LGPL licence.

To write your appliance to a USB stick in a Windows environment,
you need to the '[dd for windows][win-dd]' tool.

To find the device to write to, insert the USB stick, open the
command line (cmd), and type:

       dd --list

You will see output similar to this:

        C:\temp>dd --list
        rawwrite dd for windows version 0.5.
        Written by John Newbigin <jn@it.swin.edu.au>
        This program is covered by the GPL.  See copying.txt for details
        Win32 Available Volume Information
        \\.\Volume{ac56bf46-66cc-11dc-86f6-806d6172696f}\
          link to \\?\Device\HarddiskVolume1
          fixed media
          Mounted on \\.\c:
        
        \\.\Volume{ac56bf47-66cc-11dc-86f6-806d6172696f}\
          link to \\?\Device\HarddiskVolume2
          fixed media
          Mounted on \\.\d:
        
        \\.\Volume{d8bf0b41-66cd-11dc-a7a7-806d6172696f}\
          link to \\?\Device\CdRom0
          CD-ROM
          Mounted on \\.\e:
        
        \\.\Volume{65668b14-8a7b-11dd-ab31-545543445208}\
          link to \\?\Device\Harddisk1\DP(1)0-0+8
          removeable media
          Mounted on \\.\f:
        
        
        NT Block Device Objects
        \\?\Device\CdRom0
          Removable media other than floppy. Block size = 2048
          size is 695670784 bytes
        \\?\Device\Harddisk0\Partition0
          link to \\?\Device\Harddisk0\DR0
          Fixed hard disk media. Block size = 512
          size is 120034123776 bytes
        \\?\Device\Harddisk0\Partition1
          link to \\?\Device\HarddiskVolume1
        \\?\Device\Harddisk0\Partition2
          link to \\?\Device\HarddiskVolume2
        \\?\Device\Harddisk0\Partition3
          link to \\?\Device\HarddiskVolume3
          Fixed hard disk media. Block size = 512
          size is 6366334464 bytes
        \\?\Device\Harddisk0\Partition4
          link to \\?\Device\HarddiskVolume4
          Fixed hard disk media. Block size = 512
          size is 468808704 bytes
        \\?\Device\Harddisk1\Partition0
          link to \\?\Device\Harddisk1\DR7
          Removable media other than floppy. Block size = 512
          size is 1014497280 bytes
        \\?\Device\Harddisk1\Partition1
          link to \\?\Device\Harddisk1\DP(1)0-0+8
          Removable media other than floppy. Block size = 512
          size is 1014480896 bytes
        
        Virtual input devices
         /dev/zero   (null data)
         /dev/random (pseudo-random data)
         -           (standard input)
        
        Virtual output devices
         -           (standard output)
        
        C:\temp>

The entry we are looking for is the one that says removable media, which
is the USB stick you just plugged in.
If you're in doubt, remove it, run dd --list again, and check if
the line disappears.
The information we need is the \\.\f:
    
       \\.\Volume{65668b14-8a7b-11dd-ab31-545543445208}\
         link to \\?\Device\Harddisk1\DP(1)0-0+8
         removeable media
         Mounted on \\.\f:

Important note: It is really, really important that you get the device path
right - you can cause irreparable damage to your system if you don't.

After finding the device path, you need to run dd to write your
appliance to the USB stick.
dd needs two arguments: the input file (your appliance) and the output
file (the path to your USB device).
In our example, the input file is named "c:\temp\myappliance.raw" and
the path to the device is "\\.\f:", so we run the following command in a
terminal window:
  
       dd if=c:\home\suse\myappliance.raw of=\\.\f: bs=4k

The last argument (bs=4k) is optional, but adding it will speed up writing
to the USB device.

**Bear in mind that this will completely overwrite the USB device.
Make sure you don't have any important data on it first!**

Writing to a USB stick is usually quite slow, so don't be alarmed if it
takes a long time.
When dd has finished, it will tell you some statistics about how much
data it has written to the USB stick.
If your USB stick has a light on it that blinks when data is being
written, wait until it stops blinking before removing it.

Now you have a custom software appliance ready to be booted from your
USB stick!


## Check the MD5SUM of the raw image and the USB stick

We received feedback that defective USB sticks can show problems with
whole systems but not with single files.
If your USB stick shows confusing behavior, check the MD5SUM to be sure
that your Linux system is not broken.

**Note: As writing images blockwise is a critical process, please
compare the md5sums of the raw image and the newly created device:**

       md5sum /home/suse/myappliance.raw
       md5sum /dev/sdb


## Copying an image onto a hard disk

That is the same case as How to copy an image onto a USB drive but
with the difference that you need to boot from an external media, like
another USB stick or liveCD with the `dd` tool on it.

An alternative is to boot from some live media and then transfer the
image over the network from another machine.
To do so, you need a live media, which contains netcat, and you have to
execute the following command on the machine, where the image should be
written to the hard disk:
       
       netcat -l -p1234 | dd of=/dev/sda

Make sure that the of argument points to the disk you want to write to.
Content on this disk will be lost.

On the machine where the image is located, execute the following command:

   dd if=/path/to/image.raw | netcat 1.2.3.4 1234

Replace 1.2.3.4 by the IP address of the machine you are writing to.
1234 is just a random network port.
Use a different number here, if needed.


## Changing configurations on disk image

You can mount the disk image by using the following command:
       
       losetup /dev/loop0 yourimage.raw 
       kpartx -av /dev/loop0
       mount /dev/mapper/loop0p1 mnt/

Then change whatever you want to change and umount.


## Testing an image on qemu

You can test the images on qemu before copying them to USB or disk.
Although you can do this with qemu, qemu-kvm shows a better performance.
In any case, the disk image has no free space, as it would expand on the
USB or disk, but it would not on qemu.
After making a copy of it, you can add some free space with the command:

       dd if=/dev/zero of=image.raw bs=1 count=1 conv=notrunc seek=3G	

Then try the image on qemu or qemu-kvm:

       qemu image.raw	

or
       
       qemu-kvm image.raw

or

       qemu-kvm --snapshot -m 512 image.raw


## Making a USB drive bootable

It is known that in some situations the USB drive would not boot.
Sometimes it may boot once, then stop booting afterwards.
The problem is that the main partition should be marked as bootable.
To do this, connect the usb disk and in a terminal enter:
(/dev/sdb has to be your USB: Make sure to use the right device!)

       umount /dev/sdb1 (and any other partition you have mounted automatically. Check it with the mount command)
       fdisk /dev/sdb
       \tp     «--- print partition table
       \ta     «--- activate partition (bootable)
       \t1     «--- partition 1 is bootable
       \tw     «--- write changes to partition table


## Creating a read-only appliance

This is only useful if you build images for SLES11 SP1 or later. The 
following procedure creates a root filesystem which is immutable:

1. Enable the LVM configuration in the 'Configuration' > 'Appliance' tab.
2. Define a volume group name (default is set to 'systemVG').
3. Create a root partition and a data partition. The root partition will
be read-only (placeholder LVM_GROUP_NAME), but the data partition will be set to read-write.
4. Go to the 'Configuration' > 'Appliance' tab and enable 
'Run script whenever the appliance boots'.
5. Enter the following lines into the text field and replace '{LVM_GROUP_NAME}'
with the real LVM root name:
   
       rm -f /etc/mtab && ln -s /proc/mounts /etc/mtab
       sed -i 's#^\(\s*kernel .*\)#\1 readonlyroot state=/dev/mapper/{LVM_GROUP_NAME}-LVRoot#g' /boot/grub/menu.lst
6. Build the appliance.


[uncompress-blog]: http://blogs.simplythebest.net/entry.php?w=RadianT&e_id=8
[studio-repo-imagewriter]: http://download.opensuse.org/repositories/SUSE:/SUSEStudio/
[win-imagewriter]: ftp://ftp.berlios.de/pub/kiwi/ImageWriter.exe
[win-7zip]: http://www.7-zip.org/
[win-dd]: http://www.chrysocome.net/dd
