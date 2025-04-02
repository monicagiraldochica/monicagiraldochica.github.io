# 9. Manipulating text and csv files

## 9.1. Writing files

### The `echo` command

So far we have used echo to print text into the terminal. You can also use this utility to print text into a file (and create a new file if it doesn't exist):

`echo "some text" >> someFile.txt` appends `some text` in a new line of `someFile.txt` and adds the new line character t=at the end. If `someFile.txt` didn't exist, the file is created.

If you add the flag `-n`, bash won't print the trailing newline character: `echo -n "some text" >> someFile.txt`.

If you add the flag `-e`, bash will interpret the character scape sequences in the text (see table below in the `printf` section for a list of scape sequences).

If you use `>` instead of `>>`, the previous contents of the file (if it existed) will be erased and replaced with the new text that you are echoing.

### The `printf` command

printf is a powerful tool that allows you to format the information before printing it in a file, the command line or another variable. For example, you can specify the format of any number that you print and the number of decimal points you want to use. You could even use this tool to change the format of a variable (i.e. from scientific notation to float) and save the result in a new variable instead of a file. You can also add tab or any character scape sequence to your text.

Syntax: `printf <format> <arguments>`

`printf` uses the format specified in `<format>` to print the objects (strings, numbers or variables) specified in `<arguments>`. `<format>` is a string that contains alphanumerical characters, character scape sequences and format specifications, each of which causes printing of the next successive argument. In contrast with the `echo` command, `printf` does not print the text in a new line by default, in order to add a new line the following character scape sequence should be added at the end of `<format>`: `\n`.

### Format

As explained above, `format` is the first string after `printf` and indicates the type of format that should be used to print the arguments. The following table shows the strings that can be used for formatting.

One should specify one format per argument. For example, in the command `printf "%d %s" 10 "my_string"`, `"%d %s"` is the format, which indicates that the first argument after the format (`10`) should be printed as a decimal (`%d`). Then, there should be a space, and then the second argument (`my_string`) should be printed as a string (`%s`).

In the command `printf "%d %s\n" 10 "my_string"`, `%d %s\n` is the format, which indicates that the first argument after the format (`10`) should be printed as a decimal (`%d`), followed by a space. The second argument (`string`) should be printed as a string (`%s`). It also indicates that after the second argument there should be a new-line character (`\n`). `\n` is a scape sequence. The list of [scape sequences](#scape-sequences) can be found bellow. If any of the arguments is a string with special characters, spaces or [scape sequences](#scape-sequences), make sure to always surround it with quotation marks.

| Format option | Meaning |
| --- | --- |
| `%%` | Prints the symbol `%` and no argument is used. For example, `printf "%%"` just prints a `%`. |
| `%b` | Prints the corresponding argument as a string. The [scape sequences](#scape-sequences) are interpreted instead of reading them as literal strings. It will stop when the number of characters specified in the precision is reached or at the end of the string if the precision is not specified (or if the string has less characters than the precision). |
| `%c` | Prints the first character of the corresponding argument if it is a string or the first digit if it's a number. `printf "%c %c" "some string" 199` will print `s 1`. |
| `%d` | The corresponding argument is a positive or negative integer number. If no precision is specified, it just prints the number. Otherwise, adds zeros before the integer to achieve the number of digits specified in the precision. For example, `printf "%d %.5d\n" -2 2` prints `-2 00002`. |
| `%e` | Prints the corresponding argument, which should be a number, in scientific notation. There will be one digit before the decimal point and six digits after the decimal point if no precision is specified (or the number of digits specified in the precision). Infinity is printed as `inf` and NaN as `nan`. For example, 234.567 equals 2.34567 × 102 in scientific notation. So, if we use `printf "%e" 234.567`, the result will be `2.345670e+02`. |
| `%f` | Prints the corresponding argument, which should be a number, in floating-point. The number of digits after the decimal point equals the precision or six digits if no precision was specified. Infinity is printed as `inf` and NaN as `nan`. For example, `printf "%f\n" 2.34567890123` will print `2.345679` and `printf "%.3f\n" -2.34567890123` will print `-2.346`. |
| `%s` | Prints the corresponding argument as a string. The [scape sequences](#scape-sequences) are interpreted as literal strings. So, `printf "%s,%s" "text1" "text2\ttext3"` will print `text1,text2ttext3`, no tab introduced. It will stop when the number of characters specified in the precision is reached or at the end of the string if the precision is not specified (or if the string has less characters than the precision). For example, `printf "%s" "example"` will print `example`, and `printf "%.3s" "example"` will print `exa`. |

### Scape sequences

| Character scape sequence | Meaning |
| --- | --- |
| `\b` | Do not print the previous character (acts as backspace). For example, `printf "%b" "abcdef"` will print `abcdef`, while `printf "%b" "abc\bdef"` will print `abdef`. |
| `\c` | Suppresses any output after the sequence. For example, `printf "%b" "Hello\c World"` will print only `Hello`. `World` will not be printed. |
| `\n` | Write a new-line character. For example, `printf "%b" "abc\ndef"` will print `abc` in one line, and `def` in another line. |
| `\r` | Moves the cursor to the beginning of the current line. So, the following characters will replace the ones at the beginning of the line. For example, `printf "%b" "Happy World\rLala"` prints `Lalay World` because `Lala` is written at the beginning of the line and replaces `Happ`. |
| `\t` | Write a tab character |
| `\v` | Write a vertical tab. |
| `\'` | Write a single quote character. |
| `\\` | Write a backslash character. |

### Assigning result to a variable

You can save the output of printf into a variable instead of printing it. For example, if you have a number in scientific notation and you want to convert it to floating, you can type the following:

```bash
$ FLOAT=$(printf "%f" 2.345670e+02)
$ echo $FLOAT
234.567000
```

## 9.2. Reading files

### Reading a file line by line

The `cat` command, followed by the path of a file, can be used to visualize the content of the file in the command line:

```bash
$ cat subjectList.txt
AA0083277
AA0084999
AC0208933
AC0148099
AD0190300
BB0299033
BC0345100
BD0365666
CA0372599
CA0381677
CB0384399
CC0384433
DD0385444
```

If you want to read a file line by line and run a set of instructions on each line, you can combine the `cat` and `for` commands. The following example reads the content of a file line by line (which contains a list of subject IDs) and copies that information into a new file with their group membership, which can be obtained from the first two letters of the subject ID. The first two letters of each line are extracted with `${line:0:2}`.

```bash
$ for line in $(cat subjectList.txt)
> do
> echo "${line:0:2},${line}" >> subjectInfo.txt
> don
```

The content of `subjectInfo.txt` after running those lines will be:
AA,AA0083277
AA,AA0084999
AC,AC0208933
AC,AC0148099
AD,AD0190300
BB,BB0299033
BC,BC0345100
BD,BD0365666
CA,CA0372599
CA,CA0381677
CB,CB0384399
CC,CC0384433
DD,DD0385444

### Doing statistics on the numerical values of a column in a text file

This is the content of `infoFile.txt`:

| SubjectID | Group | Gender | Ethnicity | Handedness | Age | Movement |
| --- | --- | --- | --- | --- | --- | --- |
| AA0083277 | Control | M | Hispanic | R | 20 | 0.23525 |
| AA0084999 | Patient | M | Hispanic | R | 18 | 0.14564 |
| AC0208933 | Control | F | Hispanic | R | 17 | 0.18698 |
| AC0148099 | Control | M | NonHispanic | R | 21 | 0.19789 |
| AD0190300 | Patient | M | NonHispanic | R | 16 | 0.23454 |
| BB0299033 | Control | F | NonHispanic | R | 22 | 0.19752 |
| BC0345100 | Control | M | NonHispanic | R | 19 | 0.18789 |
| BD0365666 | Patient | F | NonHispanic | R | 17 | 0.14386 |
| CA0372599 | Patient | F | NonHispanic | R | 20 | 0.12384 |
| CA0381677 | Control | F | NonHispanic | L | 17 | 0.13453 |
| CB0384399 | Control | F | Hispanic | R | 18 | 0.45655 |
| CC0384433 | Control | M | NonHispanic | R | 15 | 0.13465 |
| DD0385444 | Patient | M | Hispanic | R | 16 | 0.32433 |

In this example we will calculate the minimum, maximum and average movement in the MRI scanner for the subjects in each group and gender. These values should be shown with only three decimals. There are many ways to do that, some of them a lot more efficient than the one presented here, using functions that we have not learn yet but that will be introduced further down in this document. We will use in this case the `cat` command to read from the file, the `for` loop, and some non-integer and array operations that have been learned from previous chapters.

The `for` will read in each loop one line of the text file and extract the gender, group and movement values. Depending the group and gender, it will add the movement to one of the following arrays:

CM: to save the movement of all male controls.
CF: to save the movement of all female controls.
PM: to save the movement of all male patients.
PF: to save the movement of all female patients.

In bash it is not necessary to initialize an array. Instead, you can start adding values and the first time you add a value to a non-existent array, it will be automatically initialized. When you ask bash the size of an array that hasn’t been initialized, it will return the value zero.

These are the steps to follow in order to calculate the minimum, maximum and average movement from the file:

1. Create a loop that reads each line of the file (except the first one which is just a heather with column names).
2. In each loop do the following:
    2.1. Split the line using the comma as a separator and save that in a variable called `ARRAY`.
    2.2. Obtain the subject group, which is located in the 2nd column (position 1 of the array). Remember, bash arrays start in the position 0 (not the position 1).
    2.3. Obtain the subject gender, which is located in the 3rd column (position 2 of the array).
    2.4. Obtain the subject movement, which is located in the 7th column (position 6 of the array).
    2.5. Depending on the value of the group and gender, add movement to the corresponding array:
        - If group equals "Control" and gender equals "M" (Male): Add the movement at the end of the array `CM`. If `CM` has zero values, the new item should be added to the position 0, if `CM` has one value, the new item should be added to the position 1 (because the existent item in the array will be in the position 0), and so on. So, every new item is added to the position that is equal to the current size of the array. As a reminder, the size of an array can be obtained with `${#array[@]}`.
        - If group equals "Control" and gender equals "F" (Female): Add the movement at the end of the array `CF`.
        - If group equals "Patient" and gender equals "M": Add the movement at the end of the array `PM`.
        - If group equals "Patient" and gender equals "F": Add the movement at the end of the array `PF`.
3. Sort the four arrays with the previously learned command: `IFS=$'\n' sorted=($(sort <<<"${array[*]}"))`
4. Show the minimum, maximum and average value of each array. Use `printf` instead of `echo` in order to show only three decimals per number:
    - Minimum value: will be the first value in the sorted array.
    - Maximum value: will be the last value in the sorted array (in the position `SIZE_ARRAY–1`).
    - Average value: will equal to the sum of all values divided by the size of the array. As a reminder, this is the general command used to calculate the average of an array, as shown in previous chapters: `IFS='+' avg=$(echo "scale=1;(${array[*]})/${#array[@]}"|bc)`.

Now, lets see this in actual code:

```bash
# Loop through the lines of the file
n=0
for line in $(cat infoFile.csv)
do
# Skip the first row with the heathers
if [ $((n++)) -gt 0 ]
then
    IFS=',' read -a ARRAY <<< "${line}"
    GRP=${ARRAY[1]}
    GEN=${ARRAY[2]}
    MOV=${ARRAY[6]}

    if [ "$GRP" == "Control" ] && [ "$GEN" == "M" ]
    then
        CM[${#CM[@]}]=${MOV}
    fi

    if [ "$GRP" == "Control" ] && [ "$GEN" == "F" ]
    then
        CF[${#CF[@]}]=${MOV}
    fi

    if [ "$GRP" == "Patient" ] && [ "$GEN" == "M" ] > then
        PM[${#PM[@]}]=${MOV}
    fi

    if [ "$GRP" == "Patient" ] && [ "$GEN" == "F" ]
    then
        PF[${#PF[@]}]=${MOV}
    fi
fi
done

# Get the minimum, maximum and average values of the CM array (Controls, Males)
IFS=$'\n' sortedCM=($(sort <<<"${CM[*]}"))
IFS='+' avg=$(echo "scale=4;(${CM[*]})/${#CM[@]}"|bc)
printf "Male Controls:\nMin: %.3f\nMax: %.3f\nAve: %.3f\n" ${sortedCM[0]} ${sortedCM[${#sortedCM[@]} -1]} $avg

# Get the minimum, maximum and average values of the CF array (Controls, Females)
IFS=$'\n' sortedCF=($(sort <<<"${CF[*]}"))
IFS='+' avg=$(echo "scale=4;(${CF[*]})/${#CF[@]}"|bc)
printf "Male Controls:\nMin: %.3f\nMax: %.3f\nAve: %.3f\n" ${sortedCF[0]} ${sortedCF[${#sortedCF[@]} -1]} $avg

# Get the minimum, maximum and average values of the PM array (Patients, Males)
IFS=$'\n' sortedPM=($(sort <<<"${PM[*]}"))
IFS='+' avg=$(echo "scale=4;(${PM[*]})/${#PM[@]}"|bc)
printf "Male Controls:\nMin: %.3f\nMax: %.3f\nAve: %.3f\n" ${sortedPM[0]} ${sortedPM[${#sortedPM[@]} -1]} $avg

# Get the minimum, maximum and average values of the PF array (Patients, Females)
IFS=$'\n' sortedPF=($(sort <<<"${PF[*]}"))
IFS='+' avg=$(echo "scale=4;(${PF[*]})/${#PF[@]}"|bc)
printf "Male Controls:\nMin: %.3f\nMax: %.3f\nAve: %.3f\n" ${sortedPF[0]}
```

The number of lines in the loop of the previous code could be reduced by simplifying the `if` expressions. The code below is equivalent to the one above (the loop), but written in less lines. In the chapter of [Condition testing](content6.html) I explain how to simplify `if` expressions:

```bash
n=0
for line in $(cat infoFile.csv)
do
if [ $((n++)) -gt 0 ]
then
    IFS=',' read -a ARRAY <<< "${line}"
    GRP=${ARRAY[1]}
    GEN=${ARRAY[2]}
    MOV=${ARRAY[6]}
    [ "$GRP" == "Control" ] && [ "$GEN" == "M" ] && CM[${#CM[@]}]=${MOV}
    [ "$GRP" == "Control" ] && [ "$GEN" == "F" ] && CF[${#CF[@]}]=${MOV}
    [ "$GRP" == "Patient" ] && [ "$GEN" == "M" ] && PM[${#PM[@]}]=${MOV}
    [ "$GRP" == "Patient" ] && [ "$GEN" == "F" ] && PF[${#PF[@]}]=${MOV}
fi
done
```

You could reduce even more the number of lines in the code of the loop:

```bash
n=0
for line in $(cat infoFile.csv)
do
if [ $((n++)) -gt 0 ]
then
    IFS=',' read -a ARRAY <<< "${line}"
    [ "${ARRAY[1]}" == "Control" ] && [ "${ARRAY[2]}" == "M" ] && CM[${#CM[@]}]=${ARRAY[6]}
    [ "${ARRAY[1]}" == "Control" ] && [ "${ARRAY[2]}" == "F" ] && CF[${#CF[@]}]=${ARRAY[6]}
    [ "${ARRAY[1]}" == "Patient" ] && [ "${ARRAY[2]}" == "M" ] && PM[${#PM[@]}]=${ARRAY[6]}
    [ "${ARRAY[1]}" == "Patient" ] && [ "${ARRAY[2]}" == "F" ] && PF[${#PF[@]}]=${ARRAY[6]}
fi
done
```

### Reading lines of a text file which contain spaces

In the previous example we read line by line a file using a `for` loop and the `cat` utility. This works most of the times. However, if you try to read this way a file in which one or more of the lines contain a space, bash will read each word separated by a space as a separate line.

For example, if file `test.txt` has the following content:
a b
c d
e f
g h
i j

When you try to read each line using a file, this is the result you will get:

```bash
$ for line in $(cat test.txt)
> do
> echo $((i++)) $line
> done
0 a
1 b
2 c
3 d
4 e
5 f
6 g
7 h
8 i
9 j
```

To fix this problem you have to tell bash that newline (`\n`) is the only separator. You do this by declaring the system variable `IFS=$'\n'`.

```bash
$ IFS=$'\n'
$ for line in $(cat test.txt)
> do
> echo $((i++)) $line
> done
0 a b
1 c d
2 e f
3 g h
4 i j
```

### Load the content of a file into an array and access a specific line separately

```bash
$ ARRAY=($(cat test.txt))
$ echo ${ARRAY[0]}
a b
$ echo ${ARRAY[1]}
c d
$ echo ${ARRAY[2]}
e f
$ echo ${ARRAY[3]}
g h
$ echo ${ARRAY[4]}
i j
```

## 9.3. Using the `read` command for more complex csv files

So far, we have learned that using the `for` loop and the `cat` utility you can read each line of a file and separate it into different fields using a separator. However, csv files can become very difficult to separate into fields if some of them contain a comma (the same character that is being used as a separator), a space, or both.

Example: Obtain the last field of `line` using the concepts learned before.

```bash
$ line="SUBJ20"," Age 22-30","VISIT1","1","DIAGN: Major Depressive Disorder, Single Episode, In Full Remission"
$ IFS=',' read -a ARRAY >>> "$line"
$ echo "The last fifth of line is: "${ARRAY[4]}
The fifth field of line is: DIAGN: Major Depressive Disorder
```

However, this is not the correct result. The fifth field of `line` is `"DIAGN: Major Depressive Disorder, Single Episode, In Full Remission"`. But because we are using a comma as a separator, bash is separating this field into separate columns. To solve this problem, you can read from the file descriptor and save each field in a separate variable using the `read` utility. With `read` if one of the columns contains a comma but is surrounded by quotation marks, it will read the text inside the quotation marks as a single field.

Suppose that you have a file called `example.csv` with the following content:
"SUBJ1","Age 22-30","VISIT1","DIAGN: Major Depressive Disorder, Single Episode"
"SUBJ2","Age 22-30","VISIT1","DIAGN: Bipolar, Schizophrenia"
"SUBJ3","Age 22-30","VISIT1","DIAGN: Major Depressive Disorder"
"SUBJ4","Age 22-30","VISIT1","DIAGN: Autism, Dyslexia, ADHD"
And you want to read each line of the file and save the first and last fields into a new file called `result.csv`.

You would accomplish that with the following code:

```bash
# Assign the file descriptor 3 (or any integer number) to example.csv
$ exec 3< example.csv
# Obtain the number of lines in the input file
$ N=$(cat example.csv | wc -l)
$ echo $N
4
# Iterate through all the lines of the file
$ i=0
$ while [ $((i++)) -lt $N ]
> do
> IFS=',' read -u 3 f1 f2 f3 f4 # Save each field in a different variable. Variable f1 will contain the 1st field, variable f2 the second field, etc.
> echo "$f1,$f4" >> result.csv # Write the value of the first and last fields into the output file.
> done
# You must close the file descriptor using the following command (replace number 3 by the corresponding file descriptor)
$ exec 3<&-
# Read the content of the output file
$ cat result.csv
"SUBJ1","DIAGN: Major Depressive Disorder, Single Episode"
"SUBJ2","DIAGN: Bipolar, Schizophrenia"
"SUBJ3","DIAGN: Major Depressive Disorder"
"SUBJ4","DIAGN: Autism, Dyslexia, ADHD"
```

## 9.4. Reading comma-separated CSV files using the read utility but no file descriptor

In the following example we are going to read the same csv file from above called `example.csv`. The file has four columns. We are going to use the `while` loop to iterate through each line of the file and save the fields in variables `f1`, `f2`, `f3`, `f4`. Before starting to iterate, we have to tell bash that comma will be the separator in each line with `IFS=','`.

```bash
$ IFS=','
$ i=1
$ while read f1 f2 f3 f4
> do
> echo "Line $((i++)):"
> echo "Field 1: $f1"
> echo "Field 2: $f2"
> echo "Field 3: $f3"
> echo "Field 4: $f4"
> done < example.csv
Line 1:
Field 1: "SUBJ1"
Field 2: "Age 22-30"
Field 3: "VISIT1"
Field 4: "DIAGN: Major Depressive Disorder, Single Episode"
Line 2:
Field 1: "SUBJ2"
Field 2: "Age 22-30"
Field 3: "VISIT1"
Field 4: "DIAGN: Bipolar, Schizophrenia"
Line 3:
Field 1: "SUBJ3"
Field 2: "Age 22-30"
Field 3: "VISIT1"
Field 4: "DIAGN: Major Depressive Disorder"
Line 4:
Field 1: "SUBJ4"
Field 2: "Age 22-30"
Field 3: "VISIT1"
Field 4: "DIAGN: Autism, Dyslexia, ADHD"
```

## 9.5. The `awk` and `grep` commands

### `awk`

`awk` is a bash program that scans files and process their content using patterns. It reads each line of a file or a group of files searching for the specified pattern and each time that it finds the pattern, performs an associated action. This tool can extract specific lines or columns from files, merge files, search the content of one file in the other, etc.

When reading each line of the specified files, `awk` will separate it into fields (columns) using the blank space as a separator. If your file uses a different separator (i.e. a comma), you must specify your separator using the `-F` flag (see syntax below). The different fields will be denoted `$1`, `$2`, `$3`... etc. `$0` will refer to the entire line. If the field separator (`FS`) is null, each line will be split into one field per character.

See the examples section for a better understanding of this command.

Syntax: `awk [ -F fs ] [ -v var=value ] [ 'pattern {action}' ] [ files ] | [ other functions ]`

| Command Section | Meaning |
| --- | --- |
| `-F fs` (optional) | Defines the input field separator to be the regular expression `fs`. Use this flag when the columns of your file use a separator other than a space. |
| `-v var=variableName` (optional) | When the value that is being search is stored in a variable, you should use this flag. See below for examples on how to use this flag. |
| `files` | List of files to be searched. |
| other functions (optional) | You can apply to the output of `awk` other functions such as `head`, `tail`, `paste`, `grep`, etc. |

### `grep`

`grep` searches a given pattern or text in a file or list of files. `grep` is is able to find simple patterns and basic regular expressions, `egrep` can perform search of extended regular expressions. `fgrep` is quicker than both tools but can only handle fixed patterns. `zgrep`, `zegrep`, and `zfgrep` act like `grep`, `egrep`, and `fgrep`, respectively, but accept compressed files as input.

See the examples section for a better understanding of this command.

Syntax: `grep [flag] [pattern] [file(s)]`

| `grep` Format | Meaning |
| --- | --- |
| `grep my_string files` | Search the list of files for lines that *contain* `my_string`. |
| `grep '^my_expression' files` | Search for any lines that *start with* `my_expression` in the list of files. If `my_expression` contains a back slash, the special meaning of the next special character is turned off. If expression contains a dot that is not preceded by a black slash, it will match a single character of any value in the position of the dot. `grep '^string' file.txt` will search for any lines in `file.txt` that start with string. |
| `grep 'my_expression$' files` | Search for any lines that *end with* `my_expression` in the list of files. If `my_expression` contains a back slash, the special meaning of the next special character is turned off. If expression contains a dot that is not preceded by a black slash, it will match a single character of any value in the position of the dot. `grep 'string$' file.txt` matches any lines in `file.txt` that end with `string`. `grep '^string$' file.txt` matches any lines in `file.txt` that start and end with `string`. |
| `grep '[characters]' files` | Search for any lines that *contain any of the characters* enclosed between the brackets. Use a hyphen for a range of values. `grep '[abcde]' file.txt` matches any lines in `file.txt` that contain `a`, `b`, `c`, `d` or `e`. `grep '[Ss]tring' file.txt` matches any lines in `file.txt` that contain the words string or String. `grep 'B[ai][dt]' file.txt` matches any lines in `file.txt` that contain the words `Bad`, `Bat`, `Bid` or `Bit` (the second character can be `a` or `i` and the third character `d` or `t`). `grep '[0-9][0-9]' file.txt` matches any lines in `file.txt` that contain a pair of numeric digits. `grep '[a-zA-Z]' file.txt` matches any lines in `file.txt` with at least one letter. `grep '^$' file.txt` matches any empty lines. |
| `grep '[^characters]' files` | Search for any lines that *don't contain* any of the characters enclosed between he brackets. Use a hyphen for a range of values. `grep '[^a-zA-Z0-9]' file.txt` matches any lines in `file.txt` that don't contain any letter or number (any lines that contain only special characters). |
| `grep 'character*' files` | The character preceding the asterisk is optional when matching lines. `grep '"*smug"*' file.txt` matches any lines in `file.txt` that contain `smug` or `"smug"` (with or without the quotes that precede the asterisks). |
| `grep 'my_expression\{n\}' files` | Match exactly `n` occurrences of `my_expression`. `grep '[0-9]\{3\}-[0-9]\{4\}' file.txt` matches any lines in `file.txt` that contain three digits, followed by a line and four digits. |
| `grep 'expression \{n,\}' files` | Match `n` or more occurrences of expression. `grep '[0-9]\{3,\}' file.txt` matches any lines in `file.txt` that contain three or more digits. |

| Flag | Meaning |
| --- | --- |
| `-A num` | Print `num` lines of trailing context after each match. |
| `-B num` | Print `num` lines of leading context before each match.  |
| `-C num` | Print `num` lines of leading and trailing context surrounding each match. If `num` is not specified, `num=2`. |
| `-c` | Print the number of matched lines per file instead of the actual lines. |
| `--color=when` | Mark up the matching text with the expression stored in the `GREP_COLOR` environment variable. The possible values of `when` can be: `never`, `always` or `auto`. |
| `-d action` | Specify the demanded action for directories. The possible values of action are: `read` (default), which means that the directories are read in the same manner as normal files; `skip` to silently ignore the directories, and `recourse` to read them recursively, which has the same effect as the `-R` and `-r` option. |
| `-e pattern` | To search for more than one pattern/expression, add the flag `-e` in front of each pattern/expression. |
| `--exclude` | If specified, it excludes files matching the given filename pattern from the search. Note that `--exclude` patterns take priority over `--include` patterns. Patterns are matched to the full path specified, not only to the filename component. |
| `--exclude-dir filename_pattern` | If `-R` is specified, it excludes directories matching the given `filename_pattern` from the search. |
| `-f file` | Read one or more newline separated patterns from `file`. Empty pattern lines match every input line. Newlines are not considered part of a pattern. If `file` is empty, nothing is matched. |
| `-h` | Omit the filename headers with output lines. |
| `--help` | Print a brief help message. |
| `--include` | If specified, only files matching the given filename pattern are searched. Note that `--exclude` patterns take priority over `--include` patterns. Patterns are matched to the full path specified, not only to the filename component. |
| `--include-dir filename_pattern` | If `-R` is specified, only directories matching the given `filename_pattern` are searched. Note that `--exclude-dir` patterns take priority over `--include-dir` patterns. |
| `-L` | Only the names of files not containing selected lines are listed. |
| `-l` | Only the names of files containing selected lines are listed. |
| `-m num` | Stop reading the file after num matches. |
| `-n` | Each output line is preceded by its relative line number in the file, starting at line 1. The line number counter is reset for each file processed. This option is ignored if `-c`, `-L`, `-l`, or `-q` is specified. |
| `--null` | Prints a zero-byte after the file name. |
| `-O` | If `-R` is specified, follow symbolic links only if they were explicitly listed on the command line. The default is not to follow symbolic links. |
| `-o` | Prints only the matching part of the lines. |
| `-q` | Suppress normal output. |
| `-R` or `-r` | Recursively search subdirectories listed. |
| `-S` | If `-R` is specified, all symbolic links are followed. The default is not to follow symbolic links. |
| `-s` | Suppress error messages from nonexistent or unreadable files. |
| `-V` | Display version information and exit. |
| `-v` | Selected lines are those not matching any of the specified patterns. |
| `-w` | The expression is searched for as a whole word. |
| `-x` | Show only the cases where the whole line equals the expression. |
| `-Z` or `-z` | Accepts compressed input files. |
| `--line-buffered` | Force output to be line buffered. By default, output is line buffered when standard output is a terminal and block buffered otherwise. |

### Examples of `awk` and `grep`

The following examples will show how to read and manipulate files using different command line tools. Each example will read one or more of the following files. `file1.csv` and `file3.csv` use comma as the separator between columns. On the other hand, `file2.txt` and file `file4.txt` use a space as the separator between columns.

Content of `file1.csv`:
"Anonymized ID","Subject Group","HASCONDITION","CONDITION"
"B33199522","Group1","0",""
"B33199603","Group3","0",""
"B11137879","Group1","0",""
"B11144410","Group2 b","0",""
"B11110455","Group2 b","0",""
"B11135291","Group3","0",""
"B11153927","Group1","0",""
"B11177579","Group2 b","0",""
"B11177806","Group1","MD",""
"B11157958","Group3","0",""
"B11110690","Group3","0",""
"B11152799","Group1","0",""
"B11154358","Group1","0",""
"B11110925","Group1","0",""
"B11135291","Group3","9","mTBI"
"B11135072","MISSING","0",""
"B33199603","Group3","0",""
"B11137879","Group1","0",""
"B11110603","Group1","0",""
"B11110927","Group1","0",""
"B11147712","Group1","0",""
"B33191224","Group2 b","0",""
"B11131290","Group2 b","0",""
"B11157974","Group1","0",""
"B33191224","Group2 b","0",""
"B11141503","Group3","0",""
"C11137159","Group3","9","mTBI"
"B33199522","Group1","0",""

Content of `file2.txt`:
"AnonymizedID" "SubjectGroup" "TEST1" "TEST2"
"B11130912" "Group2b" "900" "MissingData"
"B11137244" "Group1" "450" "555"
"B11154534" "Group1" "456" "456"
"B11144100" "Group1" "450" "886"
"B11137244" "Group1" "450" "456"
"B12226566" "Group2b" "450" "MissingData"
"B11134987" "Group1" "900" "MissingData"
"B11144345" "Group1" "900" "776"
"C11137159" "Group3" "MissingData" "MissingData"
"B11156453" "Group4" "456" "2"
"B11110676" "Group1" "900" "10"
"C11138929" "Group2b" "2" "MissingData"
"B11154532" "Group1" "456" "886"
"B11155267" "Group3" "900" "10"
"B11137120" "Group2b" "450" "456"
"B33191224" "Group2b" "450" "776"
"B11155267" "Group3" "900" "10"
"C11138999" "Group2b" "900" "MissingData"
"B11131605" "Group1" "456" "MissingData"
"B11137784" "Group1" "900" "436"
"B11156098" "Group1" "500" "886"
"B11133232" "Group1" "500" "MissingData"
"B11135292" "Group3" "MissingData" "MissingData"
"C11138912" "Group2b" "900" "MissingData"
"B11150911" "Group2b" "900" "117"
"B11152577" "Group1" "900" "756"
"B11156098" "Group1" "456" "886"
"B11133232" "Group1" "456" "MissingData"

Content of `file3.csv`:
Anonymized ID,Subject Group,AGE
C11138122,MISSING,21
C11138192,Group1,54
B12226507,Group1,68
B12226546,Group1,67
C11138122,Group1,24
C11138184,Group1,59
C11138797,Group1,22
C11138152,Group1,53
C11138150,Group1,41
C11137167,Group3,14
C11137159,Group3,13
C11137167,Group3,16
C11137159,Group3,13
C11131039,Group2 b,67
C11135566,Group2 b,73
B11119903,Group2 b,83
C11137544,Group1,21
C11137443,Group3,11
C11137123,Group2 b,69
C11137439,Group3,79
C11137439,Group3,15
C11133100,Group1,23
D11144030,Group3,13
B11108399,Group1,23
B11108326,Group1,59
B11119909,Group2 b,61
B11110893,Group1,28

Content of `file4.txt`:
AnonymizedID SubjectGroup AGE
B11108326 Group1 59
B11108399 Group1 23
B11110893 Group1 28
B11119909 Group2 61
D11144030 Group3 11
D11144030 Group3 13
B11119903 Group2 84
C11131039 Group2 67
C11133100 Group1 23
C11135566 Group2 72
C11137159 Group3 11
C11137159 Group3 12
C11137167 Group3 14
C11137167 Group3 16
C11137439 Group3 15
C11137439 Group3 79
C11137443 Group3 15
C11137544 Group1 22
C11137123 Group2 68
C11138150 Group1 44
C11138152 Group1 10
C11138797 Group1 24
C11138184 Group1 57
C11138122 Group1 23
C11138122 MISSING 25
C11138192 Group1 45
B12226507 Group1 26
B12226546 Group1 55

#### Examples: Reading specific columns from a file or a list of files

##### Example 1: Print the first column of each file.

In order to print the first column of these files, we will use `awk` utility. As it was shown before, the command for this utility consists on some optional flags followed by an action statement, and then the list of files. In this case, the action statement is `'{print $1}'`, because we want to print only the first column (`$1`). Files `file2.txt` and `file4.txt` use a space as a column separator (which is the separator for default). So, to access the first column of these files we don't need the `-F` flag. However, files `file1.csv` and `file3.csv` use a comma as a separator. So, in order for `awk` to distinguish the different columns, we have to use the `-F` flag. In this case, the parameter of the `-F` flag is a comma (`-F','`). If you are trying to read a file which has the columns separated by a different character, then use that character instead of `','`.

Space-separated files:

```bash
$ awk '{print $1}' file2.txt
"AnonymizedID"
"B11130912"
"B11137244"
"B11154534"
"B11144100"
"B11137244"
"B12226566"
"B11134987"
"B11144345"
"C11137159"
"B11156453"
"B11110676"
"C11138929"
"B11154532"
"B11155267"
"B11137120"
"B33191224"
"B11155267"
"C11138999"
"B11131605"
"B11137784"
"B11156098"
"B11133232"
"B11135292"
"C11138912"
"B11150911"
"B11152577"
"B11156098"
"B11133232"

$ awk '{print $1}' file4.txt
AnonymizedID
B11108326
B11110893
B11119909
D11144030
D11144030
B11119903
C11131039
C11133100
C11135566
C11137159
C11137159
C11137167
C11137167
C11137439
C11137439
C11137443
C11137544
C11137123
C11138150
C11138152
C11138797
C11138184
C11138122
C11138122
C11138192
B12226507
B12226546
```

Comma-separated files:

```bash
$ awk -F',' '{print $1}' file1.csv
"Anonymized ID"
"B33199522"
"B33199603"
"B11137879"
"B11144410"
"B11110455"
"B11135291"
"B11153927"
"B11177579"
"B11177806"
"B11157958"
"B11110690"
"B11152799"
"B11154358"
"B11110925"
"B11135291"
"B11135072"
"B33199603"
"B11137879"
"B11131605"
"B11110927"
"B11147712"
"B33191224"
"B11131290"
"B11157974"
"B33191224"
"B11141503"
"C11137159"
"B33199522"

$ awk -F',' '{print $1}' file3.csv
AnonymizedID
C11138122
C11138192
B12226507
B12226546
C11138122
C11138184
C11138797
C11138152
C11138150
C11137167
C11137159
C11137167
C11137159
C11131039
C11135566
B11119903
C11137544
C11137443
C11137123
C11137439
C11137439
C11133100
D11144030
B11108399
B11108326
B11119909
B11110893
```

To precede each line by the line number, add `NR` after `print` in the `awk` command to indicate that you want to print the Number Row before the column 1 (`$1`):

Space-separated files:

```bash
$ awk '{print NR,$1}' file2.txt
1 "AnonymizedID"
2 "B11130912"
3 "B11137244"
4 "B11154534"
5 "B11144100"
6 "B11137244"
7 "B12226566"
8 "B11134987"
9 "B11144345"
10 "C11137159"
11 "B11156453"
12 "B11110676"
13 "C11138929"
14 "B11154532"
15 "B11155267"
16 "B11137120"
17 "B33191224"
18 "B11155267"
19 "C11138999"
20 "B11131605"
21 "B11137784"
22 "B11156098"
23 "B11133232"
24 "B11135292"
25 "C11138912"
26 "B11150911"
27 "B11152577"
28 "B11156098"
29 "B11133232"

$ awk '{print NR, $1}' file4.txt
1 AnonymizedID
2 B11108326
3 B11110893
4 B11119909
5 D11144030
6 D11144030
7 B11119903
8 C11131039
9 C11133100
10 C11135566
11 C11137159
12 C11137159
13 C11137167
14 C11137167
15 C11137439
16 C11137439
17 C11137443
18 C11137544
19 C11137123
20 C11138150
21 C11138152
22 C11138797
23 C11138184
24 C11138122
25 C11138122
26 C11138192
27 B12226507
28 B12226546
```

Comma-separated files:
```bash
$ awk -F',' '{print NR, $1}' file1.csv
1 "Anonymized ID"
2 "B33199522"
3 "B33199603"
4 "B11137879"
5 "B11144410"
6 "B11110455"
7 "B11135291"
8 "B11153927"
9 "B11177579"
10 "B11177806"
11 "B11157958"
12 "B11110690"
13 "B11152799"
14 "B11154358"
15 "B11110925"
16 "B11135291"
17 "B11135072"
18 "B33199603"
19 "B11137879"
20 "B11131605"
21 "B11110927"
22 "B11147712"
23 "B33191224"
24 "B11131290"
25 "B11157974"
26 "B33191224"
27 "B11141503"
28 "C11137159"
29 "B33199522"

$ awk -F',' '{print NR, $1}' file3.csv
1 Anonymized ID
2 C11138122
3 C11138192
4 B12226507
5 B12226546
6 C11138122
7 C11138184
8 C11138797
9 C11138152
10 C11138150
11 C11137167
12 C11137159
13 C11137167
14 C11137159
15 C11131039
16 C11135566
17 B11119903
18 C11137544
19 C11137443
20 C11137123
21 C11137439
22 C11137439
23 C11133100
24 D11144030
25 B11108399
26 B11108326
27 B11119909
28 B11110893
```

##### Example 2: Print the first column of `file1.csv` and `file2.txt` in reverse order

In order to print from the last line to the first line, you can use the command `tail` with the flag `-r` (for reverse) after the command `awk`. The command line will first execute the `awk` command, which is written before the `|` symbol, and then it will run the `tail` command, which inverts the order of the previous output. Remember that for `file1.csv` you need to use `-F','` to indicate that the columns are separated by commas and not spaces.

Space-separated file:

```bash
$ awk '{print $1}' file2.txt | tail -r
"B11133232"
"B11156098"
"B11152577"
"B11150911"
"C11138912"
"B11135292"
"B11133232"
"B11156098"
"B11137784"
"B11131605"
"C11138999"
"B11155267"
"B33191224"
"B11137120"
"B11155267"
"B11154532"
"C11138929"
"B11110676"
"B11156453"
"C11137159"
"B11144345"
"B11134987"
"B12226566"
"B11137244"
"B11144100"
"B11154534"
"B11137244"
"B11130912"
"AnonymizedID"
```

Comma-separated file:

```bash
$ awk -F',' '{print $1}' file1.csv | tail -r
"B33199522"
"C11137159"
"B11141503"
"B33191224"
"B11157974"
"B11131290"
"B33191224"
"B11147712"
"B11110927"
"B11110603"
"B11137879"
"B33199603"
"B11135072"
"B11135291"
"B11110925"
"B11154358"
"B11152799"
"B11110690"
"B11157958"
"B11177806"
"B11177579"
"B11153927"
"B11135291"
"B11110455"
"B11144410"
"B11137879"
"B33199603"
"B33199522"
"Anonymized ID"
```

The same as in example 1, to precede each line by the line number, add `NR` after `print` in the `awk` command to indicate that you want to print the Number Row before the column 1 (`$1`):

Space-separated file:

```bash
$ awk '{print NR, $1}' file2.txt | tail -r
29 "B11133232"
28 "B11156098"
27 "B11152577"
26 "B11150911"
25 "C11138912"
24 "B11135292"
23 "B11133232"
22 "B11156098"
21 "B11137784"
20 "B11131605"
19 "C11138999"
18 "B11155267"
17 "B33191224"
16 "B11137120"
15 "B11155267"
14 "B11154532"
13 "C11138929"
12 "B11110676"
11 "B11156453"
10 "C11137159"
9 "B11144345"
8 "B11134987"
7 "B12226566"
6 "B11137244"
5 "B11144100"
4 "B11154534"
3 "B11137244"
2 "B11130912"
1 "AnonymizedID"
```

Comma-separated file:

```bash
$ awk -F',' '{print NR, $1}' file1.csv | tail -r
29 "B33199522"
28 "C11137159"
27 "B11141503"
26 "B33191224"
25 "B11157974"
24 "B11131290"
23 "B33191224"
22 "B11147712"
21 "B11110927"
20 "B11110603"
19 "B11137879"
18 "B33199603"
17 "B11135072"
16 "B11135291"
15 "B11110925"
14 "B11154358"
13 "B11152799"
12 "B11110690"
11 "B11157958"
10 "B11177806"
9 "B11177579"
8 "B11153927"
7 "B11135291"
6 "B11110455"
5 "B11144410"
4 "B11137879"
3 "B33199603"
2 "B33199522"
1 "Anonymized ID"
```

##### Example 3: Print the second and third columns of `file2.txt`

In the previous examples we used the action statement `'{print $1}'` to print the first column. Since we now want to print the second and third columns instead of the first one, we replace `$1` by `$2,$3`. If you wanted to print columns 4 and 5 instead, you would simply use `$4,$5`, etc.

```bash
$ awk '{print $2,$3}' file2.txt
"SubjectGroup" "TEST1"
"Group2b" "900"
"Group1" "450"
"Group1" "456"
"Group1" "450"
"Group1" "450"
"Group2b" "450"
"Group1" "900"
"Group1" "900"
"Group3" "MissingData"
"Group4" "456"
"Group1" "900"
"Group2b" "2"
"Group1" "456"
"Group3" "900"
"Group2b" "450"
"Group2b" "450"
"Group3" "900"
"Group2b" "900"
"Group1" "456"
"Group1" "900"
"Group1" "500"
"Group1" "500"
"Group3" "MissingData"
"Group2b" "900"
"Group2b" "900"
"Group1" "900"
"Group1" "456"
"Group1" "456"
```

To precede each line by the line number, add `NR` after `print` in the `awk` command to indicate that you want to print the Number Row before the column 1 (`$1`):

```bash
$ awk '{print NR,$2,$3}' file2.txt
1 "SubjectGroup" "TEST1"
2 "Group2b" "900"
3 "Group1" "450"
4 "Group1" "456"
5 "Group1" "450"
6 "Group1" "450"
7 "Group2b" "450"
8 "Group1" "900"
9 "Group1" "900"
10 "Group3" "MissingData"
11 "Group4" "456"
12 "Group1" "900"
13 "Group2b" "2"
14 "Group1" "456"
15 "Group3" "900"
16 "Group2b" "450"
17 "Group2b" "450"
18 "Group3" "900"
19 "Group2b" "900"
20 "Group1" "456"
21 "Group1" "900"
22 "Group1" "500"
23 "Group1" "500"
24 "Group3" "MissingData"
25 "Group2b" "900"
26 "Group2b" "900"
27 "Group1" "900"
28 "Group1" "456"
29 "Group1" "456"
```

##### Example 4: Print the second and third columns of `file1.csv` in reverse orfer

In order to print the output in reverse order for `file1.csv`, use the `tail -r` command after the `awk`.

```bash
$ awk -F',' '{print $2,$3}' file1.csv | tail -r
"Group1" "0"
"Group3" "9"
"Group3" "0"
"Group2 b" "0"
"Group1" "0"
"Group2 b" "0"
"Group2 b" "0"
"Group1" "0"
"Group1" "0"
"Group1" "0"
"Group1" "0"
"Group3" "0"
"MISSING" "0"
"Group3" "9"
"Group1" "0"
"Group1" "0"
"Group1" "0"
"Group3" "0"
"Group3" "0"
"Group1" "MD"
"Group2 b" "0"
"Group1" "0"
"Group3" "0"
"Group2 b" "0"
"Group2 b" "0"
"Group1" "0"
"Group3" "0"
"Group1" "0"
"Subject Group" "HASCONDITION"
```

To precede each line by the line number, add `NR` after `print` in the `awk` command to indicate that you want to print the Number Row before the column 1 (`$1`):

```bash
$ awk -F',' '{print NR,$2,$3}' file1.csv | tail -r
29 "Group1" "0"
28 "Group3" "9"
27 "Group3" "0"
26 "Group2 b" "0"
25 "Group1" "0"
24 "Group2 b" "0"
23 "Group2 b" "0"
22 "Group1" "0"
21 "Group1" "0"
20 "Group1" "0"
19 "Group1" "0"
18 "Group3" "0"
17 "MISSING" "0"
16 "Group3" "9"
15 "Group1" "0"
14 "Group1" "0"
13 "Group1" "0"
12 "Group3" "0"
11 "Group3" "0"
10 "Group1" "MD"
9 "Group2 b" "0"
8 "Group1" "0"
7 "Group3" "0"
6 "Group2 b" "0"
5 "Group2 b" "0"
4 "Group1" "0"
3 "Group3" "0"
2 "Group1" "0"
1 "Subject Group" "HASCONDITION"
```

##### Example 5: Print all the columns of `file1.csv` showing the lines in reverse order.

To print all the columns of a file using `awk`, use `$0` (instead of a column number). Or use the command `cat`.

Using `awk`: `awk -F',' '{print $0}' file1.csv | tail -r`
Using `cat`: `cat file1.csv | tail -r`

##### Example 6: Print all the columns of `file1.csv` in reversed order (first the third column, then the second and finally the first one), and save the re-ordered columns in a new file called file1_reordered.csv

If you were going to print the columns one to three in normal order, you would use `'{print $1,$2,$3}'`. To print them in reverse order, you just reverse the order of the columns in print, like so: `'{print $3,$2,$1}'`.

To save the output to a file instead of showing it in the terminal, use `>>` file as explained in previous sections.
Remember to use the `-F','` flag to indicate that the columns are separated by commas, and not the default space.

```bash
awk -F',' '{print $3,$2,$1}' file1.csv >> file1_reordered.csv
```

##### Example 7: Print the columns and lines of `file1.csv` in reverse order

Use the same command as before, adding `| tail -r` at the end to invert also the lines.

```bash
awk -F',' '{print $3,$2,$1}' file1.csv | tail -r
```

##### Example 8: Read the second column of `file1.csv` and save it into an array

When saving a column of a file into an array, you must specify that the elements of the array are separated by new lines (`'\n'`). You do this using the command `IFS=$'\n'`.

The elements of the array will be saved in the variable `ARRAY`. As it was learned in previous chapters, to access the individual elements of `ARRAY` you use the syntax `${ARRAY[index]}`. With `index` starting at 0. So, to access the first element the command is `echo ${ARRAY[0]}`. To access the second element it is `echo ${ARRAY[1]}`, etc. Type `echo ${ARRAY[@]}` to view all the elements of the array.

Remember, the system variable `IFS` contains the separator that is being used to separate each feld within the lines of a file. You can change the value of this variable at any time by using `IFS='character'`, where `character` is the one separating the fields.

```bash
$ IFS=$'\n'
$ ARRAY=($(awk -F',' '{print $2}' file1.csv))
$ echo ${ARRAY[0]}
"Subject Group"
$ echo ${ARRAY[1]}
"Group1"
$ echo ${ARRAY[@]}
"Subject Group" "Group1" "Group3" "Group1" "Group2 b" "Group2 b" "Group3" "Group1" "Group2 b" "Group1" "Group3" "Group3" "Group1" "Group1" "Group1" "Group3" "MISSING" "Group3" "Group1" "Group1" "Group1" "Group1" "Group2 b" "Group2 b" "Group1" "Group2 b" "Group3" "Group3" "Group1"
$ echo ${#ARRAY[@]}
29
```

##### Example 9: Print the first column of `file2.txt` followed by the first column of `file4.txt`

To print a specific column for more than one file, you use the same command, adding the list of files you want to print after the first one. However, all the files in the list must use the same column separator (in this case it's a space). Since the column separator for this list of files is a space (the default), you don't need to use the `-F` flag.

```bash
awk '{print $1}' file2.txt file4.txt
```

##### Example 10: Print the first column of `file3.csv` followed by the first column of `file1.csv`.

Since the column separator for this list of files is a comma, you need to use the `-F','` flag.

```bash
awk -F',' '{print $1}' file3.csv file1.csv
```
