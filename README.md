# FreeNAS - A home NAS solution

**Important: I had built this in 2011 and have not updated this document since. There will be obsolete information here but the concepts should still remain very useful.**

**Disclaimer: Use this document at your own risk. I am not responsible for any damages caused by your use of this document, or caused by errors and/or omissions in this document. If that is not acceptable to you, you may not use this document. By using this document you are accepting this disclaimer.**

<br />

**An Open Source Storage Platform**

When my third-party NAS had failed me, and I lost data, I decided to build my own NAS. Priority for me now was data redundancy at the highest level.

After some research into NAS specific areas, i.e. data integrity, functionality, and flexibility, I decided to go with FreeNAS. It met my requirements!

Supporting both ZFS and H/W Raid-1, a host of functions, stability of BSD, able to interface with Windows, Mac, Linux, and the flexibility to allows *nix scripting.

Below are the details of my NAS build...

FreeNAS is an open source Network Attached Storage (NAS) platform based on FreeBSD. It uses an embedded operating system which is compact and can be installed on a USB flashdrive, CompactFlash, or a hard drive. FreeNAS supports the following:

- Protocols: CIFS (samba), NFS, AFP, SSH, rsync, FTP, TFPT, UPnP(DLNA), BitTorrent, iTunes
- Filesystems supported: ZFS, UFS2
- Exporting services: iSCSI
- Directory services: Active Directory, LDAP
- Monitoring: SNMP, S.M.A.R.T
- Disk Encryption

<br />

**Hardware Specifications**

The following section contains the specifications, details, and the configuration of server hardware.

<br />

(Actual case used)

<img align="left" width="300" src="/images/FractalCase.jpg">

**Server Specifications**

**Motherboard**  
Gigabye GA-H55-USB3 (rev. 2.0)

**CPU**  
Intel Core i5-760 (Quad) 2.80 GHz

**Memory**  
16GB | 4x Transcend 4GB 1333MHz DDR3 Non-ECC

**Network**  
Onboard (mobo) RTL8111E chip 1000MB

**PSU**  
Corsair Progessional Series HX650W

**Case**  
Fractal Design Define R3, Black Pearl

**Video**  
ASUS EAH5450 Silent/DI/512MB

**Optical Drive**  
Samsung TS-H653 DVD Writer

**Operating System**  
FreeNAS-8.0-RELEASE-amd64

**Interfaces**  
eSATA USB 3.0, USB 2.0, Firewire (1394)

<br />
<br />
<br />
<br />
<br />
<br />

**Storage Subsystem**

**System Disk**  
Primary: 8GB TDK Gold USB Flashdrive  
Secondary: 8GB TDK Platinum USB Flashdrive

**Storage Disk**  
Config: RAID-Z  
Disks: 4x 2TB WD Caviar Black 7200 RPM SATA 6GB/s1  
Controller: Onboard (mobo) SATA 2.0 Controller  
Capacity: 5.9TB

**Backup Disk**  
Config: UFS2/Hardware Raid-1  
Disks: 2x 1TB WD Caviar Black 7200 RPM SATA 3GB/s  
Controller: Astrotek PCI-Ex 1 2ch SATA 3.0 Controller  
Capacity: 1TB

**Extra Disk**  
Config: UFS2/Hardware Raid-1  
Disks: 2x 1TB WD Caviar Black 7200 RPM SATA 3GB/s  
Controller: Onboard (mobo) SATA 2.0 Controller  
Capacity: 1TB  
Storage redundancy includes ZFS, H/W Raid-1, and storage to the cloud.  
Motherboard disk controller is SATA 2.0 & limits interface to 3GB/s

**Cooling**  
Case fan configuration includes 2x intakes and 1x outtake. Fans used:

Fractal Design Silent Series 120mm
- RPM: 1000 +/- 10% RPM
- Noise: 15 dBA
- Airflow: 38.3 CFM, 65 m³/h

Arctic F12 TC
- RPM: 300 - 1350 RPM
- Noise: 0.5 Sone
- Airflow: 57 CFM / 96.8 m³/h

Expansion: Total of 7 fan slots (2x120mm in front, 2x 120/140mm in top, 1x120mm in back, 1x 120/140mm in side panel, 1x 120/140mm in bottom)

**Silent Computing**  
Define R3, Black Pearl
- Pre-fitted with dense noise absorbing material
- HDD-trays include silicone mounting
- ModuVent™ design to allow optimal silence (or optimal airflow

ASUS EAH5450 Silent/DI/512MB
- ASUS exclusive 0dB thermal solution

<br />

**Software Procedures**

The following section contains step-by-step instructions for maintenance and additional functionality to your FreeNAS server.

Some commands to be executed on client machines are for specific operating systems only, i.e. only for Windows, Mac, or Linux.

Backup System Diskdd is a very powerful program under Linux which does a low-level copy and conversion of raw data, i.e. it ignores cylinders, partitions.Warning: Incorrectly assigning (i.e. reversing) devices to the if and of parameters can possibly result in the loss of some or all of your data on the source device!Steps1. Insert your source USB flashdrive

2. Find the device name assigned to your USB Flashdrive. E.g. /dev/sdb

dmesg | tail -20

3. Insert your destination USB flashdrive

4. Find the device name assigned to your USB Flashdrive. E.g. /dev/sdc

       

dmesg | tail -20

5. Clone media

       

dd if=/dev/sdb of=/dev/sdc bs=1M

where

    if = input device/to be read from (i.e. source)

    of = output device/to be written to (i.e. destination)

    bs = blocksize (optional) - determines the speed of the procedure. Larger blocks result in a faster copy

6. Remove the USB flashdrives

Copy files via Mass Storage

The following outlines the steps on how to copy data to your FreeNAS server via an external mass storage device.

Copying files to a FreeNAS server via local file protocols are much faster compared with using network file sharing protocols.

I use a USB 2.0 mass storage device (NTFS filesystem) to transfer data to my FreeNAS box.

Note, using an external SATA harddrive here (if you have an available SATA port) would achieve much faster data transfers when compared with USB 2.0.

Warning: If used incorrectly, you can overwrite and/or lose data.

Steps

1. Copy the data to your USB mass storage drive (under Windows)

robocopy /R:3 /W:1 /Z /E /NP /LOG:F:\Log.txt "E:\MyDirOfLargeFiles" "F:\MyDirOfLargeFiles"

Review the Log.txt file created at the destination. This will report any failures with the copy process.

2. Insert your USB mass storage drive to your NAS

3. Secure Shell into your NAS

      

ssh myuser@192.168.1.2

4. Find the device name assigned to your USB mass storage drive

E.g. /dev/da1

dmesg | tail -20

5. Mount the USB mass storage drive

      

cd /mnt

     su

     mkdir myextusb

     mount -t ntfs /dev/da1s1 /mnt/myextusb/

To mount FAT32 filesystems, use: -t msdosfs -o large

Note: Creating a directory under /mnt (or higher) will not be preserved after a reboot, as this is treated as ROM by FreeNAS.

6. Copy the files to your storage drive

       

cp -r /mnt/myextusb/MyDirOfLargeFiles /mnt/zpool1/MyDirOfLargeFiles

      

7. Unmount the USB mass storage drive

cd

unmount /dev/da1s1

You may now unplug the USB mass storage drive from your NAS

8. Exit Secure Shell

Copy files via rsync over ssh

The following outlines how to do an ad-hoc copy of a local directory on your PC to a remote directory on your FreeNAS server.

Rsync is a copying tool, that can be used to copy to a local or remote machine over any remote shell.

Warning: If used incorrectly, you can overwrite and/or lose data.

rsync command options:

 - v: verbose

 - r: recursive

 - a: archive mode; equal -rlptgoD

 - z: compress

 - e: remote shell (rsh/ssh)

   --delete: delete extraneous files from destination

 - u: update, skip files that are new on the destination

 - P: keep partially transferred files

 - n: dry run

Copy a local directory to a remote directory

   

rsync -vrazuPe "ssh -l myuser" /some/local/directory/ 192.168.1.2:/mnt/zpool1/dataset1/some/directory/

To synchronise, include the --delete option (after option -e). This will delete extraneous files from the destination directory.

Recommend doing a trial run with no changes, include the -n option.

Automate backups with rsync over ssh

The following outlines the steps to schedule a daily task on your PC to synchronise a local directory to a remote directory on your FreeNAS server.

Rsync, Bash, and Cron can be used to build a complete automated backup solution.

Warning: If used incorrectly, you can overwrite and/or lose data.

Steps

 

1. Create a public/private key pair for ssh authentication

(On your PC)

       

ssh-keygen -t dsa

Follow the prompts and hit [enter]. Use an empty passphrase so the key can be used without requiring user interaction (i.e. in an automated script).

Note: You can use rsa keys (-t rsa)

Output

Private key is located at  $HOME/.ssh/id_dsa

Public key is located at   $HOME/.ssh/id_dsa.pub

2. Setup a home directory

(On your FreeNAS server)

Create a home directory. Note: Creating a directory in /mnt (or higher) will not be preserved after a reboot, as this is treated as ROM by FreeNAS.

       

cd /mnt/zpool1

mkdir home

cd home

mkdir .ssh

     chown root:wheel /mnt/zpool1/home

Log into the FreeNAS Web GUI /Account/Users/myuser/

Update myuser's Home Directory to the above location, i.e. /mnt/zpool1/home

3. Copy the public key to the FreeNAS server

(On your PC)

Under Linux

ssh-copy-id -i ~/.ssh/id_dsa.pub myuser@192.168.1.2

Under Mac       

scp ~/.ssh/id_rsa.pub myuser@192.168.1.2:/mnt/zpool1/home/.ssh/authorized_keys

4. Verify public/private key ssh authentication

(On your PC)

Make sure you are logged out of any ssh sessions

ssh myuser@192.168.1.2

5. Create a backup script

Create an empty file

vi backupscript

Add content

rsync -vrazuPe ssh /some/local/directory/ 192.168.1.2:/mnt/zpool1/dataset1/some/directory/

Save & quit

:wq

Make the file executable

chmod +x backupscript

6. Test the backup script

       

./backupscript

7. Schedule the backup script using Cron

Add an entry to your user's crontab file

crontab -e

With content, e.g. run ever day at 11:00pm

#minute(0-59)    hour(0-23)    dom(1-31)    month(1-12)    weekday(0-6)    cmd

0                23            *            *              *               /mnt/zpool1/home/backupscript

Save & quit

:wq

Verify (display) the current crontab

crontab -l

Setup Apple Time Machine Backup

FreeNAS supports Apple Time Machine. Time Machine backups can be stored on a local volume, or a dataset.

Using a (dedicated) dataset for Time Machine backups allow you to enforce a quota to limit Time Machine from using your entire volume space.

The below steps include sample values.

Steps:

1. Create a Dataset

 Volume

 Dataset Name

 Quota

 zpool1

 TimeMachine

 1000G

2. Add a Share

The next time you are in Mac OS X, Time Machine should auto-detect this backup disk over the network.

Storage to the Cloud

The following outlines the steps to synchronise a directory (or entire drive) from your FreeNAS server to the cloud. I use "Amazon Cloud" for my cloud computing and storage.

What is AWS

Amazon Web Services (AWS) is Amazon's cloud platform, which allows you to utilise computing power and storage. AWS offers infrastructure services, including Amazon Elastic Compute Cloud (Amazon EC2), Amazon Simple Storage Service (S3), and more.

Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides computing capacity in the cloud. You can create a virtual Amazon EC2 environment with most operating systems, services, databases, and application platform stack for your hosted application.

Warning: If used incorrectly, you can overwrite and/or lose data.

Steps:

1. Sign up for Amazon EC2

        http://aws.amazon.com/

Note: When signing up for Amazon Elastic Compute Cloud (Amazon EC2), this will automatically sign you up for:

    Amazon Simple Storage Service (Amazon S3), and
    Amazon Virtual Private Cloud (Amazon VPC).

2. AWS Management Console

    Go to the Amazon EC2 tab
    Select Launch Instance button
    Choose an Amazon Machine Image (AMI). A packaged-up environment to setup & boot the instance, e.g. Basic 64-bit Amazon Linux AMI
    Create a Key Pair. This is the security credential for authentication for the instance being created (do not lose this).
    Create a Security Group. This will define firewall rules to your instance, e.g. allow ssh under Linux, RDC under Windows...

3. Secure the X.509 certificate

This is the Key Pair generated during the creation of the instance (above step).

    Copy this certificate to your FreeNAS box
    Use chmod to make your private key not publicly viewable.

chmod 400 myuser.pem

4. Verify public/private key ssh authentication

ssh -i myuser.pem ec2-user@[your-public-dns]amazonaws.com

Notes:

    Public DNS can be obtained from the AWS Management Console, EC2 Instance
    Some AMIs let you log in as root
    To run a command as root, prefix the command with sudo

5. Create a sync script

Create an empty file

vi synccloudscript

Add content

rsync -vrazuPe "ssh -i myuser.pem -l root" /source/dir/ ec2-user@[your-public-dns].compute-1.amazonaws.com:/home/ec2-user/target/dir/

Save & quit   

:wq

Make the file executable

chmod +x synccloudscript

6. Test the sync script

       

./synccloudscript

7. Schedule the sync script using Cron

Add an entry to your user's crontab file

crontab -e

With content, e.g. run ever day at 11:00pm

#minute(0-59)    hour(0-23)    dom(1-31)    month(1-12)    weekday(0-6)    cmd

     0                23            *            *              *               /mnt/zpool1/home/synccloudscript

Save & quit

:wq

Verify (display) the current crontab

crontab -l

Zettabyte File System

ZFS is a 128-bit file system and logical volume manager written from the ground-up to provide data integrity, immense scalability, with simple administration. Data integrity is a high priority for ZFS, to protect user's data (on disk) from silent corruption. ZFS filesystems are built on virtual storage pools called zpools, where traditional file systems would reside on a single disk and require a volume manager to use more than one disk.

zpool version 15 is supported in FreeBSD 8.2-RELEASE (which is what FreeNAS 8 is built from).

Commands

Identify the available storage pool and file system space

zpool list

Get detail status of storage pool

zpool status -v

Check data integrity, i.e. explicit scrubbing of all data within the pool

zpool scrub zpool1

Show storage pool format version and features supported

zpool upgrade -v

Show IO statistics for a storage pool

zpool iostat 5

Create a storage pool

zpool create zpool1 /disk1 /disk2

Destroy a storage pool

zpool destory zpool1

Attach a device to the storage pool 

zpool attach zpool1 /disk1 /disk3

Detach a device from the storage pool

zpool detach zpool1 /disk1

Import (an exported) storage pool

zpool import -d / zpool1

Export a storage pool

zpool export zpool1

Image used with permission from Fractal Design

