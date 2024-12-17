# Fixing-initramfs-Errors : Boot-Failure
Below is the step-by-step documentation on how to resolve the initramfs error when the /mnt directory is missing at the root (/) level. This issue typically arises after an improper shutdown, leading to filesystem corruption or missing mount points.
**Note : This process will work for any Linux-based operating system that uses initramfs**

--------------------------------------------------------------------------------------------------------------------------------------------
**Step 1**
Error ( If you trying Advanced Options > Recovery Mode )
4.001176) BTRFS: Transaction aborted (error -17) 
4.001977] BTRFS: error (device nυπεθη1p2 state A) in _ btrfs_run_delayed_items: 1176: errno-17 Object already exists 
4.0019981 BTRFS: error (device nume@nip2 state EA) in btrfs_replay_log:2116: errno=-17 Object already exists (Failed to recover log 
4.0125041 BTRFS error (device numeθη1p2 state EA): open_ctree failed 
mount: mounting /dev/nume@nip2 on /root failed : 
File exists Failed to mount /dev/numeon1p2 as root file system.
This error getting while starting parrot os


This error indicates an issue with the BTRFS file system on your Parrot OS installation. The "Object already exists" errors suggest there is corruption or inconsistency in the BTRFS metadata. This often happens due to improper shutdowns, power loss, or disk issues.

Confirm the partition is using BTRFS with:

***command : sudo blkid (if sudo not work try without sudo )***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 2**
First, try mounting the partition in read-only mode<br>
***command : sudo mount -o ro /dev/numeon1p2 /mnt***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 3**
If this fails, proceed with btrfs check
This will scan the partition for errors without making changes.<br>

***command : sudo btrfs check --readonly /dev/numeon1p2*** ( after running this)

The output indicates that no errors were found during the btrfs check, meaning the file system seems to be intact. However, you’re still stuck at the (initramfs) prompt, which means the system isn't booting successfully.

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 4**
Verify the Partition is Mountable
If the /mnt directory does not exist, create it
It should be created in the root directory (/).<br>

***command : sudo mkdir -p /mnt***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 5**
Then, try mounting the partition again:<br>
***command : sudo mount /dev/nvme0n1p2 /mnt***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 6**
Now try mounting your partition:
This will mount the partition /dev/nvme0n1p2 to the /mnt directory, making its contents accessible at /mnt.<br>
***command : sudo mount /dev/nvme0n1p2 /mnt ( if you get below error )***

Error 
Mount /dev/nume0n1p2/mnt [1199.
4528841 BTRFS: Transaction aborted (error-17) 1199.
4544013 BTRFS: error (device numeon1p2 state A) in _btrfs_run_delayed_items: 1176: errno-17 Object already exists 
1199.4551191 BTRFS: error (device nuneen1p2 state EA) in btrfs_replay_log:2116: errno=-17 Object already exists (Failed to recover log tree) [1199.4661841 BTRFS error (device numeθη1p2 state ΕΑ): open_ctree failed 
mount: mounting /dev/numeon1p2 on /mnt failed: Invalid argument 

[ The errors indicate that the BTRFS file system is encountering issues with the log tree. Specifically, the "Object already exists" error suggests that the log replay process is failing due to corruption or inconsistencies in the metadata ]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 7**
The "Failed to recover log tree" error can often be resolved by clearing the log:
This command clears the log tree, which might fix the corruption.<br> 
***command : sudo btrfs rescue zero-log /dev/nvme0n1p2***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 8**
After running this, try mounting the partition again:<br>
***command : sudo mount /dev/nvme0n1p2 /mnt***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 9**
If the command sudo mount /dev/nvme0n1p2 /mnt succeeded without any error, this means the partition /dev/nvme0n1p2 has been successfully mounted to the /mnt directory.
Check /mnt<br>
***command : ls /mnt***

If the file system is mounted correctly, you should see the contents of the root file system, including directories like /etc, /var, /usr, etc.

If you have got @ @home
If after running sudo mount /dev/nvme0n1p2 /mnt you see @ and @home in the /mnt directory, it means the partition is using BTRFS subvolumes, and the root subvolume (@) and home subvolume (@home) are being displayed.
@ is usually the root file system.
@home is typically the home directory.

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 10**
You can mount these subvolumes one by one. For example:
This will mount the root file system in /mnt.<br>
***command : sudo mount -o subvol=@ /dev/nvme0n1p2 /mnt***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 11**
This will mount the home directory in /mnt/home.<br>
***command : sudo mkdir -p /mnt/home***<br>
***command : sudo mount -o subvol=@home /dev/nvme0n1p2 /mnt/home***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 12**
Verify<br>
***command : ls /mnt***<br>
***command : ls /mnt/home***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 13 ( If 13,14,15,16 not work, Directly jump to step 17 )**

Rebuild the System (if Necessary)
If you're trying to repair your system or fix boot issues, you can now chroot into your root file system:<br>
***command : sudo mount --bind /dev /mnt/dev***<br>
***command : sudo mount --bind /proc /mnt/proc***<br>
***command : sudo mount --bind /sys /mnt/sys***<br>
***command : sudo chroot /mnt***<br>
Inside the chroot, you can update or reinstall your bootloader, regenerate the initramfs, or perform any other system repair.

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 14**
Exit the chroot:
***command : exit***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 15**
Unmount the subvolumes:<br>
***command : sudo umount /mnt/home***<br>
***command : sudo umount /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 16**
Reboot the system<br>
***command : sudo reboot***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 17**
You can mount these subvolumes one by one. For example:<br>
***command : sudo mount -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

Error
You may getting this error 
can't find /dev/nvme0n1p2 in /etc/fstab
[The error mount: can't find /dev/nvme0n1p2 in /etc/fstab occurs because the mount command expects an entry in /etc/fstab when using certain options. However, this is not required for manual mounting.]
The fstab warning can be ignored for manual mounts. Ensure you are running the correct mount command:
This command mounts the @ subvolume directly to /mnt. If you still face issues, proceed with these checks:

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 18**
Try again<br>
***command : sudo mount -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

Error 
(initranfs) mount -o subvol=@ /dev/nume0n1p2 /mnt 
[ 1797.509173] fuseblk: Unknown parameter 'subvol' 
[1797.510832] ext3: Unknown parameter 'subvol' 
[ 1797.512312] ext2: Unknown parameter 'subvol' 
[1797.513851] ext4: Unknown parameter 'subvol' 
[1797.515584] xfs: Unknown parameter 'subvol' 
mount: mounting /dev/nume0n1p2 on /mnt failed: Invalid argument 

[ The error indicates that the kernel is trying to interpret the subvol mount option but does not recognize it because the file system type is not correctly identified as BTRFS. This typically happens if the wrong file system type is being assumed or the BTRFS driver is not correctly loaded in the initramfs environment ]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 19**
Check available subvolumes on the partition:<br>
***command : sudo btrfs subvolume list /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 20**
Force the mount command to use the BTRFS file system driver by adding the -t btrfs option:<br>
***command : sudo mount -t btrfs -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

Error
mount: mounting /dev/nume0n1p2 on /mnt failed: Device or resource busy

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 21**
Run the following command to check all currently mounted partitions:<br>
***command : mount | grep nvme0n1p2***<br>

If Output 
/dev/nume0n1p2 on /mnt type btrfs (rw,relatime, ssd, discard=async,space_cache=u2, suhuolid-5,subuol=/)
[This means that the partition is already mounted, and the subvolume @ is likely already active at /mnt. ]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 22**
Check if the @ Subvolume is Mounted Since the partition is already mounted, let's confirm which subvolumes are active. You can run:<br>
***command : udo btrfs subvolume list /mnt***<br>

Around 428 id entries getting. ( So much logs, entries you may get )
[It seems you're encountering a large number of subvolumes listed under /mnt as shown by the btrfs subvolume list /mnt output. Since there are numerous entries, scrolling manually to a specific subvolume is impractical.]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 23**
You can filter the subvolume list to search for specific subvolumes, like @, using grep:
This will show you all subvolumes that contain @ in their path. It will help you quickly identify the subvolume you're looking for

Again try to run<br> 
***command : sudo btrfs subvolume list /mnt | grep '@'*** <br>

--------------------------------------------------------------------------------------------------------------------------------------------

Error 
[ After running this showing device and resources busy ]
[The "device or resource busy" error usually indicates that the filesystem is currently in use or that the mount operation cannot proceed due to ongoing processes accessing the mounted device.]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 24**
The output of ls /mnt showing @ and @home suggests that your Btrfs subvolumes have been mounted correctly, as these are common names for Btrfs subvolumes.<br>

***command : ls /mnt***
Output [@ @home]
[Since you are seeing the subvolumes @ and @home when running ls /mnt, it indicates that the system has successfully mounted the root and home subvolumes. Before rebooting, here’s a quick checklist to ensure everything is in order:]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 25**
Check that the subvolumes are mounted correctly:<br>
***command : mount | grep '/mnt'***

--------------------------------------------------------------------------------------------------------------------------------------------

Output should be 
[ /dev/nvme0n1p2 on /mnt type btrfs (rw,subvol=@)
/dev/nvme0n1p2 on /mnt/home type btrfs (rw,subvol=@home) ] 

You might get 
[/dev/nume0n1p2 on /mnt type btrfs (rw,relatime, ssd, discard-async,space_cache=u2, subvolid-5,subvol=/)]

The output you provided suggests that the /dev/nvme0n1p2 partition is mounted correctly on /mnt with the btrfs filesystem, but it seems like you are seeing a subvolid-5 instead of the subvolume @ being explicitly mentioned. This could be due to the mount options or subvolume not being specified directly during mounting


**Step 26**
Mount the @ subvolume explicitly: Now, mount the @ subvolume explicitly using the following command:<br>
***command : mount -o subvol=@ /dev/nume0n1p2 /mnt***

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 27**
***command : sudo btrfs subvolume list /mnt | grep '@'***

Error 
If again output is 
[After running this showing Device or resource busy]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 28**
Use ps to list processes and grep to find processes related to /mnt or /dev/nvme0n1p2<br>
***command : ps aux | grep /mnt***
[Output -: 610 root grep /mnt]

It seems that no active processes are using /mnt or the device /dev/nvme0n1p2, as the output shows only the grep command itself. it should be safe to unmount or try mounting again. Here’s what you can do next

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 29**
This should show processes that are interacting with the mount point or device.<br>
***command : ps aux | grep /dev/nvme0n1p2***
[Output -: 612 root]

It seems that no active processes are using /mnt or the device /dev/nvme0n1p2, as the output shows only the grep command itself. it should be safe to unmount or try mounting again. Here’s what you can do next

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 30**
Now run<br> 
***command : sudo mount -t btrfs -o subvol=@ /dev/nvme0n1p2 /mnt***

If you're not getting any error after running the mount command (sudo mount -t btrfs -o subvol=@ /dev/nvme0n1p2 /mnt), it means the filesystem has been successfully mounted!

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 31**
You can confirm that the mount was successful by running:<br>
***command : sudo mount | grep /mnt***<br>
output shoulld be : [/dev/nume0n1p2 on /mnt type btrfs (rw,relatime, ssd, discard=async,space_cache=u2, subuolid=256, subvol=/@)]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 32**
Run:<br>
***command : ls /mnt/*** <br>
[ Output should be : The output of the ls /mnt/ command shows that you have successfully mounted the root subvolume (@) to /mnt, as it displays typical system directories like bin, boot, etc, lib, opt, proc, root, and others. ] <br>

Check Root Directory Contents: It looks like your system's root filesystem has been successfully mounted. Let's check the contents of /mnt/root to verify the root user’s home directory. Run: <br>
***command : ls /mnt/root*** <br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 33**
Run: <br>
***command : ls /mnt/root/*** <br> directory contains expected files such as Desktop, go, home, and snap. This confirms that your root filesystem is now properly mounted. 

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 34**
Run:<br>
***command : cat /mnt/etc/fstab***<br>
Output should be <br>
[
# <file system> <mount point> <type> <options> <dump> <pass><br>
UUID=****-**** /boot/efi vfat defaults,noatime,nodiratime 0 2<br>
UUID=*******-*****-*****-****-******** / btrfs subvol=@,defaults,noatime,nodiratime,autodefrag 0 1<br>
UUID=******-****-****-****-******** /home btrfs subvol=@home,defaults,noatime,nodiratime,autodefrag 0 2<br>
]

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 35**
After saving the changes, you can try mounting again to verify everything is correct.<br>
This will mount all filesystems defined in fstab.<br>
***command : mount -a***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 36**
Run:<br>
Reboot your machine <br>
***command : reboot or reboot -f***

