# BASH
* Shell is just a program that processes the commands and outputs the result
* Terminal is a program that allows us to interact with different kind of shells
* POSIX is a standard to ensure that different operating systems adhere for interoperability and compatibility, POSIX compliant operating systems will have a similar kind of shell
* A shell script is just a series of commands put along in a clever way to achieve some automation
* A shell script is non interactive way to interact with terminal unless we prompt the user to give an input

> [!NOTE]
> * The `PS1` variable for shell scripts is empty string

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
> ```bash
> function_name() {
>   Statements
> }
> ```

> [!IMPORTANT]
> ### EXPANDING VARIABLES
> * We can use variable expanding to access a variables value `echo ${VARIABLE_NAME}`
> * This is useful in following case, first line 2 gives empty output as `heightcm` is not a variable that is defined
> ```bash
> height="190"
> 
> echo $heightcm
> 
> echo #{height}cm
> ```
> * This can be used in command line arguments or special shell variables
> * Using double quotes around variable expantion like below will avoid variable from splitting into different elements, specially in case of arrays
> ```bash
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
> * This is because that there is a special environment variable `IFS` that defaults to (tab or space or line break) any word contained in a string variable seperated by these will get split and will be considered as different elements as parsed by shell interpreter

```bash
# PRINT SUBSTRING
echo "${VAR:START:END}"
```
```bash
# LENGTH OF STRING
echo "${#VAR}"
```

### PARAMETER EXPANTION
* `#` for removing prefix and `%` for removing suffixes
* `echo "${VAR#PREFIX_TO_REMOVE%SUFFIX_TO_REMOVE}`, if prefix and suffix do not mat from starting and ending, they do not get removed
* We also have a pattern called longest prefix removal `##` and longest suffix removal `%%` which removes the pattern's first occurance as prefix or postfix, mainly used in paths
* While removing file extension, we can use any of the `%` or `%%`
* `echo "${VAR##PREFIX_TO_REMOVE%%SUFFIX_TO_REMOVE}`
Examples
```bash
# SUBSTRING REPLACEMENT # PARAMETER EXPANTION
echo "${VAR:WORD_SUBSTRING_TO_BE_REPLACED:REPLACING_WORD_SUBSTRING}"
```
```
# REMOVE ALL `/` AND FOLLOWING TEXT EXCEPT THE FILE_NAME, HERE WE ARE USING REGEX
echo "${VAR##*/}"
```
```
# REMOVE LAST AND FIRST WORD, USING REGEX OR GLOB
echo "${VAR#* % *}"
```

### GLOBS
* Used in variables and commands too
* `*`, `?`, `\`, `[` and `]` can be used to build regex in parameter expansion
    - `?` matches any single character in the string
    - `*` matches string of 0 or more number of characters of any type, wildcard usage
    - `\` escape character
    - `[]` allows to create a character class
```bash
# MATCH WORDS HAVING "ail" AT THE END AND ARE HAVING LENGTH 4
echo "${VAR%?ail}"

# GET DIRECTORIES OF SIMILAR PATTERN
ls ?ail

# GET ALL FILES of PATTERN tesxx.txt WHERE xx CAN BE ANYTHING
ls tes??.txt

# GET ALL ITEMS IN DIRECTORY HAVING LENTH OF 4
ls ????

# GET LIST OF ALL ITEMS STARTING WITH "report."
ls report.*

# GET LIST OF ALL PDF ITEMS
ls *.pdf

# LIST FILES OF LENGTH 4 ENDING WITH "ail"
touch ?ail

# GET FILES HAVING A EXTENSION STARTING WITH "hu"
ls hu*.?*

# GET FILES STARTING WITH "file_" AND ENDING WITH 3 CHARACTER EXTENSION
ls file_*.???

# GET ALL FILES ENDING WITH 4 CHARACTER EXTENSION
ls *.????

# GET ALL FILES HAVING "*" IN BETWEEN
ls *\**.*

# GET ALL FILES HAVING A, B, C AT THE END AND STARTING WITH "fi"
ls fi[ABC]

# GET ALL FILES NOT HAVING A, B, C AT THE END AND STARTING WITH "fi"
ls fi[^A-C]

# REMOVVE ALL FILES STARTING WITH "fi" AND ENDING WITH 1, 2, 3, 4
rm fi[1-4]

# SOME MORE EXAMPLES
rm abc[A-E][0-7]
```

### BRACE EXPANTION
* Return a set of strings generated by a pattern and use it to create elements
EXAMPLES
```bash
# MAKE FILES THAT HAVE a, b, c, d AT END AND "abc" AT STARTING
touch abc{a..d}

OTHER EXAMPLES
touch huhu{qwe,asd,zxc}{001..004}
touch huhu{1..10..2} # WITH STEPS
```

> [!TIP]
> * For directory paths, filenames, URLs better use double quote expanding, and when in doubt use double quote expanding

## REFRESHER
### SCRIPTFLOW
> [!TIP]
> When we need to run multiple scripts in sequence, what will happen if one fails, others will fail too, so we must export a success status variable from the script that an be used by the next script to make decisions based on previous script's completion or success status

> [!TIP]
> Use `[[ CONDITION ]]` in conditional statements because it offers wider range of conditional expressions

* `seq` can be used in for loops as it generates a sequence of numbers
```bash
for number in $(seq N1 N2); do
  echo ${number}
done
```

> [!TIP]
> * Call another script in our script and also have the variables defined by that scripts in out file but we must also check if that file exists or not
> ```bash
> readonly SCRIPT_PATH="PATH_TO_SCRIPT"
>
> if [[ -f $(SCRIPT_PATH) ]]; then
>   source PATH_TO_SCRIPT
> else
>   Statements
> fi
> ```

> [!TIP]
> * We should add `exit 0` at the end of script to tell the interpreter and console that it completed
> * But when error occurs, we should exit the script using `exit 0`

### FUNCTIONS
* Use in generous amount
* There are local variables that can't be accessed outside the function
```bash
function_name() {
  local VARIABLE_NAME=VAL
}
```
> [!TIP]
> `basename` command is used to extract the filename or last component of a path for a specified element we pass as an argument
> `basename qwe/asd/zxc.qaz` outputs zxc

### COMMAND LINE ARGUMENTS
* If we pass `N` command line arguments, we can access them by `$1`, `$2`, ... `$N` or like `${N}`
* The number of command line arguments that we can pass is limite by variable `ARG_MAX`, we can see it using `getconf ARG_MAX`
* The `shift` statement shifts the index of arguments by one, the example below prints first 3 arguments
```bash
echo ${1}
shift
echo ${1}
shift
echo ${1}
```

### PID
* Script runs as a process and launches child processes when it runs the statemets
* `strace -Tfp PID` see all system calls made by the process
* `pgrep -o bash` to get PID of current bash session
* `$$` holds PID of parent shell

### BUILT-IN COMMAND
* Builtin commands do not generate new PIDs
* Use `type COMMAND` to see its type
* Use `time COMMAND` to see time taken by the command to execute
* `/bin/COMMAND` takes less time to execute than `/usr/bin/COMMAND`
* `time` is not a command but a keyword
* `compgen -b` to see builtin commands
* `compgen -k` to see keywords

### BUILT-IN vs KEYWORDS
* Builtin never generate seperate PID, keywords are special words to control flow of execution
* `[ ]` or `test` in conditionals are builtin and `[[ ]]` are keywords, see `man [` or `man test`
* `[[ ]]` are not posix compliant and do not have backward compatibility

> [!TIP]
> ### GUARD CLAUSE
> * Avoid nesting of `if` statements

### SHEBANG
* Tells which program to use for executing itself, if not specified, it uses the parent shell's program
* See the shell `echo $SHELL`
* `#!/bin/bash`

## STREAMS
* Flow of data between processes

> [!TIP]
> ### CHECK IF THE IMPOT WAS FROM KEYBOARD OR A PIPE
> ```
> if [[ -t 0 ]]; then
>   echo "Keyboard"
> else
>   echo "Pipe or file "
> fi
> ```
> ```
> echo "HUHU" | test -t 1 && echo "From Pipe"
> ```
> * Trace sys calls for input and output redirection
> ```
> sudo strace -Tfp $$ 2>&1 | grep -E 'read|write' &
> ```

### HEREDOC and HERESTRINGS
* Multiline text input
* `EOF` is a token and can be any string
```bash
cat <<  EOF
...
...
...
EOF
```
```bash
cat > FILE_PATH << EOF
...
...
...
EOF
```
* `COMMAND <<< "HUHU"`

EXAMPLE
```bash
usage() {
  cat << USAGE
This
Is
Docs
USAGE
}

if [[ $# -ne 1 ]]; then
  usage
  exit 1
fi
```

### PIPEFAIL
* To make a script return a exit status of error when a command in the script fails add `set -o pipefail` below the shebang
* To prevent the output files from being overwritten add `set -o noclobber` below the shebang

### EXIT CODE
* Must use `exit "${code}"` to exit the script
* This should be used in if conditions to get conditional flow of if we see an error we return like this
```bash
export CONF_FILE="FILE_PATH"

terminate() {
  echo "${1}" >&2
  exit ${code}
}

if [[ ! -f "${CONF_FILE}" ]]; then
  terminate "Can't find file ${CONF_FILE}"
else
  cat "${CONF_FILE}"
fi
exit 0
```

> [!CAUTION]
> ### XARGS
> Do it !
> ### EVAL
> Too common, used to run a script provided as string `VAR=VAL; eval "${VAR}"`
> ### PRINTF
> * `echo` alternatve with advanced features

## SUBSHELLS and COMMAND SUBSTITUTION and SUBPROCESS
* Running a complex command and capturing output in a variable, this runs as a subshell
* Subshell is a shell instance running under another shell instance
* This also supports command chaining
```bash
VAR=$(COMPLEX_CHAIN_OF_COMMANDS)
```
EXAMPLES
```bash
curr_env="huhu"
a=$(echo  ${curr_env})
```
```bash
curr_env="huhu"
(
sleep 1
echo ${curr_env}
)
```

## COMMAND CHAINING
* `COMMAND && COMMAND && COMMAND ...` next command executes only if the command before it executes successfully, useful when we need series of commands to be done perfectly
* `COMMAND || COMMAND || COMMAND ...` next command executes only if the command before it fails to execute, useful in error resolving methods

## SPECIAL VARIABLES
* Allow access to current state of shell and command line arguments passed
* `$?` stores info for last excuted command, which is exit status, generally used in interactive shell, rare in script
* `$0` stores name of script being run
* `$#` number of arguments passed, generally used inside script
* `$*` string of all arguments being passed, generally used inside script and functions `VAR=$*`
* `$@` array of all values passed as argument, generally used inside script and functions `VAR=$@`
* `$$` Current process ID, can be used anywhere
* `$!` ID of background job, generally inside script but can be used anywhere, stores info of last program sent to background
* `$-` contains flags in use by the script, generally inside script but can be used anywhere
* `$_` contains the last argument of previous command

> [!TIP]
> * Creating own exit codes and in reproducable manner, like this
> ```bash
> terminate() {
>   local msg="${1}"
>   local code="${2:-160}" # SETTING DEFAULT VALUE OF ERR CODE IS 160
>   echo "Error: ${msg}" >&2
>   exit "${code}"
> }
>
> # CALL TERMINATION
> terminate "Err Msg" "${EXIT_STATUS}"
> terminate "Err Msg" "69"
> ```

### $0
* Reading the file and modifying it with time
* Get the path of the script and then read it
```bash
# ABS_PATH
readonly SCRIPT_PATH=${0}
# readonly SCRIPT_NAME=${0##*/}
cat ${SCRIPT_PATH}
```

> [!IMPORTANT]
> * See `IFS` environment variable, it is used for default seperators for and values inside the script and interactive shell too, changing it for specific purposes is possible and set it like `IFS=",.:"` or something else inside the script, its default value is ` \t\n`
> * `set -- ${VAR}` var is a string of elements seperated by elements and this command assigns the elemets to command line argument variables and then we can access them in the way `echo $1 $2 $3`

## ARRAYS
### DECLARE
* `declare` keyword is used to define data structures, for help `declare --help`

| EXPRESSION | EFFECT |
| ---------- | ------ |
| `declare -r VAR=VAL` | create readonly variable |
| `declare -u VAR=STRING` | Have string in upper case |
| `declare -l VAR=STRING` | Have string in lower case |

> [!TIP]
> See `typeset`

> [!TIP]
> A string can be treated as numbers.
> This is done using dynamically typed syntax which allows us to reassign different type of values to a single variable
> ```
> VAR="NUMBER"
> echo $$((VAR+1))
> ```

### ARRAY
* Index start from 0

> [!TIP]
> While declaring an array, the sepereators between the elements is one of the characters which is present if `IFS` variable

```bash
# DECLARE ARRAY
VAR=(VAL1 VAL2 VAL3 ...)
declare -a VAR=(VAL1 VAL2 VAL3 ...)

# CHANGE VALUE
VAR[N]=NEW_VALUE

# ACCESS ELEMENT
echo ${VAR[N]}

# ACCESS / REERENCE WHOLE ARRAY
echo ${VAR[@]}

# LOOP ACCESS, BUT ELEMENT GET SPLIT
for ELEMENT in ${VAR[@]}; do
  echo "${ELEMENT}"
done

# LOOP ACCESS, BUT ELEMENT DO NOT GET SPLIT
for ELEMENT in "${VAR[@]}"; do
  echo "${ELEMENT}"
done

# INSERT ELEMENT, IF ARRAY IS FROM [0, N-1]
VAR[N]=VALUE
VAR[${#VAR[@]}]=VALUE

# GET ARRAY LEN USING PARAMETER EXPANTION WITH VARIABLE SUBSTITUTION
a=${#VAR[@]}

# INSERT IN BETWEEN AT INDEX N USING PARAMETER EXPANTION AND VARIABLE SUBSTITUTION
VAR=("${VAR[@]:0:N}" VALUE "${VAR[@]:N}")

# REMOVE ARRAY ELEMENT
# This just the value at the inedx to "" but the index stays there and shows no value
unset VAR[N]

# EMPTY ENTIRE ARRAY
unset VAR[@]

# APPEND TO ARRAY
VAR+=(VAL1 VAL2 VAL3 ...)
```

> [!TIP]
> - See sorting an array
> - We can have nested arrays

### ASSOCIATIVE ARRAY
* Works like hashmap
* Access values using keys
```bash
# DECLARE
declare -A VAR=( [KEY1]=VALUE1 [KEY2]=VALUE2 ... )

# ACCESS ELEMENT
echo ${VAR[KEY]}

# ADD / UPDATE KEY VALUE PAIR
VAR[KEY]=VALUE

# REMOVE KEY VALUE PAIR
unset VAR[KEY]

# REMOVE ALL VAY VALUE PAIRS
unset VAR

# GET ALL KEYS
echo ${!VAR[@]}

# ACCESS ALL KEY VALUE PAIRS
for key in "${!VAR[@]}"; do
  echo "${key} : ${VAR[${key}]}"
done
```

## GOOD PRACTICES
* Always start scripts with shebang `#!`
### STRICT MODE
* Even after occurence or error, bash script keeps running, this should not happen in some cases
* We will set flags to handle these abnormal events, these flags are set just below the shebang in general
    - `set -e` exit on error, as soon as script encounters error, see status using `echo $?` after the script exits, there are many reserved exit codes, see bash documentation for exit codes, can also be used in interactive shell, but the shell will exit as soon as a wrong command is entered
    - `set -u` exit if unset variable is used, it also exits with a error code
    - `set -o pipefail` catches error in piped commands which are used in the scripts, but always exits with status 0, thats why there is need to use the use the `OR` Command chanining
* Initial way to handle errors in small scripts, but this do not scale well for large scripts
```
ehco "HUHU" || {echo "Failed, error encountered"; exit 1}
```

### NO OP COMMAND
* Testing is important, where only logic is checked but no changes are made
* This is done by dry run
* Instead of using a empty echo command use a colon `:` to test the code for logical errors and syntactical errors
* Always keep in mind: interpreter error vs runtime error

> [!TIP]
> Always docs and comments just below the shebang and it must have small description, usage, error codes and author name

## LOGGING
* Crucial when working on new or complex things
* Better to use the `ISO 8601` format which is `YYYY-MM-DDTHH:MM:SS:SSSZ`
```bash
log() {
  echo $(date -u +"%y-%m-%d_%H:%M:%S~") "${@}"
}
```
