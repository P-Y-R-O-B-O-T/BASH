> [!TIP]
> ## SET PERMISSION SECURELY
> * For file permissions
> ```bash
> find some_directory -type f -print0 | xargs -0 chmod 0644
> ```
> * For directory permissions
> ```bash
> find some_directory -type d -print0 | xargs -0 chmod 0755
> ```

> [!TIP]
> ## SAVING AND GROUPING OUTPUT FROM SEVERAL COMMANDS
> ```bash
> (COMPLEX_COMMAND_CHAIN) > FILE
> ```

> [!IMPORTANT]
> ## TRAPPING
> * When required to take action based on the type of trap captured by the script, add the following to the script
> ```bash
> trap "COMMAND" TRAP # THE COMMAND IN HERE CAN BE COMPLEX_COMMAND_CHAIN OR FUNCTION CALL OR SOME OTHER COMMAND
> ```
> * Traps can be listed using `trap -l`
>
> EXAMPLE
> ```bash
> #!/usr/bin/env bash
> # cookbook filename: hard_to_kill
>
> function trapped {
>   if [ "$1" = "USR1" ]; then
>     echo "Got me with a $1 trap!"
>     exit
>   else
>     echo "Received $1 trap--neener, neener"
>   fi
> }
>
> trap "trapped ABRT" ABRT
> trap "trapped EXIT" EXIT
> trap "trapped HUP" HUP
> trap "trapped INT" INT
> trap "trapped KILL" KILL
> trap "trapped QUIT" QUIT
> trap "trapped TERM" TERM
> trap "trapped USR1" USR1
>
> while (( 1 )); do
>   : # : is a NOOP
> done
> ```

> [!IMPORTANT]
> ## GET INPUT FROM ANOTHER MACHINE
> * When there is a need to chack the state of other machine or get input from it, ssh with command subatitution is the best option
> * For this approach, make sure that public keys should be setup to make it more convenient
> ```bash
> #!/usr/bin/env bash
> # cookbook filename: command_substitution
>
> REMOTE_HOST='host.example.com'
> REMOTE_FILE='/etc/passwd'
> SSH_USER='user@'
>
> #SSH_ID='-i ~/.ssh/foo.id'
> SSH_ID='' # FOR PRIVATE KEY
>
> result=$( ssh $SSH_ID $SSH_USER$REMOTE_HOST "[ -r $REMOTE_FILE ] && echo 1 || echo 0" ) || { echo "SSH command failed!" >&2; exit 1; }
>
> if [ $result = 1 ]; then
>   echo "$REMOTE_FILE present on $REMOTE_HOST"
> else
>   echo "$REMOTE_FILE not present on $REMOTE_HOST"
> fi
> ```

> [!CAUTION]
> ## SUBSHELL CAN'T EXPORT VARIABLES TO PAREST SHELL
> * Do not try to do this, instead structure the scripts such that this is not required but if it is so important to do so, redirect value of variable to a file and then read that file in parent shell

> [!CAUTION]
> ## BUILTIN SHELL VARIABLES
> * See Bash Cookbook

> [!TIP]
> ## DEBUGGING
> * Set xtrace on in the script using `set -x`
> * Also use `export PS4='+xtrace $LINENO:'` in terminal while debugging

> [!TIP]
> ## READ LINE BY LINE
> ```bash
> while read VARIABLE; do
>   echo "HU"
> done << $(COMPLEX_SIMPLE_COMMAND_CHAIN) # THIS CAN ALSO BE A FILE INPUT LIKE < FILE
> ```

> [!TIP]
> ## ARGUMENT LIST TOO LONG
> `find /path/with/many/many/files/ -name '*e*' -print0 | xargs -0 proggy`

> [!TIP]
> ## SWAP STDOUT AND STDERR
> * `./myscript 3>&1 1>stdout.logfile 2>&3- | tee -a stderr.logfile`

> [!TIP]
> ## KEEP YOUR DATA IN THE SCRIPT
> ```bash
> grep $1 <<EOF
> mike x.123
> joe x.234
> sue x.555
> pete x.818
> sara x.822
> bill x.919
> EOF
> ```

## PARSING TEXT
```bash
#!/usr/bin/env bash
# cookbook filename: parseViaRead
#
# parse ls -l with a read statement
# an example of output from ls -l follows:
# -rw-r--r-- 1 albing users 126 2006-10-10 22:50 fnsize

ls -l "$1" | { read PERMS LCOUNT OWNER GROUP SIZE CRDATE CRTIME FILE ;
               echo $FILE has $LCOUNT 'link(s)' and is $SIZE bytes long. ;
             }
```

> [!WARNING]
> ## PASSWORDS IN PROCESS LIST
> * While running commands, never pass passwors directly into the command because it is visible when we do a `ps`
> * Password use should be avoided in scripts and see if running `sudo` is possible with `NOPASSWD`


> [!IMPORTANT]
> ## RESTRICT SSH COMMANDS
> * Edit `~./.ssh/authorized_keys` file
> ## DISCONNECT INACTIVE USERS (ROOT)
> * Set `$TMOUT` in `~/.bashrc` as `readonly TMOUT=100` so that users from the shell are not able to change the value
> ## TERMINAL SANE
> * After aborting SSH session and can't see anything meaningful just type `stty` and hit enter

> [!TIP]
> ## RUN ALL SCRIPTS IN A DIRECTORY
> ```bash
> for SCRIPT in /path/to/scripts/dir/*; do
>   if [ -f "$SCRIPT" -a -x "$SCRIPT" ]; then
>     $SCRIPT
>   fi
> done
> ```

> [!TIP]
> ## EMBED DOCS IN SCRIPT
> ```bash
> : << END_OF_DOCS
> ...
> ...
> ...
> END_OF_DOCS
> ```

> [!IMPORTANT]
> ## READABLITY
> ``` bash
> [ -n "$results" ] \
> && echo "Got a good result in $results" \
> || echo 'Got an empty result, something is wrong'
> ```

> [!IMPORTANT]
> ## BRANCH MANY WAYS USING CASE
> ```bash
> case $FN in
>   *.gif) gif2png $FN
>     ;;
>   *.png) pngOK $FN
>     ;;
>   *.jpg) jpg2gif $FN
>     ;;
>   *.tif | *.TIFF) tif2jpg $FN
>     ;;
>   *) printf "File not supported: %s" $FN
>     ;;
> esac
> ```
> ```bash
> # use other endings for case
> case $FN in
>   *.gif) gif2png $FN
>     ;;& # keep looking
>   *.png) pngOK $FN
>     ;;& # keep looking
>   *.jpg) jpg2gif $FN
>     ;;& # keep looking
>   *.tif) tif2jpg $FN
>     ;& # fall through
>   *.* ) echo "two.words"
>     ;;
>   * ) echo "oneword"
> esac
> ```

> [!TIP]
> ## FORMAT LONG PARAGRAPHS FOR READIBILITY
> ```bash
> fmt MANGLED_TEXT
> ```

> [!IMPORTANT]
> ## OVERRIDING A COMMAND
> ```bash
> function cd () {
>   if [[ $1 = "..." ]]; then
>     builtin cd ../..
>   else
>     builtin cd "$1"
>   fi
> }
> ```

> [!IMPORTANT]
> ## PARSING OUTPUT IN ARRAY
> ```bash
> #!/usr/bin/env bash
> # cookbook filename: parseViaArray
> #
> # find the file size
> # use an array to parse the ls -l output into words
> LSL=$(ls -ld $1)
> declare -a MYRA
> MYRA=($LSL)
> echo the file $1 is ${MYRA[4]} bytes.
> ```

## PARSING OUTPUT DATA OF A BUILTIN COMMAND
```bash
#!/usr/bin/env bash
# cookbook filename: parseViaFunc
#
# parse ls -l via function call
# an example of output from ls -l follows:
# -rw-r--r-- 1 albing users 126 Jun 10 22:50 fnsize

function lsparts () {
  PERMS=$1
  LCOUNT=$2
  OWNER=$3
  GROUP=$4
  SIZE=$5
  CRMONTH=$6
  CRDAY=$7
  CRTIME=$8
  FILE=$9
}

lsparts $(ls -l "$1")
echo $FILE has $LCOUNT 'link(s)' and is $SIZE bytes long.
```

## PARSING WITH READ STATEMENT
```bash
#!/usr/bin/env bash
# cookbook filename: parseViaRead
#
# parse ls -l with a read statement
# an example of output from ls -l follows:
# -rw-r--r-- 1 albing users 126 2006-10-10 22:50 fnsize

ls -l "$1" | { read PERMS LCOUNT OWNER GROUP SIZE CRDATE CRTIME FILE ;
               echo $FILE has $LCOUNT 'link(s)' and is $SIZE bytes long. ;
             }
```

> [!TIP]
> ## PARSING READ INTO ARRAY
> ```bash
> read -a MYRAY
> ```
