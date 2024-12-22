# Fixing-initramfs-Errors : Boot-Failure
**Description:**<br>
When working with Linux systems, you may encounter boot failures with error messages such as: "No init found. Try passing init bootarg."This guide walks you through troubleshooting and fixing the problem step-by-step..<br>
This issue can leave your system unbootable and stuck at the **initramfs** prompt.<br>
**Note :<br> This process will work for any Linux-based operating system that uses initramfs**<br> **If Advance > Recovery Mode fails. Then you need to follow below steps**<br> **I have solved this problem in parrotOS**


--------------------------------------------------------------------------------------------------------------------------------------------
**This guide walks you through troubleshooting and fixing the problem step-by-step.** <br>

**Step 1**<br>
Error ( If you trying Advanced Options > Recovery Mode )<br>
4.001176) BTRFS: Transaction aborted (error -17) <br>
4.001977] BTRFS: error (device nυπεθη1p2 state A) in _ btrfs_run_delayed_items: 1176: errno-17 Object already exists <br>
4.0019981 BTRFS: error (device nume@nip2 state EA) in btrfs_replay_log:2116: errno=-17 Object already exists (Failed to recover log <br>
4.0125041 BTRFS error (device numeθη1p2 state EA): open_ctree failed <br>
mount: mounting /dev/nume@nip2 on /root failed : <br>
File exists Failed to mount /dev/numeon1p2 as root file system.<br>
This error getting while starting parrot os<br>


This error indicates an issue with the BTRFS file system on your Parrot OS installation. The "Object already exists" errors suggest there is corruption or inconsistency in the BTRFS metadata. This often happens due to improper shutdowns, power loss, or disk issues.<br>

Confirm the partition is using BTRFS with:<br>

***command : sudo blkid (if sudo not work try without sudo )*** <br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 2**<br>
First, try mounting the partition in read-only mode<br>
***command : sudo mount -o ro /dev/numeon1p2 /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 3**<br>
If this fails, proceed with btrfs check<br>
This will scan the partition for errors without making changes.<br>

***command : sudo btrfs check --readonly /dev/numeon1p2*** ( after running this)<br>

The output indicates that no errors were found during the btrfs check, meaning the file system seems to be intact. However, you’re still stuck at the (initramfs) prompt, which means the system isn't booting successfully.<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 4**<br>
Verify the Partition is Mountable<br>
If the /mnt directory does not exist, create it<br>
It should be created in the root directory (/).<br>

***command : sudo mkdir -p /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 5**<br>
Then, try mounting the partition again:<br>
***command : sudo mount /dev/nvme0n1p2 /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 6**<br>
Now try mounting your partition:<br>
This will mount the partition /dev/nvme0n1p2 to the /mnt directory, making its contents accessible at /mnt.<br>
***command : sudo mount /dev/nvme0n1p2 /mnt ( if you get below error )***<br>

Error <br>
Mount /dev/nume0n1p2/mnt [1199.<br>
4528841 BTRFS: Transaction aborted (error-17) 1199.<br>
4544013 BTRFS: error (device numeon1p2 state A) in _btrfs_run_delayed_items: 1176: errno-17 Object already exists <br>
1199.4551191 BTRFS: error (device nuneen1p2 state EA) in btrfs_replay_log:2116: errno=-17 Object already exists (Failed to recover log tree) [1199.4661841 BTRFS error (device numeθη1p2 state ΕΑ): open_ctree failed <br>
mount: mounting /dev/numeon1p2 on /mnt failed: Invalid argument <br>

[ The errors indicate that the BTRFS file system is encountering issues with the log tree. Specifically, the "Object already exists" error suggests that the log replay process is failing due to corruption or inconsistencies in the metadata ]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 7**<br>
The "Failed to recover log tree" error can often be resolved by clearing the log:<br>
This command clears the log tree, which might fix the corruption.<br> <br>
***command : sudo btrfs rescue zero-log /dev/nvme0n1p2***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 8**<br>
After running this, try mounting the partition again:<br>
***command : sudo mount /dev/nvme0n1p2 /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 9**<br>
If the command sudo mount /dev/nvme0n1p2 /mnt succeeded without any error, this means the partition /dev/nvme0n1p2 has been successfully mounted to the /mnt directory.
Check /mnt<br>
***command : ls /mnt***<br>

If the file system is mounted correctly, you should see the contents of the root file system, including directories like /etc, /var, /usr, etc.<br>

If you have got @ @home<br>
If after running sudo mount /dev/nvme0n1p2 /mnt you see @ and @home in the /mnt directory, it means the partition is using BTRFS subvolumes, and the root subvolume (@) and home subvolume (@home) are being displayed.<br>
@ is usually the root file system.<br>
@home is typically the home directory.<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 10**<br>
You can mount these subvolumes one by one. For example:<br>
This will mount the root file system in /mnt.<br>
***command : sudo mount -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 11**<br>
This will mount the home directory in /mnt/home.<br>
***command : sudo mkdir -p /mnt/home***<br>
***command : sudo mount -o subvol=@home /dev/nvme0n1p2 /mnt/home***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 12**<br>
Verify<br>
***command : ls /mnt***<br>
***command : ls /mnt/home***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 13 ( If 13,14,15,16 not work, Directly jump to step 17 )**<br>

Rebuild the System (if Necessary)<br>
If you're trying to repair your system or fix boot issues, you can now chroot into your root file system:<br>
***command : sudo mount --bind /dev /mnt/dev***<br>
***command : sudo mount --bind /proc /mnt/proc***<br>
***command : sudo mount --bind /sys /mnt/sys***<br>
***command : sudo chroot /mnt***<br>
Inside the chroot, you can update or reinstall your bootloader, regenerate the initramfs, or perform any other system repair.<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 14**<br>
Exit the chroot:<br>
***command : exit***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 15**<br>
Unmount the subvolumes:<br>
***command : sudo umount /mnt/home***<br>
***command : sudo umount /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 16**<br>
Reboot the system<br>
***command : sudo reboot***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 17**<br>
You can mount these subvolumes one by one. For example:<br>
***command : sudo mount -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

Error<br>
You may getting this error<br> 
can't find /dev/nvme0n1p2 in /etc/fstab<br>
[The error mount: can't find /dev/nvme0n1p2 in /etc/fstab occurs because the mount command expects an entry in /etc/fstab when using certain options. However, this is not required for manual mounting.]<br>
The fstab warning can be ignored for manual mounts. Ensure you are running the correct mount command:<br>
This command mounts the @ subvolume directly to /mnt. If you still face issues, proceed with these checks:<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 18**<br>
Try again<br>
***command : sudo mount -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

Error <br>
(initranfs) mount -o subvol=@ /dev/nume0n1p2 /mnt <br>
[ 1797.509173] fuseblk: Unknown parameter 'subvol' <br>
[1797.510832] ext3: Unknown parameter 'subvol' <br>
[ 1797.512312] ext2: Unknown parameter 'subvol' <br>
[1797.513851] ext4: Unknown parameter 'subvol' <br>
[1797.515584] xfs: Unknown parameter 'subvol' <br>
mount: mounting /dev/nume0n1p2 on /mnt failed: Invalid argument <br>

[ The error indicates that the kernel is trying to interpret the subvol mount option but does not recognize it because the file system type is not correctly identified as BTRFS. This typically happens if the wrong file system type is being assumed or the BTRFS driver is not correctly loaded in the initramfs environment ]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 19**<br>
Check available subvolumes on the partition:<br>
***command : sudo btrfs subvolume list /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 20**<br>
Force the mount command to use the BTRFS file system driver by adding the -t btrfs option:<br>
***command : sudo mount -t btrfs -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

Error<br>
mount: mounting /dev/nume0n1p2 on /mnt failed: Device or resource busy<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 21**<br>
Run the following command to check all currently mounted partitions:<br>
***command : mount | grep nvme0n1p2***<br>

If Output <br>
/dev/nume0n1p2 on /mnt type btrfs (rw,relatime, ssd, discard=async,space_cache=u2, suhuolid-5,subuol=/)<br>
[This means that the partition is already mounted, and the subvolume @ is likely already active at /mnt. ]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 22**<br>
Check if the @ Subvolume is Mounted Since the partition is already mounted, let's confirm which subvolumes are active. You can run:<br>
***command : udo btrfs subvolume list /mnt***<br>

Around 428 id entries getting. ( So much logs, entries you may get )<br>
[It seems you're encountering a large number of subvolumes listed under /mnt as shown by the btrfs subvolume list /mnt output. Since there are numerous entries, scrolling manually to a specific subvolume is impractical.]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 23**<br>
You can filter the subvolume list to search for specific subvolumes, like @, using grep:<br>
This will show you all subvolumes that contain @ in their path. It will help you quickly identify the subvolume you're looking for<br>

Again try to run<br> 
***command : sudo btrfs subvolume list /mnt | grep '@'*** <br>

--------------------------------------------------------------------------------------------------------------------------------------------

Error <br>
[ After running this showing device and resources busy ]<br>
[The "device or resource busy" error usually indicates that the filesystem is currently in use or that the mount operation cannot proceed due to ongoing processes accessing the mounted device.]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 24**<br>
The output of ls /mnt showing @ and @home suggests that your Btrfs subvolumes have been mounted correctly, as these are common names for Btrfs subvolumes.<br>

***command : ls /mnt***<br>
Output [@ @home]<br>
[Since you are seeing the subvolumes @ and @home when running ls /mnt, it indicates that the system has successfully mounted the root and home subvolumes. Before rebooting, here’s a quick checklist to ensure everything is in order:]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 25**<br>
Check that the subvolumes are mounted correctly:<br>
***command : mount | grep '/mnt'***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

Output should be <br>
[ /dev/nvme0n1p2 on /mnt type btrfs (rw,subvol=@)<br>
/dev/nvme0n1p2 on /mnt/home type btrfs (rw,subvol=@home) ] <br>

You might get <br>
[/dev/nume0n1p2 on /mnt type btrfs (rw,relatime, ssd, discard-async,space_cache=u2, subvolid-5,subvol=/)]<br>

The output you provided suggests that the /dev/nvme0n1p2 partition is mounted correctly on /mnt with the btrfs filesystem, but it seems like you are seeing a subvolid-5 instead of the subvolume @ being explicitly mentioned. This could be due to the mount options or subvolume not being specified directly during mounting<br>


**Step 26**<br>
Mount the @ subvolume explicitly: Now, mount the @ subvolume explicitly using the following command:<br>
***command : mount -o subvol=@ /dev/nume0n1p2 /mnt***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 27**<br>
***command : sudo btrfs subvolume list /mnt | grep '@'***<br>

Error <br>
If again output is <br>
[After running this showing Device or resource busy]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 28**<br>
Use ps to list processes and grep to find processes related to /mnt or /dev/nvme0n1p2<br>
***command : ps aux | grep /mnt***<br>
[Output -: 610 root]<br>

It seems that no active processes are using /mnt or the device /dev/nvme0n1p2, as the output shows only the grep command itself. it should be safe to unmount or try mounting again. Here’s what you can do next<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 29**<br>
This should show processes that are interacting with the mount point or device.<br>
***command : ps aux | grep /dev/nvme0n1p2***<br>
[Output -: 612 root]<br>

It seems that no active processes are using /mnt or the device /dev/nvme0n1p2, as the output shows only the grep command itself. it should be safe to unmount or try mounting again. Here’s what you can do next<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 30**<br>
Now run<br> 
***command : sudo mount -t btrfs -o subvol=@ /dev/nvme0n1p2 /mnt***<br>

If you're not getting any error after running the mount command (sudo mount -t btrfs -o subvol=@ /dev/nvme0n1p2 /mnt), it means the filesystem has been successfully mounted!<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 31**<br>
You can confirm that the mount was successful by running:<br>
***command : sudo mount | grep /mnt***<br>
output shoulld be : [/dev/nume0n1p2 on /mnt type btrfs (rw,relatime, ssd, discard=async,space_cache=u2, subuolid=256, subvol=/@)]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 32**<br>
Run:<br>
***command : ls /mnt/*** <br>
[ Output should be : The output of the ls /mnt/ command shows that you have successfully mounted the root subvolume (@) to /mnt, as it displays typical system directories like bin, boot, etc, lib, opt, proc, root, and others. ] <br>

Check Root Directory Contents: It looks like your system's root filesystem has been successfully mounted. Let's check the contents of /mnt/root to verify the root user’s home directory. Run: <br>
***command : ls /mnt/root*** <br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 33**<br>
Run: <br>
***command : ls /mnt/root/*** <br> directory contains expected files such as Desktop, go, home, and snap. This confirms that your root filesystem is now properly mounted. <br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 34**<br>
Run:<br>
***command : cat /mnt/etc/fstab***<br>
Output should be <br>
[
# <file system> <mount point> <type> <options> <dump> <pass><br>
UUID=****-**** /boot/efi vfat defaults,noatime,nodiratime 0 2<br>
UUID=*******-*****-*****-****-******** / btrfs subvol=@,defaults,noatime,nodiratime,autodefrag 0 1<br>
UUID=******-****-****-****-******** /home btrfs subvol=@home,defaults,noatime,nodiratime,autodefrag 0 2<br>
]<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 35**<br>
After saving the changes, you can try mounting again to verify everything is correct.<br>
This will mount all filesystems defined in fstab.<br>
***command : mount -a***<br>

--------------------------------------------------------------------------------------------------------------------------------------------

**Step 36**<br>
Run:<br>
Reboot your machine <br>
***command : reboot or reboot -f***<br>

