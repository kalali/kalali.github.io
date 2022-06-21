# Learning how to use zfs in OpenSolaris

## What is a file system 

**File systems** make it possible to store and retrieve files and containing data into storages like hard disks, optical disks and other types of storages.

OpenSolaris support both legacy file systems like **UNIX File System (UFS)** and its own file system called **Zettabyte File System** (**ZFS**). Following figure show how UFS and other legacy file systems work.

![](post-img/3180_02_01.png)

As you can see we can partition each storage device into one or more
volumes with different file systems. For example one hard disk may have
an **NTFS** volume, a UFS volume and an **EXT4** volume.

In contrast with legacy file systems, ZFS uses concept of resource
pooling. In ZFS one or more devices, volumes or files can be combined to
form a pool of storages, hence called **zpool**. Then file systems are
built on top of this pool. For example we can create a file system
capable of storing a 32 TB file on top of a zpool created on using 16
hard disks, 2 TB each.

## What OpenSolaris supported file systems are

OpenSolaris as a pioneer OS for enterprises supports several file
systems in addition to pushing its flagship and unique file system named
ZFS. Each supported file system is either supported because of backward
compatibility or to provide the administrators with a wider range of
choices when it come to formatting the storages.

The UNIX file system (UFS) is the common file system on UNIX like
operating systems.

The **Personal Computer File System** (**PCFS**) supports **FAT12**,
**FAT16** and **FAT32** formatted disks.

The **High Sierra File System** (**HSFS**) allows us to access files on
High Sierra or ISO 9660 format CD-ROM disks.

The **Temporary File Storage Facility** (**TMPFS**) provides a temporary
file system in **RAM** or **SWAP** space, we can access the file system
as a normal file system but in the background it is stored in memory
instead of persisted storage.

The integration of **Storage and Archive Manager** and **Quick File
System** (**SAM-QFS**) is provided so we can have access to a clustered
archiving solution in OpenSolaris.

ZFS is the flagship file system of OpenSolaris. The file system is a
combination of file system and logical volume manager. Some of the ZFS
highly steamed features are discussed below.

### ZFS unique features

ZFS is a unique file system which addresses all concerns in the
enterprise data centers. We will discuss these features in this section.

#### Storage pools

ZFS file systems are built on top of storage pools which are built on
top of a group of **virtual devices**. A virtual device can vary from an
entire hard drive down to a file.

#### Capacity

ZFS is a 128-bit file system which can store 1.84 × 10^19^ times more
data than current 64-bit systems.

#### Copy-on-write transactional model

No data get overwritten when updated and instead new data will be wrote
in separate blocks and then the pointers pointing to old data will be
changed to point to new blocks. Multiple write operation can be grouped
together to enhance the performance.

#### Snapshots

ZFS does not overwrite the data when they are changed; this feature is
used to keep **snapshots** of file system for later user.

#### Clones

A **clone** is a writeable snapshot which its block content changes when
a block is changed in another clone.

#### Deduplication

**Deduplication** is a file system level compression method in which
redundant data is eliminated. For example in a typical enterprise mail
server one email attachment may find its way into hundreds of users mail
boxes, Deduplication will store the actual file once and all other
instance of the file are referenced to the same actual file.

We use a console to study all commands discussed in this chapter. So
before we can execute any of these commands we should be in terminal
environment. To activate the terminal environment we can:

Hit ALT+F2 to summon the run window and then type console to open a
console window

Press ALT+CTRL+F2 to switch the workspace to another terminal and use it
to practice the command.

# 1.Using basic file system commands

OpenSolaris supports multiple file systems but to access all of them we
need to know how to use some basic commands. Following table includes
these basic commands along with their descriptions and samples showing
how we can use them.

## Getting ready

We will study some of the very basic file system commands in this
recipe. The commands need to be invoked in the terminal window. To enter
the terminal window Hit ALT+F2 to summon the run window and then type
console to open a console window.

## How to do it...

We have some basic commands which we should know to deal with the file
system in a console. Some of these commands are introduced in the
following table.

<table>
<colgroup>
<col style="width: 15%" />
<col style="width: 84%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Command</td>
<td>Sample</td>
</tr>
<tr class="even">
<td>cd</td>
<td><p>We can change the current directory using cd command. For example
:</p>
<p>cd /home/masoud: switch the current directory to /home/masoud</p>
<p>cd ..: switch one level up from the current directory.</p></td>
</tr>
<tr class="odd">
<td>ls</td>
<td><p>We can use this command to get a list of directories and files.
It works similar to ls in Linux or dir in MS-DOS. For example:</p>
<p>ls: shows the list of all files and directories except hidden
ones.</p>
<p>ls -al: shows the list of all files and directories along with
detailed permissions and creation dates.</p>
<p>ls -al /home: shows content of the given path as explained
above.</p></td>
</tr>
<tr class="even">
<td>mkdir</td>
<td><p>We can create new directories using mkdir command anywhere we
have write permission. For example:</p>
<p>mkdir safe: will create a directory named safe inside the current
directory.</p>
<p>mkdir -p /home/masoud/safe/personal/cash: When using -p it creates
all directories required to form the mentioned path.</p></td>
</tr>
</tbody>
</table>

## How it works..

All commands introduced in this recipe rely on the metada stored for the
file system to perform its job. For example the ls command checks the
metadata available for the path in question to show content of the given
path.

## There\'s more..

We can get a short or long help for any command in OpenSolaris using the
commands help messages or the provided man pages.

### Viewing the help message for a command

We can pass \--help parameter to almost any command in OpenSolaris to
see a short help message including usage syntax and list of options
along with their descriptions. For example:
{{< highlight shell >}}
\# mkdir \--help
{{< /highlight >}}
### Viewing extensive manual of a command

We can see complete and extensive manual pages for any command in
OpenSolaris by passing the command name to man command. For example to
view the man pages for mkdir we can use the following command:
{{< highlight shell >}}
\# man mkdir
{{< /highlight >}}
### Some other commands to review

Following list includes some other commands which we need to know.

Command to copy files and directories: cp

Command to delete file and directories: rm

Command to move or rename files and directories: mv

# 2. Formatting disks

In this task we will learn how we can format and label disks prior to
creating a file system on them.

## Getting ready

When we **attach** a disk to an OpenSolaris machine OpenSolaris will
assign a name to disk and its **partitions** or **slices**.

All disks have assigned names under /dev/dsk and /dev/rdsk which lets us
access the disk at **block level** in the first name and at **bye
level** using the second name. The byte level access method is also
called **raw access** method.

The disks names are usually compatible with C#T#D#S# or C#T#D#P#. The C
stands for controller and the number part is a hex number like 4 or F4.
A **SATA** connector on the mother board is sample of a controller. The
T which is optional stands for target, the D stands for disk and the
number after it points to one of the disks attached to the controller
and the digit after S or P point to the slice number or the partition
number. If the partition is from Solaris type we call it slice and its
identifier is S# while if the partition is non-Solaris it will be
identified with the P#

## How to do it..

We can use format and fdisk commands to format and label the disks prior
to using them. We will discuss format command as it also includes fdisk.

To enter into the format command shell we should invoke format command
in a console which will open the format shell. The initial screen as we
can see in the following figure shows currently attached disks and
allows us to select which disk we want to operate on.

I have two hard disks connected to SATA controller and one to **IDE**
controller. We can enter 1 and the list of available commands appears
which is as shown in the following figure:

![](post-img/3180_02_03.png)

The partition, format and fdisk are most common operations which we
usually use. Typing each one of them opens another set of options which
we can choose to commence with the operation.

Instead of typing the complete command name, we can just enter its first
character. For example we can quit from format shell to operating system
shell or get back to format shell from its subcommands using q instead
of quit.

If we select a brand new disk in the format menu, the format command
informs us that the disk has no partition and we need to use fdisk
command to create some partitions before proceeding. To use fdisk we
just need to enter fdisk subcommand and create partitions as we need or
accept the default partitioning which fdisk command suggest. The default
partitioning will create one **Solaris2** partition occupying the entire
disk.

### Creating a Solaris2 partition

We can create a Solaris 2 partition using the following steps in format
shell:

Enter disk command and then select 1 to operate on c9t0d0

Enter fdisk command and when it asked whether we want to use the default
partition select y so it create one Solaris2 partition occupying the
whole disk.

### Creating a FAT32 partition

Now let\'s create a FAT32 partition in the second disk using the
following steps in the format shell:

Enter disk command and then select 2 to operate on c9t1d0

Enter fdisk command and when it asked whether we want to use the default
partition select n so it will enter the fdisk subcommand shell.

When asked for selection enter 1 to create a new partitioning

When asked for the partition type enter C to create a FAT32 partition

When asked for size, enter 100 to use the whole disk capacity

Enter y to activate the partition

Now that the partition creation is complete enter 5 to apply the changes
and exit fdisk shell.

Now quit the format shell by entering q command.

After creating the partitions we need to apply a file system on them
before actually using them. To apply the file system on Solaris slice we
can use newfs command as follow.
{{< highlight shell >}}
\# newfs /dev/rdsk/c9t0d0s2
{{< /highlight >}}
And to apply the file system on the FAT32 partition we can use the mkfs
with some additional parameters as follow:
{{< highlight shell >}}
\# mkfs -F pcfs -o fat=32 /dev/rdsk/c9t1d0p0:c
{{< /highlight >}}
### Formatting removable disks.

We can use format and fdisk commands to partition a removable media like
a memory card or a thumb drive. For more information and detailed
instructions check
<http://docs.sun.com/app/docs/doc/817-5093/gafmj?a=view>

### Growing a UFS

We can use growfs command to change a UFS size provided that when
creating the file system we use the -T option with newfs command.

## How it works..

When we create a partition in a disk, the layout we specified will be
stored in the **partition table** area of the hard disk located in the
beginning of the disk. The partition table contains all information like
partition type, and its size which we specify when we create the
partition. A good place to learn more about partitioning in general is
http://en.wikipedia.org/wiki/Disk_partitioning

## See also

In the next recipe we will learn how we can mount and put the partitions
we created here in use.

# 3. Mounting and unmounting file systems

Before we can use a file system we should **mount** the slices or
partitions we created in the operating system. When we mount a partition
or a slice we are making it accessible trough a directory which it is
mounted to and we **unmount** the file system we are just detaching the
file system from OS.

## Getting ready

In previous recipes we create two partitions and applied a file system
on each one of them. In this recipe we will mount them to some
directories and start using them. To mount a file system we need a
**mount point.** A mount point is a directory which we use it to access
the mounted file system. So let's create the mount point before mounting
the partitions.
{{< highlight shell >}}
\# mkdir /mnt/fat /mnt/sol
{{< /highlight >}}
This command creates two directories named fat and sol inside the /mnt
directory.

## How to do it..

To mount the Solaris2 partition we created in previous recipe we can use
the mount command as follow:
{{< highlight shell >}}
\# mount /dev/dsk/c9t0d0s2 /mnt/sol
{{< /highlight >}}
This command mounts the Solaris2 slice to /mnt/sol and we can create
files and directories inside it using its mount point. For example:
{{< highlight shell >}}
\# mkdir /mnt/sol/sample
{{< /highlight >}}
If we get the list of /mnt/sol/ we will see something like the following
figure.

![](post-img/3180_02_04.png)

Mounting the FAT32 partition is a bit trickier than Solaris2 partition
as you can see in the command below which mounts the FAT32 partition we
created before to /mnt/fat.
{{< highlight shell >}}
\# mount -F pcfs /dev/dsk/c9t1d0p1 /mnt/fat
{{< /highlight >}}
As you can see we are passing the file system using -F parameter to let
the command know which file system is applied on the partition. Using
the following command we can create a text file containing "Hello FAT32
Partition" inside the FAT32 partition.
{{< highlight shell >}}
\# echo Hello FAT32 Partition \> /mnt/fat/sample.txt
{{< /highlight >}}
We can check partition content using the ls command as we used for
Solaris2 partition.

When we no longer need the file system or we want to take it down for
other operations like checking the file system or moving the drive we
can use umount command to **detach** the file system from the operating
system.

The umount command operates both on the device name like
/dev/dsk/c9t1d0p1 and on the mount point like /mnt/fat. For example to
unmount the FAT32 partition we can use the following command
{{< highlight shell >}}
\# umount /mnt/fat
{{< /highlight >}}
We can not use the umount or mount command when the file system is busy,
for example when an application like terminal is accessing the file
system or just has a path inside that file system open.

## How it works..

When we mount a partition, we are asking the operating system's file
system to allow us access the content of the mounted partition through
the directory we specified as the mount point. Operating system forward
any changes we made to that mount point into the underlying partition

## There\'s more..

Mounted file systems using mount command stays mounted for the period of
the current operating system session and will not persist the system
reboot and after each reboot we will need to mount them again.

We can define the mount points in the **mount table** inside the
/etc/vfstab file to let OpenSolaris pick them an execute them. Each row
of the mount table represents one mount command. The /etc/vfstab is a
plain text file with the following format.

device device mount FS fsck mount mount

to mount to fsck point type pass at boot options

An example row for the mount table to mount our FAT32 partition is as
follow:

/dev/dsk/c9t1d0p1 /dev/rdsk/c9t1d0p1 /mnt/fat pcfs 5 yes -

Let\'s see description of each field in the sample row of mount table.

The block device name to mount.

The raw device name to be used by fsck to check the disk.

The mount point to mount the device.

The file system type to mount the device under it.

Whether the file system should be checked at startup or not.

Whether to mount the device at boot or not.

Additional options for mounting. Options like enforcing a read-only
mount, enabling logging and so on. We applied no option.

### Using fuser to see who is accessing a file system

We can use fuser command to check which process is accessing the file
system. This command comes handy when we want to unmount a device and
umount throw us an error saying the device is busy. For example to check
which process is using the /mnt/fat we can use the following command.
{{< highlight shell >}}
\# fuser /dev/dsk/c9t1d0p1
{{< /highlight >}}
The above command results in something similar to the following figure
if we have, for example a terminal window at /mnt/fat

![](post-img/3180_02_05.png)


we will discuss fuser and pfiles, which gives us more details about who
is using what part of the file system, in more details in chapter.

### Mount options

Following table shows important options we can use with mount command or
in the mount table.
<table>
    <tr>
        <td>Options</td>
        <td>Description</td>
    </tr>
    <tr>
        <td>nologging</td>
        <td>Disable the metadata operation logging.</td>
    </tr>
    <tr>
        <td>noatime</td>
        <td>Disable recording files access time.</td>
    </tr>
    <tr>
        <td>quota</td>
        <td>Enable disk space usage quotas.</td>
    </tr>
    <tr>
        <td>forcedirectio</td>
        <td>Force I/O requests to bypass the file system cache and therefore the operation can be faster for application with internal caches like Oracle and MySQL databases</td>
    </tr>
</table>
## See also

More information about the mount and umount commands is available in
their man pages. And for the mount table we can refer to its official
document at <http://docs.sun.com/app/docs/doc/805-7228/6j6q7uev3?a=view>

# 4. Monitoring UFS

Monitoring is an integral part of all enterprise level activities and
software and hardware systems are not an exception. OpenSolaris provide
several utilities for monitoring the file system activities.

## Getting ready

Following three commands are provided to monitor and check status of the
file system.

The df command report file system disk space usage.

The du command summarizes disk usage of each directory recursively
including the size of included files.

The fsstat command reports kernel file operation activity by the file
system type or by the mount point.

## How to do it..

To view the amount of disk usage and free space on each disk (mount
point) we can use the following command:
{{< highlight shell >}}
\# df -hl
{{< /highlight >}}
The result of using the command is shown in the following figure.

![](post-img/3180_02_06.png)

To view how much space each file and directory occupied we can use the
du command. For example to see how much space is used by
/export/home/masoud/Documents and its subdirectories and files we can
use the following command.
{{< highlight shell >}}
\# du -ah /export/home/masoud/Documents/
{{< /highlight >}}
The command result is something like the following figure.

![](post-img/3180_02_07.png)


We can use -b parameter to make the command show the size in bytes
instead of wrapping it based on the block size. Another useful parameter
is -s which summarizes the space consumption for the directory.

Another monitoring command which we will discuss is fsstate which can
show live statistics about file system usage. For example to see default
monitoring factors for / mount point we can use the following command.
{{< highlight shell >}}
\# fsstat / 5 200
{{< /highlight >}}
This command shows the statistics for 200 times and gathers the sampling
data each 5 second. A sample output is like the following figure.

![](post-img/3180_02_08.png)

In the resulting statistics we can see how many new files are created in
the sample period, how many calls for getting and setting attributes
placed, how many look-up, read, write, etc operations are performed.

## How it works..

Commands that collect and shows statistics about the file systems uses
multiple sources for the statistics they show including the operating
system internal messages, the file system read-only properties and
calculation they make to provide the users with sensible information.

## There\'s more..

There are plenty of options which we can use to customise the result of
each monitoring command. To available options and their usages are
included in the man pages and short help of each command.

There is another monitoring command we can use for monitoring IO
operations called iostat. More information about iostat is available in
its man pages.

# 5. Backing up and restoring UFS

In previous recipe we saw how we can create, mount and monitor UFS
partitions and file systems. In this recipe we will learn how to backup,
and restore a UFS.

## Getting ready

The command we use to create a backup is ufsdump and ufsrestore. These
two commands can create a dump of a UFS like /mnt/sol and then restore
it when required. We should backup a file system when it is not mounted
or mounted in read-only mode to ensure that during the dump operation no
file has changed.

Prior to starting this recipe I copied some files into the UFS slice to
make the backup and restore operations more sensible.

## How to do it..

The ufsdump create backup of the file system. The dump can be an
incremental dump only containing files changed after the previous dump
or it can be a full dump containing all files. We can dump the file
system into tapes or we can dump it into a binary file understandable
for restore command. Following syntax shows how we can use ufsdump
command.
{{< highlight shell >}}
ufsdump \[options\] \[list of arguments\] files_to_dump
{{< /highlight >}}
In this command schema:

The options are a single string of one-letter ufsdump options, some of
important options are listed in this recipe table.

The arguments may be multiple strings whose association with the options
is determined by order. That is, the first argument goes with the first
option that takes an argument and so on. An example of arguments is the
-f parameter argument which specifies where to dump into.

The files_to_dump is required and must be the last argument on the
command line.

For example we can create a full dump of a Solaris2 file system located
on /dev/dsk/c9t0d0s2 and store it the FAT32 partition we mounted in
/mnt/fat using the following command.
{{< highlight shell >}}
\# ufsdump 0fu /mnt/fat/dumped_here /dev/dsk/c9t0d0s2
{{< /highlight >}}
The ufsdump command creates the dumps inside the /dev/rmt/# when no dump
file is specified using the f option. Following figure shows the sample
output of this command.

![](post-img/3180_02_09.png)

Important list of options is shown in the following table.

<table>
    <tr>
        <td>Option</td>
        <td>Description</td>
    </tr>
    <tr>
        <td>0-9</td>
        <td>Specify the dump level. Level 0 means a full dump while other numbers means incremental dump after the previous level. For example if we have a dump level 1 created on Friday, a dump level 1 on Monday will dump all changes happened between Friday and Monday.</td>
    </tr>
    <tr>
        <td>f</td>
        <td>Specify the file which we want to dump the dump files into it. This option need a argument in the argument list</td>
    </tr>
    <tr>
        <td>u</td>
        <td>Update the dump record. Add an entry to the file /etc/dumpdates, for each file system successfully dumped.</td>
    </tr>
    <tr>
        <td>v</td>
        <td>Verify the dump after created to ensure its integrity.</td>
    </tr>
    <tr>
        <td>c</td>
        <td>Set the defaults for cartridge instead of the standard half-inch reel</td>
    </tr>
    <tr>
        <td>L</td>
        <td>Sets the tape label to string, instead of the default value which is none. This option needs a string argument in the arguments list.</td>
    </tr>
</table>


The ufsdump command creates the dumps inside the /dev/rmt/# when no dump
file is specified using the f option.

Now to restore the dump we have just created we can use the ufsrestore
command. The ufsrestore command works in interactive and headless mode.
In the interactive mode it walk us through the procedure step by step
and let us view a dump file content and select what to restore. An
example of using the ufsrestore in interactive more is shown in the
following figure.

![](post-img/3180_02_10.png)

As you can see in the figure we can use some common commands like ls,
add, and extract in the ufsrestore shell to get the content list, add
our selected files to list of extractees and then extract them into the
current working directory using the extract command of the ufsrestore
shell. The following table explains the options used with the ufsrestore
command above.

To use the ufsrestore in the headless mode we should use the following
syntax.
{{< highlight bash>}}
ufsrestore \[options\] \[space separated arguments\] what_to_restore
{{< /highlight >}}
In this schema we have:

The dump_file argument is path to the file we want to restore a file
system content using it, for example /mnt/fat/dump_fileImportant.

The what_to_restore argument is a path pointing to a directory or a file
inside the dump file.

The important options available to the ufsrestore command are included
in the following table.
<table>
    <tr>
        <td>Option</td>
        <td>Description</td>
    </tr>
    <tr>
        <td>i</td>
        <td>Enter the interactive shell of the ufsrestore command</td>
    </tr>
    <tr>
        <td>f</td>
        <td>Path to the dump file. The option need the file name to be passed in the argument list</td>
    </tr>
    <tr>
        <td>x</td>
        <td>Extract the named files into the current directory. For example if the named file is directory, the ufsrestore will restore that directory and its contents into the current working directory.</td>
    </tr>
    <tr>
        <td>r</td>
        <td>Extract the dump content recursively into the current working directory.</td>
    </tr>
</table>

Current working directory is where we are executing the command from.
For example if we issue cd /export/home then current working directory
changes to /export/home. To find the current working directory we can
use pwd command.

Following command restores the entire dump file into the current
directory.
{{< highlight shell >}}
\# ufsrestore -rf /mnt/fat/dump_file
{{< /highlight >}}
![](post-img/3180_02_11.png)


### Other alternatives to ufsdump and ufsrestore

There are some file system independent commands which we can use to
create backup of a file system and then restore it. Some of these
commands are as follow. The ufsdump command has its advantages like
creating incremental backup, support for reels and so on over these
commands.

The tar command is a no compression achiever which can archive a file
system.

The cpio command similar to tar but with capabilities like sending the
archive stream to a remove machine and support for different archive
formats.

The pax command is a combination of tar and cpio capabilities.

The dd command is a low level coping command.

### Creating snapshot of a UFS

We can use fssnap to create snapshot of a UFS. The created snapshot is
read-only and won't sustain a restart. We usually use these snapshots
for creating backup of the mounted file systems.

## How it works..

The ufsdump performs the dumping task by scanning the requested file
system path and creating a list of its content and writing the list into
the specified media. Then it goes through the list and read each file
addressed in the list and store its content in the specified media.
Similarly the ufsrestore read the list, creates the directory layout and
write the file contents into the media specified as restore target.

## See also

In addition to man pages we can learn more the ufsdump and ufsrestore
commands in their reference documents available at
http://docs.sun.com/app/docs/doc/817-3814/6mjcp0r5h?a=view

# 6. Checking UFS for errors

UFS like any other file system can get corrupted in the block level. We
can find the UFS corruptions and to some extend recover from those
corruptions or prevent them from happening.

## Getting ready

In this recipe we will mostly focus on using fsck command to check a
disk and repair the damage if possible. I assume that we have some UFS
partition like /dev/rdsk/c9t0d0s2 in our disposal to study on.

## How to do it..

The fsck command works on raw devices, we discussed raw and block
devices in the Formatting and Labelling recipe, so we should use the raw
name of a device to check for errors.

As you remember from the Mounting and unmounting recipe of this chapter,
OpenSolaris check the file systems for error on start-up if configured.
That checking option kicks tart the fsck command when necessary.

We should not run this command periodically nor should we execute in on
a mounted file system. More importantly, we should not use this command
on extra large disks as it may take days to complete.

Following command checks the /dev/rdsk/c9t0d0s2 and interactively asks
for our permission to perform sensitive operations.
{{< highlight shell >}}
\# fsck /dev/rdsk/c9t0d0s2
{{< /highlight >}}
As you can see in the above command we passed the raw device instead of
the block device name to this command.

## How it works..

UFS uses several mechanism to prevent and recover from corruptions, such
as logging which we discussed in Mounting and unmounting recipe. The
fsck uses the output of these mechanism to check and if required try to
fix the file system.

## There\'s more..

We can try the man page and view the short help message to find more
about this utility using the following commands.
{{< highlight shell >}}
\# man fsck

\# fsck \--help
{{< /highlight >}}
# 7. Specifying disk quotas on UFSs

Like any other advanced file system supports space quota meaning that we
can specify a space usage limitation for users on each file system.

The disk usage limitation can be enforced for a single user or a group
of users over any UFS like /mnt/sol. We can enforce the limitation over
number of consumed **blocks** and **Inodes**.

Each block, depending on its size, can contain 4 KB, 16 KB or any value
specified during the file system creation. So, limiting the number of
blocks limits the amount of data which can be stored in the file system.

Each Inode contains a reference to a file or a directory in the file
system. Limiting number of Inodes limits the number of files and
directories which can be created.

Before we get down to the business, we should understand basics that the
disk **quota** operates on. Following three terms explains these basics.

**Soft limit**: maximum amount of disk space that a user or a group of
users can use. The disk usage by the user or the group can be exceeded
for a certain amount of time. This amount of time is called **Grace
Period.**

Grace Period: A period of time which the soft limit may stay exceeded by
a user or a group of users. The grace period can be specified in
seconds, minutes, hours, days, weeks, or months. This period of time let
the user to get below the soft limit.

**Hard limit:** Specifies a hard limit for the user or group disk usage.
This limitation cannot be exceeded in the Grace Period.

For each quota we specify one line of information is recorded in the
quotas file with the following structure.

## Getting ready

Each file system needs to have a file named quotas owned by root in the
top level. We use edauota command to edit this file and include the
quota for users and groups of of the system.

By default OpenSolaris will use vi to open the quotas file for editing
but vi may not be a convenient editor for new users. We can change the
default editor of the system by changing the value of EDITOR environment
variable by using the following command:
{{< highlight shell >}}
\# export EDITOR=gedit
{{< /highlight >}}
This command change the default editor to **gedit** which is the default
OpenSolaris desktop editor.

## How to do it..

Assuming that we want to define quota over /mnt/sol for a a user named
masoud we need the following steps:

Create the quotas file in the root of /mnt/sol
{{< highlight shell >}}
\# touch /mnt/sol/quotas
{{< /highlight >}}
Changing the ownership of the file to root
{{< highlight shell >}}
\# chmod 0600 /mnt/sol/quotas
{{< /highlight >}}
Specifying the quota using edquota command using the following command.
{{< highlight shell >}}
\# edquota masoud
{{< /highlight >}}
When we execute the above command, gedit window opens with content
similar to the following figure.

![](post-img/3180_02_12.png)

To change the quota values, we just need to change the numbers and press
CTRL+S to save the file and exit gedit.

After specifying the quotas we should turn quotas on if we have not
turned it on using the mount table. Following commands turn quotas on
for the given file system.
{{< highlight shell >}}
\# quotaon /mnt/sol
{{< /highlight >}}
To turn off the quota for a file system we can use the following
command.
{{< highlight shell >}}
\# quotaoff /mnt/sol
{{< /highlight >}}
Now we can get report on quotas using the quota and repquota commands.
For example to check quota for masoud on current file system we can use
the following command:
{{< highlight shell >}}
\# quota -v masoud
{{< /highlight >}}
Output for this command can be similar to the following figure.

![](post-img/3180_02_13.png)


To enable the quotas for a file system permanently, persistent across
reboot, we can use the mount table options. For example we can add the
following line to mount table by editor /etv/vfstab to enable quota for
the /mnt/sol
{{< highlight shell >}}
/dev/dsk/c9t0d0s2 /dev/rdsk/c9t0d0s2 /mnt/sol ufs 5 yes rq
{{< /highlight >}}
We used the last column which specifies mount options to enable quota
for this file system.

## How it works..

When we enable the quotas for a file system, Operating system consult
the quota definitions before storing anything in the file system to see
whether the user has the right to write some data into the disk
according to his disk usage or not.

## There\'s more..

Varieties of commands are available to get more out of quotas. We
discuss some of these commends here.

### Other quotas reporting commands

We can use repquota to view the quota details for a file system. For
example to get detailed report on /mnt/sol file system quotas we can use
the following command.
{{< highlight shell >}}
\# repquota /mnt/sol

Or to view quota information for all users for all file systems we can
use the following command.
{{< highlight shell >}}
\# repquota -av
{{< /highlight >}}
### Specifying quotas for group of users or multiple users

### Specifying the grace period

We can use edquota to specify the grace period for each user. To open
the quota editor for grace period we can use the following command.
{{< highlight shell >}}
\# edquota -t masoud
{{< /highlight >}}
In the editor we just need to specify two numbers for the grace period
of blocks and inodes.

## See also

Looking at the quota, quotaon, repquota, quotacheck, quotaoff man pages
can increase our understanding of quotas in general and these commands
in particular. We can also check UFS management documentation located at
http://docs.sun.com/app/docs/doc/817-0403/sysresquotas-97946?a=view

# 8. Getting list of ZFS pools

In the introduction section of this chapter we said that all ZFS file
systems are build on top of storage pools. So, to work with ZFS, the
first thing to lean is storage pools. In this recipe we will learn how
to get information about available storage pools using zpool command.

## Getting ready

In this recipe we will work on viewing the available zpools, you may
find that the result of my commands are different than what you will see
in your console, this difference can be caused by the fact that I have
more than one pool created while you may only have the rpool which is
created when installing OpenSolaris.

## How to do it..

Following command shows a list of available pools in the system along
with their basic properties\' values.
{{< highlight shell >}}
\# zpool list
{{< /highlight >}}
The result for the executing the command can be similar to the following
figure.

![](post-img/3180_02_14.png)


The list subcommand can show one or more specific pool\'s properties
when we pass them as its arguments. For example to see information about
rpool we can use the following command.
{{< highlight shell >}}
\# zpool list rpool
{{< /highlight >}}
Each pool has some more specific attributes which we can view or edit
their values using zpool list command. For example the following command
will show values for the given listed properties of rpool.
{{< highlight shell >}}
\# zpool list -o available,health,size,used,failmode rpool
{{< /highlight >}}
The above command can result can be similar to the following figure.

![](post-img/3180_02_15.png)


The complete list of a zpool properties other than what we can see when
we get the pool list is shown in the following table. These properties
are editable in contrast with what we can see using the list subcommand.

<table>
    <tr>
        <td>Property</td>
        <td>Description</td>
    </tr>
    <tr>
        <td>altroot</td>
        <td>ZFS datasets mounts under this directory by default.</td>
    </tr>
    <tr>
        <td>autoreplace</td>
        <td>Specifies whether ZFS can replace the devices automatically or not.</td>
    </tr>
    <tr>
        <td>bootfs</td>
        <td>Specifies the bootable dataset in this pool</td>
    </tr>
    <tr>
        <td>cachefile</td>
        <td>Specifies the file used to cache the pool information.</td>
    </tr>
    <tr>
        <td>delegation</td>
        <td>Specifies the dataset delegation possibility for this pool.</td>
    </tr>
    <tr>
        <td>failmode</td>
        <td>Specifies the system reaction to failure</td>
    </tr>
    <tr>
        <td>listsnapshots</td>
        <td>Specifies whether the snapshot list should be visible in when we view zpool status or not.</td>
    </tr>
    <tr>
        <td>version</td>
        <td>Specifies the ZFS version string</td>
    </tr>
    <tr>
        <td>dedup</td>
        <td>Specifies whether deduplication is enabled or not.</td>
    </tr>
</table>

More information about these priperties is available at
http://docs.sun.com/app/docs/doc/819-5461/gfifk?a=view

In addition to using the list subcommand, we can use set and get sub
commands to get values for one or more of there properties or to set
their values.

For example to get value of all properties for rpool we can use the
following command.
{{< highlight shell >}}
\# zpool get all rpool
{{< /highlight >}}
The above command result is shown in the following figure.

![](post-img/3180_02_16.png)


### Using status subcommand

We can use status subcommand to view status of one or more pool. For
example to view status information of all available pools we can use the
following command
{{< highlight shell >}}
\# zpool status
{{< /highlight >}}
To view status of mpool and rpool we can use the following command
{{< highlight shell >}}
\# zpool status mpool rpool
{{< /highlight >}}
### Using set subcommand to change properties\' values

We can change a pool editable properties using set subcommand of zpool.
For example to turn on the deduplication on mpool we can use the
following command
{{< highlight shell >}}
\# zpool set dedup=on mpool
{{< /highlight >}}
## How it works..

The zpool command manipulates the ZFS metadata stored in the /etc/zfs
and we usually do not play with this file unless absolutely necessary.
When the OS is booting it reads the configuration from this directory
and provides the ZFS with it to configure the file system layout.

## See also

More information about zpool command is available in its man pages.

# 9. Creating and destroying a ZFS pool

We can use the zpool command to create or destroy a ZFS pool. A pool can
be created on top of one or more devices like a raw hard disk, a
formatted slice or partition and so on.

## Getting ready

In the second recipe of this chapter we saw that our machine has two
hard disk attached to it, we will use those disks during this recipe to
demonstrate how to create and destroy ZFS pools.

## How to create a pool..

Creating pools is possible using the create subcommand of zpool command.
The syntax for this command considering only important options and
arguments is as follow

zpool create -o property_name=value -m mount_point pool_name
list_of_devices_to_use

For example to create a pool using both of our available disks and
assign it a custom mount point we can use the following command
{{< highlight shell >}}
\# create -m /mnt/fpool fpool c9t0d0 c9t1d0
{{< /highlight >}}
A new pool will be created on top of both c9t0d0 and c9t1d0 devices. As
we used the -m option, the new pool mounts under /mnt/fpool instead of
the default mount point follow the /pool_name syntax. Without using the
-m /mnt/fpool our pool mounts as /fpool. Following figure shows the
mount point and status of the newly created pool.

![](post-img/3180_02_17.png)

Expanding a ZFS pool

We can **expand** a pool using add subcommand of the zpool command. For
example if we need to add another 2TB disk named /dev/dsk/c9t4d0 to
fpool we can use the following command.
{{< highlight shell >}}
\# zpool add fpool /dev/dsk/c9t4d0
{{< /highlight >}}
This will immediately add the space to the pool right after the command
executed successfully.

## How to destroy a pool..

Destroying a pool is as simple as executing one single command. For
example to destroy fpool we can use the following command
{{< highlight shell >}}
\# zpool destroy -f fpool
{{< /highlight >}}
When we create a pool with a custom mount point, the mount point we used
will remain after destroying the pool. For example in our case the
/mnt/fpool directory remains after destroying the fpool itself. When we
use default mount points, zpool command remove the mount point after
destroying the pool.

## How it works..

When we create or destroy a pool, ZFS checks the parameters we assigned
and changes the metadata associated with ZFS pools located at /etc/zfs
to include the new zpool in the file system layout or to remove the pool
from the file system layout.

## See also

In the next recipe we will lean mirroring ZFS pool underlying devices.

# 10. Mirroring in ZFS pools

In this recipe we will study ZFS **mirroring**. We will learn how to add
mirroring capabilities to a pool after the pool is created or during the
pool creation.

## Getting ready

When we mirror a pool, we basically mirror one or more of the pool\'s
devices on one or more identical devices in term of capacity. We can not
mirror a 1 TB device with a 2 TB device as they are not identical.

## How to do it..

We can configure mirroring when we create a pool or we can add the
mirroring configuration after we created the pool normally as we did in
previous recipe.

Following commands shows how we can create a pool and then add a mirror
to one of its underlying devices.
{{< highlight shell >}}
\# zpool create fpool c9t0d0
{{< /highlight >}}
This command creates a pool with one underlying device.
{{< highlight shell >}}
\# zpool attach fpool c9t0d0 c9t1d0
{{< /highlight >}}
This command attaches the c9t1d0 as a mirror for c9t0d0. We can add as
many mirrors for a device as we need. After attaching the mirror device
all existing data from the active device **resilver** into the mirror.

Following figure illustrates steps to create the pool and attaching a
mirror to one of its disk.

![](post-img/3180_02_18.png)


To mirror a disk during pool creation with the same layout as above we
can use the following command.
{{< highlight shell >}}
\# zpool create fpool mirror c9t0d0 c9t1d0
{{< /highlight >}}
This command creates fpool on top of c9t0d0 mirrored on c9t1d0.

We may need to detach a disk for maintenance tasks or to replace it with
another disk. The detach subcommand of zpool let us detach a mirror from
a pool while allowing the pool to function. For example we can use the
following command to detach c9t1d0 from fpool.
{{< highlight shell >}}
\# zpool detach fpool c9t1d0
{{< /highlight >}}
We can replace a device forming a pool using the replace subcommand.
This command detaches one device and attaches the new one in its place.
{{< highlight shell >}}
\# zpool replace fpool c9t1d0 c9t2d0

The command detaches c9t1d0 and attaches the c9t2d0 to fill its place.

We can take a device offline to perform maintenance tasks on it. When we
take a device offline, the pool status change to **degraded** which
means it may not function it its full capacity. For example to take
c9t1d0 **offline** we can use the following command.
{{< highlight shell >}}
\# zpool offline fpool c9t1d0

We can bring the device **online** using the online subcommand of the
zpool command as follow.
{{< highlight shell >}}
\# zpool online fpool c9t1d0
{{< /highlight >}}
## How it works..

A mirrored pool ensures that for each block, there is one or more copies
on the mirror devices attached to the pool. Using mirrors guarantee the
availability of our data in case of a disaster like a hardware failure
in one of the devices but it introduces performance degradation as any
changed block need to get propagated to any number of mirrors.

ZFS mirroring is superior in several aspect including but not limited
to:

Detecting a back block on one disk and repairing it using a identical
good block from one of the mirrors.

Duplication a device data into its mirror is faster when the whole
device space is not occupied as duplication only duplicates the blocks
which contain data not all of them. This duplicating operation is called
resilvering.

## See also

In the next recipe we will study how we can create a RAID using ZFS
mirroring capabilities.

# 11. RAID in ZFS

A **Redundant Array of Inexpensive Disks** (**RAID**) provides the data
availability guarantees that mirroring provides while it does not impose
performance degradation level that mirroring does.

OpenSolaris implements its own version of **RAID 5** on top of ZFS
supporting both **single-parity** and **double-parity** modes. These two
models are named raidz1 and raidz2. In this recipe we will see how we
can create these two types.

## Getting ready

I added one more disk to my OpenSolaris distribution so I can
demonstrate raidz2 as well as raidz1 because raidz2 needs at least three
disks to operate. So we have c9t0d0, c9t0d1 and c9t0d2 disks attached to
the OpenSolaris machine.

## How to do it..

We can create RAID configuration using the zpool create command using
the following syntax.
{{< highlight shell >}}
\# zpool create fpool \<raidz1, raidz2\> list_of_devices
{{< /highlight >}}
For example to create a raidz1 using c9t0d0, c9t1d0 we can use the
following command.
{{< highlight shell >}}
\# zpool create fpool raidz1 c9t0d0, c9t1d0
{{< /highlight >}}
And now if we check the status of our newly created pool we will see
something like the following output.

![](post-img/3180_02_19.png)

There is a concept and functionality named **spare** devices or
**spares** which we can use to further enhance the reliability of our
mirrors or RAID configuration. A spare device stays inactive in a pool
until one of the devices forming the pool fails, as soon as a device
fails; ZFS activate one of the available spares and continue functioning
as it should.

We can add spare devices to a pool after we created the pool or we can
add the during the pool creation. For example we can add the c9t2d0 to
fpool as a spare device using the following command.
{{< highlight shell >}}
\# zpool add fpool spare c9t2d0
{{< /highlight >}}
Now if we check the pool status we can see a new set named spares added
to the pool itself.

![](post-img/3180_02_20.png)

To add the same spare device during the pool creation we can use
following command.
{{< highlight shell >}}
\# zpool create fpool raidz1 c9t0d0 c9t1d0 spare c9t2d0
{{< /highlight >}}
A ZFS raidz1 is a single parity RAID and we need at least two disks to
create one. The raidz1 sustain a single disk failure. We can create a
raidz2 using at least three disks to keep the system operating in case
of two disks failure. Following command shows how we can create a raidz2
configuration.
{{< highlight shell >}}
\# zpool create fpool raidz2 c9t0d0 c9t1d0 c9t2d0
{{< /highlight >}}
## How it works..

The RAID algorithms and undelaying technologies is sophisticated and out
of this book context how ever, we can simply say that when we configure
a raid, we are asking the RAID driver to distribute stripes of data
between the available disks and compute parity information for each
strip to ensure that we can recover a strip if one or more disks in the
array failed.

Configuring an storage system can become quite complex when we are
dealing with mirrors, spares and RAID configuration because we can have
spares inside a mirror, or inside a RAID or we may have mirrors with
spares inside a RAID array and so on.


## See also

To learn more about RAID we can start with its Wikipedia page available
at http://en.wikipedia.org/wiki/RAID

To learn more about ZFS RAID configuration we can consult following
documents.

<http://www.solarisinternals.com/wiki/index.php/ZFS_Best_Practices_Guide>

# 12. Importing and exporting ZFS pools

We can export a zpool from one machine to safely detach it from that
machine and prepare it to be attached to another machine. Although
OpenSolaris runs on variety of platforms ranging from X86 machine up to
bleeding edge SPARC servers but it is endian-ness independent so we can
export a zpool from one machine and import it to another machine even
with a different endianness.

## Getting ready

In this recipe we will export the fpool and then import it to another
machine using import and export subcommand of the zpool command.

## How to do it..

To export fpool, which is a ZFS pool, we can use the following command
{{< highlight shell >}}
\# zpool export fpool
{{< /highlight >}}
Right after executing these command we will not be able to access its.
When we attached the underlying disks to another machine we can use the
zpool import subcommand to import the pool into the new machine. After
we attach the new disks to a machine we can check whether the pool is
ready to be imported or not using the following command.
{{< highlight shell >}}
\# zpool import
{{< /highlight >}}
Following figure shows output of the command indicating that a pool
named fpool is ready to be imported. We can also import the pool using
its id when there are more than one pool with the same name available
for import.

![](post-img/3180_02_21.png)

Now that we have the pool ready to get imported we can use the following
command to import it.
{{< highlight shell >}}
\# zpool import fpool
{{< /highlight >}}
The Indian-ness independency of ZFS pools only applies to pools created
on top of whole disks and not a partition or slice of a disk.

We used the most basic form of the import command for simplicity sake,
while we could use several options to further customize the import
operation. Some of the important options of the import command is shown
in the following table.

<table>
    <tr>
        <td>Option</td>
        <td>Description</td>
    </tr>
    <tr>
        <td>-d dir</td>
        <td>Specifies a path to search for the available pools for importing and prevent zpool to search for available pools.</td>
    </tr>
    <tr>
        <td>-o montopts</td>
        <td>Specifie a comma separated list of mount options.</td>
    </tr>
    <tr>
        <td>-f</td>
        <td>Force the import operation even some errors are preventing the import.</td>
    </tr>
</table>

## How it works..

When we issue the import command it scans for information about
available pools in all **virtual devices** (**vdevs**) and constructs
the pool layouts using the cached information on the vdev and then
present us with the available information so we can decide whether we
want those pool imported into our available zpools or not.

## There\'s more..

You can use the zpool import -D command to recover a storage pool that
has been destroyed. Because when we destroy a zpool we are only removing
the information from the ZFS metadata storage and we are not actually
destroying the pool.

## See also

# 13. Monitoring ZFS pools performance

Performance, performance, performance; this is what we hear in all
software development and management sessions. ZFS provides few utility
commands to monitor one or more pools\' performance.

## Getting ready

You should remember that we used fsstat command to monitor the UFS
performance metrics. We can use iostat subcommand of the zpool command
to monitor the performance metrics of ZFS pools.

## How to do it..

The iostat subcommand provides some options and arguments which we can
see in its syntax shown below:
{{< highlight shell >}}
iostat \[-v\] \[pool\] .. \[interval \[count\]\]
{{< /highlight >}}
The -v option will show detailed performance metrics about the pools it
monitor including the underlying devices. We can pass as many pools as
we want to monitor or we can omit passing a pool name so the command
shows performance metrics of all commands. The interval and count
specifies how many times we want the sampling to happen what is the
interval between each subsequent sampling.

For example we can use the following command to view detailed
performance metrics of fpool for 100 times in 5 seconds interval we can
use the following command.
{{< highlight shell >}}
\# zpool iostat -v fpool 5 100
{{< /highlight >}}
The output for this command is shown in the following figure.

![](post-img/3180_02_22.png)

The first row shows the entire pool capacity stats including how much
space were used upon the sampling and how much was available. The second
row shows how many reads and writes operations performed during the
interval time and finally the last column shows the band width used for
reading from this pools and writing into the pool.

## How it works.. 

The zpool iostat retrieve some of its information from the read-only
attributes of the requested pools and the system metadata and calculate
some other outputs by collecting sample information on each interval.

## There\'s more..

## See also

For viewing UFS performance take a look at recipe 4: Monitoring UFS
recipe.

# 14. Auditing ZFS pools

Auditing is an integral part of any administrator working on large scale
deployments to check and assess possible risks or trace the changes and
operations performed in a specified duration of time or an exact point
in time.

## Getting ready

In this recipe we will use the history subcommand of the zpool command
to view the ZFS pools structural changes. We assume that we have a pool
named fpool at our disposal to check the command with.

## How to do it..

The history command syntax is as follow
{{< highlight shell >}}
history \[-il\] \[\<pool\>\] ...
{{< /highlight >}}
The -i option shows ZFS internal operations in addition to operations
requested by users. The -l option shows additional information like the
user requested the operation, the host which initiated the request and
so on. Using the history command without passing the list of pool names
shows the history of changes for all pools.

Following command shows normal records of fpool history.
{{< highlight shell >}}
\# zpool history fpool
{{< /highlight >}}
Following figure shows output of the above command.

![](post-img/3180_02_23.png)


You can see that we only performed three operations on the zpool
structure including executing create, export and import command on it.

## How it works..

All changes we make on a zpool are stored in the ZFS metadata history
and the history command uses these storage to shows what changes the
pool under question has gone through.

