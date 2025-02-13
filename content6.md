# 6. Condition-testing

Variables can be used to test if a certain condition is true or false, and therefore be able to take a different course of action depending on the result of the test. For example, you might want to evaluate if a file exists to decide if you can copy it into a different folder or not. For condition-testing you will use the `if` command. This command has the following syntax:

if [ CONDITION_TO_EVALUATE ]
then
    INSTRUCTIONS THAT WILL RUN IF THE CONDITION IS TRUE
elif [ OTHER_CONDITION_TO_EVALUATE_IF_1ST_CONDITION_IS_FALSE ]
then
    INSTRUCTIONS THAT WILL RUN IF FIRST CONDITION IS FALSE BUT SECOND IS TRUE
else
    INSTRUCTIONS THAT WILL RUN IF ALL THE PREVIOUS CONDITIONS ARE FALSE
fi

The CONDITION_TO_EVALUATE is an expression that follows a specific syntax depending on what you want to test (checking files, string comparison, comparing numbers, or combining different expressions). Lets look at the different syntaxes used in each of these situations and at some examples that will help you understand this seemingly confusing subject.

## 6.1. Condition-testing to check files

In the following table, FILE refers to the path of the file or to the variable that contains the path of the file. The spaces after [ and before ] are very important. If those spaces are missing, bash will give an error.

| Condition | Meaning |
|-----------| ------- |
| `[ -a FILE ]` | Tests if `FILE` exists. |
| `[ -d FILE ]` | Tests if `FILE` exists and is a directory. |
| `[ -e FILE ]` | Tests if `FILE` exists. |
| `[ -f FILE ]` | Tests if `FILE` exists and is a regular file. |
| `[ -g FILE ]` | Tests if `FILE` exists and its `SGID` bit is set. |
| `[ -h FILE ]` | Tests if `FILE` exists and is a symbolic link. |
| `[ -k FILE ]` | Tests if `FILE` exists and its sticky bit is set. |
| `[ -p FILE ]` | Tests if `FILE` exists and is a named pipe (`FIFO`). |
| `[ -r FILE ]` | Tests if `FILE` exists and is readable. |
| `[ -s FILE ]` | Tests if `FILE` exists and has a size greater than zero. |
| `[ -t FD ]` | Tests if file descriptor `FD` is open and refers to a terminal. |
| `[ -u FILE ]` | Tests if `FILE` exists and its `SUID` (set user ID) bit is set. |
| `[ -w FILE ]` | Tests if `FILE` exists and is writable. |
| `[ -x FILE ]` | Tests if `FILE` exists and is executable. |
| `[ -O FILE ]` | Tests if `FILE` exists and is owned by the effective user ID. |
| `[ -G FILE ]` | Tests if `FILE` exists and is owned by the effective group ID. |
| `[ -L FILE ]` | Tests if `FILE` exists and is a symbolic link. |
| `[ -N FILE ]` | Tests if `FILE` exists and has been modified since it was last read. |
| `[ -S FILE ]` | Tests if `FILE` exists and is a socket. |
| `[ FILE1 -nt FILE2 ]` | Tests if `FILE1` has been changed more recently than `FILE2`, or if `FILE1` exists and `FILE2` does not. |
| `[ FILE1 -ot FILE2 ]` | Tests if `FILE1` is older than `FILE2`, or is `FILE2` exists and `FILE1` does not. |
| `[ FILE1 -ef FILE2 ]` | Tests if `FILE1` and `FILE2` refer to the same device and inode numbers. |

### Usage of `[ -a FILE ]`

```bash
$ FILE=/Users/MyUser/Desktop/someFile.txt
$ if [ -a "${FILE}" ]
> then
> echo "${FILE} exists"
> else
> echo "${FILE} doesn't exist"
> fi
```

The following example shows a common mistake. Here, the quotation marks surrounding the file path are missing. Since the path has spaces, bash gives an error:

```bash
$ if [ -a /Users/MyUser/Desktop/some file name with spaces.txt ]
> then
> echo "The file exists"
> else
> echo "The file doesn't exist"
> fi
-bash: [: too many argument
```
