# Static Resume in AWS
This is a blog-style repository to describe what I've learnt while doing the [Cloud Resume challenge](https://cloudresumechallenge.dev/docs/the-challenge/aws/) in AWS.

> [!IMPORTANT]
> In case of any errors caused by the failed authorization, the following command can be used in the CLI to decode the failure message:
> `aws sts decode-authorization-message --encoded-message <encoded message from the error>`
## Host: EC2 vs. S3
<details>

While the authors of the challenge recommended choosing S3 - which makes a lot of sense for static web-pages - I decided to go for EC2 for a couple of reasons. First, I wanted to get some first-hand experience with setting up an Apache server, and second, it was my first real hands-on experience working with Linux, and I was excited to give it a try.

Amazon offers several of their specialized Amazon Linux AMIs, but I opted for Ubuntu as a popular and well-documented OS. It turned out to be the right decision, as every problem I encountered during my work was already covered in helpful threads on Stack Overflow and reddit. It was also a great way to learn more about how SSH connections work - either directly or via a bastion host for instances in private subnets.

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

**sudo ncdu -x /path/** - uses NCDU disk utility to provide a convenient text-based UI to quickly navigate through directories and check their size. **/path/** denotes the partition, can be replaced with **/** for the root file system. **-x** performs a full scan.
</details>

## File System: EFS vs. EBS
<details>
  
The next choice was between EFS and EBS, so I asked myself: Why not both? Working with these file systems taught me about mounting and unmounting them to an instance and automating the process via the **/etc/fstab** configuration.

EBS is straightforward in that regard and only requires editing the inbound rules in the corresponding security groups, but EFS also needs a mounting point that can be found by going to the EFS menu in AWS Console, choosing the newly created file system, selecting View Details, and then Attach.

Then, to avoid repeating this process upon stopping and restarting the instance, the mount sudo command has to be added at the end of **/etc/fstab** file.

Some of the useful and most common commands that I learnt include:

**sudo mount [options] File_System_Name:/ /path** - mounts the file system at the designated path. Helpful options: **-f** to test the mounting process in a dry run, **-a** to mount all the file systems in fstab, **-v** for a verbose description of what's being done.

One especially important detail was learning the difference between **efs** and **nfs4** options in the manual mounting process. Amazon provides EFS mount helper to simplify the automatic mounting process, but without the set of **amazon-efs-utils** tools, this option has to be changed to **nfs4** in the **sudo mount** command to avoid errors.
</details>

## Content: HTML and CSS
<details>

After setting up the server, it was the time to figure out how to write the web content. Learning the difference and use cases behind HTML and CSS allowed me to differentiate between the structure and style of my web page and understand the reasons behind the best practices associated with them.

I figured out how to create multiple pages in **/var/www/** sub-directories and connect them with `<a href="filename.html"></a>` commands. I made sure to avoid any individual style edits in my HTML files for this project and do all the formatting via the CSS, although the option to correct particular elements with the inline `style` attributes within elements or with the `<style></style>` element within the `<head>` section is also helpful in a pinch and overrides whatever is written in the attached CSS.

A quick example:

`<li style="color:green;">List Item</li>` - produces a green list item.

While this fragment defines an internal CSS for an entire page: 

```
<!DOCTYPE html>
<html>
<head>
<style>
body {background-color: powderblue;}
h1   {color: blue;}
p    {color: red;}
</style>
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```
</details>
