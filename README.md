# BASH
* Shell is just a program that processes the commands and outputs the result
* Terminal is a program that allows us to interact with different kind of shells
* POSIX is a standard to ensure that different operating systems adhere for interoperability and compatibility, POSIX complient operating systems will have a similar kind of shell
* A shell script is just a series of commands put along in a clever way to achieve some automation
* A shell script is non interactive way to interact with terminal unless we prompt the user to give an input

> [!NOTE]
> * the `PS1` variable for shell scripts is empty string

* There are many kind of shells and `bash` is one of them and it is the default too

## CONVENTIONS
* Unwritten rules that govern behaviour in social settings
* Following conventions gives a sense of belonging and distinguished people as members of a group or team

> [!TIP]
> ### VARIABLES
> * All variables should be in lower case and use underscore as word seperator
> * All constants should be in upper case and use underscore as word seperator and it should be marked as readonly so that its value can't be changed `readonly VAR_NAME=VAL`
> * All variables that could be used by another script outside the local script, we must export it as `export VAR_NAME`

> [!TIP]
> ### FUNCTIONS
> * Always written in lower case and use underscore as word seperator
> * We use parenthesis without space as soon as function name ends, then a space then open a curly brace to define function body
> * Statements inside the functions should be indented as with respect with the function
> * The function closing curly brace always comes in a new line and indented at the same level as the function name
> ```
> function_name() {
>   Statements
> }
> ```

> [!IMPORTANT]
> ### EXPANDING VARIABLES
> * We can use variable expanding to access a variables value `echo ${VARIABLE_NAME}`
> * This is useful in following case, first line 2 gives empty output as `heightcm` is not a variable that is defined
> ```
> height="190"
> 
> echo $heightcm
> 
> echo #{height}cm
> ```
> * This can be used in command line arguments or special shell variables
> * Using double quotes around variable expantion like below will avoid variable from splitting into different elements, specially in case of arrays
> ```
> #!/bin/bash
>
> # THESE TWO LOOPS PRODUCE DIFFERENT OUTPUTS
>
> string="one two three"
>
> for element in ${string}; do
>   echo ${element}
> done
>
> for element in "${string}"; do
>   echo ${element}
> done
> ```
> * This is because that there is a special environment variable that defaults to (tab or space or line break) any word contained in a string variable seperated by these will get split and will be considered as different elements as parsed by shell interpreter

> [!TIP]
> * For directory paths, filenames, URLs better use double quote expanding, and when in doubt use double quote expanding

## REFRESHER
### SCRIPTFLOW
> [!TIP]
> When we need to run multiple scripts in sequence, what will happen if one fails, others will fail too, so we must export a success status variable from the script that an be used by the next script to make decisions based on previous script's completion or success status

> [!TIP]
> Use `\[\[ CONDITION \]\]` in conditional statements because it offers wider range of conditional expressions

* `seq` can be used in for loops as it generates a sequence of numbers
```
for number in $(seq N1 N2); do
  echo ${number}
done
```

* Call another script in our script and also have the variables defined by that scripts in out file but we must also check if that file exists or not
```bash
readonly SCRIPT_PATH="PATH_TO_SCRIPT"

if [[ -f $(SCRIPT_PATH) ]]; then
  source PATH_TO_SCRIPT
else
  Statements
fi
```
