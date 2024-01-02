# Static Resume in AWS
This is a blog-style repository to describe what I've learnt while doing the [Cloud Resume challenge](https://cloudresumechallenge.dev/docs/the-challenge/aws/) in AWS.
## Host: EC2 vs. S3
<details>

While the authors of the challenge recommended choosing S3 - which makes a lot of sense for static web-pages - I decided to go for EC2 for a couple of reasons. First, I wanted to get some first-hand experience with setting up an Apache server, and second, it was my first real hands-on experience working with Linux, and I was excited to give it a try.

Amazon offers several of their specialized Amazon Linux AMIs, but I opted for Ubuntu as a popular and well-documented OS. It turned out to be the right decision, as every problem I encountered during my work were already covered in helpful threads on Stack Overflow and reddit. It was also a great way to learn more about how SSH connections work - either directly or via a bastion host for instances in private subnets.

Some of the useful and most common commands that I learnt include:

**ls** - lists the contents of the current directory. Helpful options: **-a** to list all entries, **-s** to print the file size, **-l** to present the results in the long list format.

**lsblk** - lists the information about current volumes and partitions. Useful to keep track of the connected EFS and EBS volumes.

**clear** - clears the screen for convenience.

**pwd** - prints the current directory name.

**cd ..** - navigates up a directory.

**whoami** - shows the name of the current user.

**tty** - prints the name of the terminal connected to standard input.

**df** - view the information about the file systems. Useful to keep track of the connected EFS and EBS volumes and to search the instance for large outdated files in need of a clean-up. It helped me to quickly find and remove an obsolete swap file after a hibernation. Helpful options: **-h** for a more convenient presentation of file and directory sizes.

**sudo su -** and **sudo passwd [username]** - useful for changing the default passwords of the root and ubuntu users.

**echo "text" > path/file.txt** - writes the contents of "text" to the specified file. Can also be used in the user data section when spinning up a new instance, e.g., to write something to index.html on start-up.

**cat path/file.txt** - shows (concatenates) the standard output of a file. Easier and faster than viewing a document in vim every time.
</details>

## File System: EFS vs. EBS
<details>
The next choice was between EFS and EBS, so I asked myself: Why not both? Working with these file systems taught me about the process of mounting and unmounting them to an instance and automating the process via the **/etc/fstab** configuration.

EBS is straightforward in that regard and only requires editing the inbound rules in the corresponding security groups, but EFS also needs a mounting point that can be found by going to the EFS menu in AWS Console, choosing the newly created file system, selecting View Details, and then Attach.

Then, to avoid repeating this process upon stopping and restarting the instance, the mount sudo command has to be added at the end of **/etc/fstab** file.

Some of the useful and most common commands that I learnt include:

**sudo mount [options] File_System_Name:/ /path** - mounts the file system at the designated path. Helpful options: **-f** to test the mounting process in a dry run, **-a** to mount all the file systems in fstab, **-v** for a verbose description of what's being done.

One especially important detail was learning the difference between **efs** and **nfs4** options in the manual mounting process. Amazon provides EFS mount helper to simplify the automatic mounting process, but without the set of **amazon-efs-utils** tools, this option has to be changed to **nfs4** in the **sudo mount** command to avoid errors.
</details>
