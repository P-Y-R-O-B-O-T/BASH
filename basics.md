## BASH
* Sequence of statements
* Before executing, provide execution permissions using `chmod`
* While creating executables, do not add `.sh` extension
* To make it accessable to all paths, copy the executable to `/bin/` and `/usr/bin/`

### VARIABLES
* Case sensitive
* `VAR=VAL`, `VAL` can be integer, string, array, associative array
* Define read only variables `readonly VAR=VAL`

EXAMPLES
```bash
#!/bin/bash

dir_name="huhu"
mkdir ${dir_name}

echo "Directory ${dir_name} created"
```

### COMMAND LINE ARGUMENTS
* If we pass `N` command line arguments, we can access them by `$1`, `$2`, ... `$N` or like `${N}`
* The number of command line arguments that we can pass is limite by variable `ARG_MAX`, we can see it using `getconf ARG_MAX`
```bash
#!/bin/bash

# ACCESS ARGUMENT 1
echo ${1}

# GET MAXIMUM ARGS THAT CAN BE PASSED
# $(...) IS CALLED SUBSHELL
max_args=$(getconf ARG_MAX)
echo max_args
```

### READ INPUT
```bash
#!/bin/bash

# READ INPUT
read user_decision

# READ INPUT WITH PROMPT
read -p "Enter decision" user_decision

# READ N CHARACTERS
read -n 2 -p "Enter decision" user_decision
```

### ARITHMETIC OPERATIONS
* In interactive shell prompt
```
$ expr 6 + 3
$ expr 6 - 3
$ expr 6 / 3
$ expr 6 \* 3

$ A=6
$ B=3

$ expr $A + $B
$ expr $A - $B
$ expr $A / $B
$ expr $A \* $B
```
* In script
```
sol=$(( A + B ))
sol=$(( A-B ))
sol=$((A/B))
sol=$(( A * B ))
sol=$(( ++A ))
sol=$(( --A ))
sol=$(( A++ ))
sol=$(( A-- ))
```
> [!TIP]
> Precision computing, above statements give only integer answers, sometimes we need precision
> `echo $A / $B | bc -l`

## CONDITIONAL LOGIC
```bash
if [[ CONDITION ]] then
  STATEMENTS
elif [[ CONDITION ]] then
  STATEMENTS
.
.
.
else
  STATEMENTS
fi
```
### STRING OPERATORS
| EXPRESSION | EFFECT |
| ---------- | ------ |
| `[[ "${STRING_VAR1}" = "${STRING_VAR2}" ]]` | True if strings are equal |
| `[[ "${STRING_VAR1}" != "${STRING_VAR2}" ]]` | True if string are not equal |
| `[[ "${STRING_VAR}" = BLOB_EXPRESSION ]]` | True is string matches blob expression |
| `[[ "${STRING_VAR1}" > "${STRING_VAR2}" ]]` | String 1 is lexicographially large |
| `[[ "${STRING_VAR1}" < "${STRING_VAR2}" ]]` | String 1 is lexicographially small |

### MATHEMATICAL COMPARISONS
| EXPRESSION | EFFECT |
| ---------- | ------ |
| `[[ ${NUM1} -eq ${NUM2} ]]` | True if numbers are equal |
| `[[ ${NUM1} -ne ${NUM2} ]]` | True if numbers are not equal |
| `[[ ${NUM1} -gt ${NUM2} ]]` | If first is greater than second | 
| `[[ ${NUM1} -lt ${NUM2} ]]` | If first is smaller thatn second |

### FILE OPERATORS
| EXPRESSION | EFFECT |
| ---------- | ------ |
| `[[ -e FILE ]]` | If file exists |
| `[[ -d FILE ]]` | If file exists and is a directory |
| `[[ -s FILE ]]` | If file exists and is not empty |
| `[[ -x FILE ]]` | If file is executable |
| `[[ -w FILE ]]` | If file is writable |
| `[[ -z ${VAR} ]]` | If variable is empty |

### LOGICAL OPERATORS
| EXPRESSION | EFFECT |
| ---------- | ------ |
| `[[ CONDITION && CONDITION ]]` | And operation |
| `[[ CONDITION \|\| CONDITION ]]` | Or operation |
| `[[ (CONDITION && CONDITION) \|\| (CONDITION \|\| CONDITION) ]]` | Conditional chaining |

EXAMPLES
```bash
CONF_FILE="/PATH/TO/CONFIG/FILE"
rand_number=$RANDOM

if [[ ${1} = "${rand_number}" ]] then
  echo "You guessed correct number"
elif [[ $((${1})) -lt 1  ]] then
  echo "Number cant be less than 1"
else
  echo "Wrong guess"
fi
```
```bash
[[ A -gt 4 && A –lt 10 ]]
```
```bash
[[ A -gt 4 || A –lt 10 ]]
```

### FOR LOOPS
```bash
for ELEMENT in ITERABLE; do
  STATEMENTS
done
```

EXAMPLES
```bash
for mission_id in 1 2 3 4 5 6; do
  touch "mission_${mission_id}"
done
```
```bash
for file_id in {1..100}; do
  rm "secret_file_${file_id}"
done
```
```bash
for (( pet_number=0; pet_number <= 5; pet_number++ )); do
  echo "Pet ${pet_number} is here"
done
```

> [!TIP]
> Get Server Uptime
> ```bash
> for server in ${cat servers.txt} do
>   uptime=$(ssh server "uptime")
>   if [[ $? = 0 ]]; then
>     echo "Server ${server} is up since ${uptime}"
> ```

> [!TIP]
> Install packages
> ```bash
> for package in $(cat install-packages.txt) do
>   sudo apt-get –y install $package
> done
> ```

### WHILE LOOPS
```bash
while [[ CONDITION ]]; do
  STATEMENTS
done
```

EXAMPLES
```bash
FILE_PATH="/home/slenderman/Documents/KUSHAL_RESUME.pdf"
while [[ ! -f ${FILE_PATH} ]]; do
  STATEMENTS
done
```

> [!IMPORTANT]
> ### BREAK and CONTINUE
> `break` keyword is for breaking a loop.
> `continue` keyword is to skip statements and goto next iteration of the loop

### SWITCH CASE
```bash
case ${VARIABLE} in
  PATTERN_OR_VALUE_1)
    STATEMENTS
    ;;
  PATTERN_OR_VALUE_2)
    STATEMENTS
    ;;
.
.
.
  PATTERN_OR_VALUE_N)
    STATEMENTS
    ;;
  *) # THE DEFAULT CASE
    STATEMENTS
    ;;
esac
```

> [!TIP]
> Find files matching different patterns
> ```bash
> DIRECTORY_PATH="~/Downloads/"
> DUMP_PATH="~/DUMP"
> for file in $(ls ${DIRECTORY_PATH}); do
>   case ${file} in
>     *.*.*)
>       mv ${DIRECTORY_PATH}${file} ${DUMP_PATH}${file}
>       ;;
>     *.pdf)
>       mv ${DIRECTORY_PATH}${file} ~/Documents/${file}
>       ;;
>     *)
>       echo "Unable to decide action for ${file}"
>   esac
> done
> ```

## FUNCTIONS
```bash
function_name() {
  STATEMENTS
}
```

> [!TIP]
> * To access arguments accessed to function, use `${N}` inside the function
> * To get number of arguments passed to function, use `$#` inside the function
> * To get array of all arguments passed to function, use `$@` inside function
> * To get string of all arguments passed to function as a single object, use `$*` inside the function
> * Define local variables inside function `local VAR=VAL`

> [!TIP]
> We can also return values from functions.
> The value we echo is returned
> ```
> function_name() {
>   echo "HUHU"
> }
>
> returned_value=$(function_name)
> ```

## EXIT CODES
* Every command have a exit code that it returns

> [!TIP]
> Always terminate a script with `exit 0` as last statement

* `0` means that there was no error, any other value than `0` means error
```bash
ERR_ENCOUNTERED=0

clean_up_mess() {
  # ADD STATE MENTS TO REVERT THE MESS CREATED BY RUNNING THIS SCRIPT IF FAIL TO COMPLETE NORMALLY
}

terminate() {
  clean_up_mess
  echo "$1" >&2
  exit ${2}
}

if [[ ERR_ENCOUNTERED = 0 ]]; then
  terminate "Error occured" 1
fi

exit 0
```
