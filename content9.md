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
