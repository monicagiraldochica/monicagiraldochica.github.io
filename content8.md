# 8. General file manipulation

## 8.1. Basic concepts and simple commands

### The absolute path of a file

The absolute or full path of a file specifies its unique location in the file system, its name and extension. The absolute path of every file is different and constant. It will always be the same regardless of the current directory in which you are working (unless you move the file). This path follows a hierarchy of directories where the file is located, separating each directory with the delimiting character, which is the slash for Macintosh and Linux and the backslash for Windows. For example, a text file named file.txt located in the Desktop of a Mac computer, will commonly have an absolute path similar to this:
/Users/user_name/Desktop/file.txt
To find the absolute path of a file you can drag and drop it into the Terminal.

For example, if this is the absolute path of a file:
/Users/user_name/Desktop/SomeFolder/Subfolder/file.txt
It means that in the user Desktop there is a folder called SomeFolder, inside SomeFolder there is another folder called Subfolder, and inside Subfolder is located the corresponding file, with name file and extension txt. Inside a directory there can be two files with the same name but in this case, they must have a different extension. There cannot be two files with both the same name and same extension inside the same folder.

### The relative path of a file

The relative path of a file starts from the current working directory and represents the location of a specific file relative to this directory. It is always changing as it depends on what directory you are located in the terminal, but is a lot shorter than the absolute path.

If the absolute path of a file is the following, meaning the file is located in the Desktop:
/Users/user_name/Desktop/file.txt
And the user current working directory is the desktop, then the relative path of the file is:
file.txt or ./file.txt
Since the file is located in the current directory, the user can use just the name and extension for the relative path. Adding ./ doesn't do anything because ./ represents the current directory.

The following relative paths represent files that are located in the current working directory:
File1.txt
File2.nii.gz
./File3.nii
./File4.docx

The absolute path of these files will be something similar to this (for Mac users), if the current directory is the Desktop:
/Users/username/Desktop/File1.txt
/Users/username/Desktop/File2.nii.gz
/Users/username/Desktop/File3.nii
/Users/username/Desktop/File4.docx

The same way that one dot represents the current directory in a relative path, two dots represent the parent directory. So, if the absolute path of my current working directory is the following:
/Users/user_name/Desktop/SomeFolder
I can refer to files located in the parent directory (Desktop) using the double dots. So, all these files are located in the Desktop (the same as SomeFolder):
../FileA.txt
../FileB.txt
../FileC.txt

### Some tips about file paths

Both the absolute and relative paths are used to reference files when doing operations with it, but I generally prefer to use the absolute path when writing scripts because that way my script will run with no issues independently of my working directory. Never the less, when doing fast operations in the Terminal (like renaming or copying a file), then it's faster to use the relative path.

It is also recommended to not use spaces or any other special characters when naming a file, because it makes it harder to write the path of that file. For example, if I have a file named Some File.txt in my Desktop (on a Mac), this is not the absolute path:
/Users/user_name/Desktop/Some File.txt
Instead, the absolute path will be:
/Users/user_name/Desktop/Some\ File.txt

The backslash is telling us to not treat the space as a special character but instead as part of the file path. This works the same way in Linux and MacOS.

The same will happen with a file which name contains the dollar sign (which is a special character). For example, if in the Desktop there is a folder named untitled$folder, this is not the absolute path:
/Users/user_name/Desktop/untitled$folder
Instead, the absolute path will be:
/Users/user_name/Desktop/untitled\$folder

Again, the backslash is indicating us to treat the special character ($) as part of the file name and not as the beginning of a variable name (which is the normal use of $ in bash). If we don't use the backslash before the dollar sign, bash will try to find a variable called folder ($folder normally refers to the value of folder as a variable, not a string). If that variable doesn't exist, then bash will read /Users/user_name/Desktop/untitled$folder as /Users/user_name/Desktop/untitled, replacing $folder for an empty space.

In the following sections you will learn how the absolute and relative paths are used to manipulate files **in MacOS and Linux**.

### The `pwd` command

Prints the absolute path of the current working directory.

### The `ls` command

Lists the files and folders inside the specified directory. If no directory is specified after `ls`, then it prints the contents of the current folder. If no flag is used, it lists the files in bare format (without any details such as modified date and time or permissions). Depending which flag is used (or a combination of flags), then specific information about the file will be displayed. The following table is taken from manual of the `ls` command as displayed in the command line.

**List of flags that I use more often:**

| Flag | Use |
| ----------- | ------- |
| `-a` | Include directory entries whose names begin with a dot (`.`). Typically, these are configuration files for applications or hidden files. |
| `-G` | Enable colorized output. This option is equivalent to defining `CLICOLOR` in the environment. |
| `-H` | Symbolic links on the command line are followed. This option is assumed if none of the `-F` or `-l` options are specified. |
| `-l` | Force output to be one entry per line. This is the default when output is not to a terminal. |
| `-lh` | Use unit suffixes when displaying the information in long output: Byte, Kilobyte, Megabyte, Gigabyte, Terabyte and Petabyte, in order to reduce the number of digits to three or less using base 2 for sizes. |
| `-ln` | Display user and group IDs numerically, rather than converting to a user or group name in a long output. |
| `-R` | Recursively list subdirectories encountered. |
| `-S` | Sort files by size. |
| `-t` | Sort by time modified (most recently modified first) before sorting the operands by lexicographical order. |

**Other flags:**

| Flag | Use |
| ----------- | ------- |
| `-A` | List all entries except for (`.`) and (`..`). Always set for the super-user. |
| `-B` | Force printing of non-printable characters in file names as `\xxx`, where `xxx` is the numeric value of the character in octal. |
| `-b` | As `-B`, but use C escape codes whenever possible. |
| `-C` | Force multi-column output; this is the default when output is to a terminal. |
| `-c` | Directories are listed as plain files (not searched recursively). |
| `-F` | Display a slash immediately after each pathname that is a directory, an asterisk after each that is executable, an at sign (`@`) after each symbolic link, an equal sign after each socket, a percent sign after each whiteout, and a vertical bar after each that is a FIFO. |
| `-f` | Output is not sorted. This option turns on the `-a` option. |
| `-i` | For each file, print the files file serial number (inode number). Each object in the file system is represented by an inode. |
| `-k` | If the `-s` option is specified, print the file size allocation in kilobytes, not blocks. This option overrides the environment variable `BLOCKSIZE`. |
| `-L` | Follow all symbolic links to final target and list the file or directory the link references rather than the link itself. This option cancels the `-P` option. |
| `-l@` | Display extended attribute keys and sizes in long output. |
| `-le` | Print the Access Control List (ACL) associated with the file, if present, in long output. |
| `-lg` | This option is only available for compatibility with POSIX; it is used to display the group name in the long format output (the owner name is suppressed). |
| `-lO` | Include the file flags in a long output. |
| `-lT` | Display complete time information for the file, including month, day, hour, minute, second, and year. |
| `-m` | Stream output format; list files across the page, separated by commas. |
| `-o` | List in long format, but omit the group id. |
| `-P` | If argument is a symbolic link, list the link itself rather than the object the link references. This option cancels the `-H` and `-L` options. |
| `-p` | Write a slash after each filename if that file is a directory. |
| `-q` | Force printing of non-graphic characters in file names as the interrogation character; this is the default when output is to a terminal. |
| `-r` | Reverse the order of the sort to get reverse lexicographical order, or the oldest entries first (or largest files last, if combined with sort by size). |
| `-s` | Display the number of file system blocks actually used by each file, in units of 512 bytes, where partial units are rounded up to the next integer value. If the output is to a terminal, a total sum for all the file sizes is output on a line before the listing. The environment variable `BLOCKSIZE` overrides the unit size of 512 bytes. |
| `-u` | Use time of last access, instead of last modification of the file for sorting (`-t`) or long printing (`0-l`) |
| `-U` | Use time of file creation, instead of last modification for sorting (`-t`) or long output (`-l`). |
| `-v` | Force unedited printing of non-graphic characters; this is the default when output is not to a terminal. |
| `-W` | Display whiteouts when scanning directories. |
| `-w` | Force raw printing of non-printable characters. This is the default when output is not to a terminal. |
| `-x` | The same as `-C`, except that the multi-column output is produced with entries sorted across, rather than down the columns. |

The following examples show the usage of the flags presented in the previous table and details of the results that can be obtained when using the command `ls` together with those flags.

#### `ls` with no flags

Show the list of the files in the working directory:

```bash
$ ls
Applicationshome
Libraryinstaller.failurerequests
Networknet
Subjectsopt
Usersprivate
Volumessbin
binmp
coresusr
devvar
etc
```

Show the list of files in a different directory using *the absolute path* of that directory:

```bash
$ ls /Volumes/MyExternalDrive
ArticlesMRIdata SharedFolder
```

Show the list of files in a subfolder (Applications) using *the relative path*:

```bash
$ ls Applications/
Calculator.app
Calendar.app
Chess.app
Dashboard.app
Dictionary.app
EndNote X7
EndNote X8
FaceTime.app
Firefox.app
IBM
Image Capture.app
Launchpad.app
MATLAB_R2014b.app
```

#### `ls -l`

When using the flag `-l`, bash will not only show the list of files but also a description for each file. The output will be organized in columns, each one with a specific meaning that I will explain in the table below.

```bash
$ ls -l /Volumes/MyExternalDrive
Total 0

drwxr-xr-x	22	myUser	UserGroup	714	Sep 4	11:40	Articles
-rw-r--r--@	1	myUse	UserGroup	51620	Jan 14	2017	CV.docx
-rw-r--r--	1	myUser	UserGroup	137195	Jan 14	2017	CV.pdf
drwxr-xr-x	28	myUser	UserGroup	952	Sep 10	09:04	MRIdata
drwxrwxrwx	5	myUser	UserGroup	170	Sep 4	11:15	SharedFolder
```

| Column number | Column name | Meaning |
| ----------- | ------- | ------- |
| 1 | File type + permissions | [See below](#file-type-and-permissions-from-the-ls--l-command) for the explanation of the output presented in this column. |
| 2 | Link count | Number of hard links to the file. Data is only removed from your drive when all hard links to the data have been removed. |
| 3 | Owner name | [See below](#file-type-and-permissions-from-the-ls--l-command) for the explanation of file ownership and permissions. |
| 4 | Group name | [See below](#file-type-and-permissions-from-the-ls--l-command) for the explanation of file ownership and permissions. |
| 5 | File size | Number of bytes in the file. See the flag `-lh` to output the size of the file in human-readable format. |
| 6 | Last modified date | Abbreviated month and day-of-month when the file was last modified. |
| 7 | Last modified time | Abbreviated hour and minute when the file was last modified in military format (24 hours). If the file was last modified more than six months before, then the year of the last modification is displayed instead of the hour and minute. |
| 8 | File name | Name of the file with its extension. |

#### File type and permissions from the `ls -l` command

The first column of the output printed when using the flag `-l` shows the file type and permissions. This column has 10 characters. Each character will be a letter or a dash.

**The first character** describes the type of file and can be one of the following:

| Character | File type |
| ----------- | ------- |
| `b` | Block special file: provide access to hardware devices. |
| `c` | Character special file: provide access to hardware devices. |
| `d` | Directory. |
| `l` | Symbolic link: a file that points to another file (a shortcut). It does not contain the data in the target file; it simply contains a pointer somewhere in the file system. |
| `s` | Socket link: file used for communication between processes. |
| `p` | FIFO: special file that can be opened by various processes for exchanging data. |
| `-` | Regular file. |

**The second character** shows if the user listed as *owner* has reading permissions. It will be an `r` if the owner can read the file, or a `-` if not.
**The third character** shows if the user listed as *owner* has writing permissions. It will be a `w` if the owner can write in the file, or a `-` if not.
**The fourth character** shows if the user listed as *owner* has execution permissions. It will have value `x` if the owner can execute the file, or a `-` if not.

**The fifth character** shows if the users that are part of the **group** have reading permissions. It will have an `r` if they can read, or a `-` if not.
**The sixth character** shows if the users that are part of the **group** have writing permissions. It will have a `w` if they can write, or a `-` if not.
**The seventh character** shows if the users that are part of the **group** have execution permissions. It will have a `x` if they can execute, or a `-` if not.

**The eight character** shows if **other** users have reading permissions. It will have an `r` if they can read, or a `-` if not.
**The ninth character** shows if **other** users have writing permissions. It will have an `w` if they can write, or a `-` if not.
**The tenth character** shows if **other** users have execution permissions. It will have a `x` if they can execute, or a `-` if not.

To change the permissions of a file you would use the `chmod` command. This command has the following syntax: `chmod <num_for_owner><num_for_group><num_for_others> <file>`

`<num_for_owner>` is a number to change the **owner** permissions.
`<num_for_group>` is a number to change the **group** permissions.
`<num_for_others>` is a number to change the permissions of other users.

These numbers can be one of the following:

| Number | Characters in `ls -l` | Meaning |
| ----------- | ------- | ------- |
| 7 | `rwx` | Give full permissions (read, write and execute) |
| 6 | `rw-` | Give read and write permissions, but not execute |
| 5 | `r-x` | Give read and execute permissions, but not write |
| 4 | `r--` | Give only read permissions |
| 3 | `-wx` | Give write and execute permissions, but not read |
| 2 | `-w-` | Give only write permissions |
| 1 | `--x` | Give only execute permissions |
| 0 | `---` | Give no permissions |

For example if you run `chmod 775 myfile`, the owner and group will have full permissions, other users will be able to read and execute but not write.
If you run `chmod 744 myfile`, the owner will have fill permissions, users belonging to the group and other users will only be able to read, but wont be able to write or execute it.

#### Human readable file size with `ls -lh`

The information displayed in the 5th column (file size) when using `ls -l` is difficult to interpret by a human. In order to see this information in a human-readable format, it is only necessary to add an `h` to the `-l` flag.

```bash
$ ls -lh /Volumes/MyExternalDrive
Total 0
drwxr-xr-x	22	myUser	UserGroup	714B	Sep 4	11:40	Articles
-rw-r--r--@	1	myUser	UserGroup	50K	Jan 14	2017	CV.docx
-rw-r--r--	1	myUser	UserGroup	134K	Jan 14	2017	CV.pdf
drwxr-xr-x	28	myUser	UserGroup	952B	Sep 10	09:04	MRIdata
drwxrwxrwx	5	myUser	UserGroup	170B	Sep 4	11:15	SharedFolder
```

When using the `-lh` flag, the size of the file will be followed by a letter which represents the units:

| Letter | Unit | number of bytes per unit |
| ----------- | ------- | ------- |
| B | Bytes | 2^0 |
| K | Kilobytes | 2^10 |
| M | Megabytes | 2^20 |
| G | Gigabytes | 2^30 |
| T | Terabytes | 2^40 |
| P | Perabytes | 2^50 |

#### Using multiple flags

Most of the flags can be combined, except for the following group of flags which override each other (either partially or fully). If used together, only the last one specified will be the format used to display the information. Or if possible they will be applied in the order specified.

- `-1`, `-C`, `-x`, and `-l`
- `-c` and `-u`
- `-B`, `-b`, `-w`, and `-q`
- `-H`, `-L` and `-P`

The example below combines `-lh`, `-o`, and `-r` to print the information in long format, using human readable file sizes (`-lh`), omitting the group id (`-o`) and in reverse lexicographical order (`-r`):

```bash
$ ls -lhor /Volumes/MyExternalDrive
Total 0

drwxrwxrwx	5	myUser	170B	Sep 4	11:15	SharedFolder
drwxr-xr-x	28	myUser	952B	Sep 10	09:05	MRIdata
-rw-r--r--	1	myUser	134K	Jan14	2017	CV.pdf
-rw-r--r--@	1	myUser	50K	Jan 14	2017	CV.docx
drwxr-xr-x	22	myUser	714B	Sep 4	11:40	Articles
```
