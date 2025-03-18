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
