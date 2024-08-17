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
>
> ## PARSING OUTPUT DATA OF A BUILTIN COMMAND
> ```bash
> #!/usr/bin/env bash
> # cookbook filename: parseViaFunc
> #
> # parse ls -l via function call
> # an example of output from ls -l follows:
> # -rw-r--r-- 1 albing users 126 Jun 10 22:50 fnsize
>
> function lsparts () {
>   PERMS=$1
>   LCOUNT=$2
>   OWNER=$3
>   GROUP=$4
>   SIZE=$5
>   CRMONTH=$6
>   CRDAY=$7
>   CRTIME=$8
>   FILE=$9
> }
>
> lsparts $(ls -l "$1")
> echo $FILE has $LCOUNT 'link(s)' and is $SIZE bytes long.
> ```
>
> ## PARSING WITH READ STATEMENT
> ```bash
> #!/usr/bin/env bash
> # cookbook filename: parseViaRead
> #
> # parse ls -l with a read statement
> # an example of output from ls -l follows:
> # -rw-r--r-- 1 albing users 126 2006-10-10 22:50 fnsize
>
> ls -l "$1" | { read PERMS LCOUNT OWNER GROUP SIZE CRDATE CRTIME FILE ;
>                echo $FILE has $LCOUNT 'link(s)' and is $SIZE bytes long. ;
>              }
> ```

> [!TIP]
> ## PARSING READ INTO ARRAY
> ```bash
> read -a MYRAY
> ```

> [!IMPORTANT]
> ## SECURE SCRIPT TEMPLATE
> ```bash
> #!/usr/bin/env bash
> # cookbook filename: security_template
> # Set a sane/secure path
> PATH='/usr/local/bin:/bin:/usr/bin'
> # It's almost certainly already marked for export, but make sure
> \export PATH
> # Clear all aliases. Important: leading \ inhibits alias expansion.
> \unalias -a
> # Clear the command path hash
> hash -r
> # Set the hard limit to 0 to turn off core dumps
> ulimit -H -c 0 --
> # Set a sane/secure IFS (note this is bash & ksh93 syntax only--not portable!)
> IFS=$' \t\n'
>
> # Set a sane/secure umask variable and use it
> # Note this does not affect files already redirected on the command line
> # 022 results in 0755 perms, 077 results in 0700 perms, etc.
> UMASK=022
> umask $UMASK
>
> until [ -n "$temp_dir" -a ! -d "$temp_dir" ]; do
>   temp_dir="/tmp/meaningful_prefix.${RANDOM}${RANDOM}${RANDOM}"
> done
> mkdir -p -m 0700 $temp_dir || (echo "FATAL: Failed to create temp dir '$temp_dir': $?"; exit 100)
> # Do our best to clean up temp files no matter what
> # Note $temp_dir must be set before this, and must not change!
> cleanup="rm -rf $temp_dir"
> trap "$cleanup" ABRT EXIT HUP INT QUIT
> ```
>
> ## FIND WORLD WRITABLE DIRECTORIES
> ```bash
> #!/usr/bin/env bash
> # cookbook filename: chkpath.1
> # Check your $PATH for world-writable or missing directories
> exit_code=0
>
> for dir in ${PATH//:/ }; do
>   [ -L "$dir" ] && printf "%b" "symlink, "
>   if [ ! -d "$dir" ]; then
>     printf "%b" "missing\t\t"
>     (( exit_code++ ))
>   elif [ -n "$(ls -lLd $dir | grep '^d.......w. ')" ]; then
>     printf "%b" "world writable\t"
>     (( exit_code++ ))
>   else
>     printf "%b" "ok\t\t"
>   fi
>   printf "%b" "$dir\n"
> done
>
> exit $exit_code
> ```
>
> ## SECURE TEMPORARY DIRECTORY
> ```bash
> # cookbook filename: make_temp
>
> # Make sure $TMP is set to something
> [ -n "$TMP" ] || TMP='/tmp'
> # Make a "good enough" random temp directory
> until [ -n "$temp_dir" -a ! -d "$temp_dir" ]; do
>   temp_dir="/$TMP/meaningful_prefix.${RANDOM}${RANDOM}${RANDOM}"
> done
>
> mkdir -p -m 0700 $temp_dir
>   || { echo "FATAL: Failed to create temp dir '$temp_dir': $?"; exit 100 }
>
> # Make a "good enough" random temp file
> until [ -n "$temp_file" -a ! -e "$temp_file" ]; do
>   temp_file="/$TMP/meaningful_prefix.${RANDOM}${RANDOM}${RANDOM}"
> done
>
> touch $temp_file && chmod 0600 $temp_file
>   || { echo "FATAL: Failed to create temp file '$temp_file': $?"; exit 101 }
> ```
>
> ## CLEANUP
> ```bash
> # cookbook filename: clean_temp
> # Do our best to clean up temp files no matter what
> # Note $temp_dir must be set before this, and must not change!
> cleanup="rm -rf $temp_dir"
> trap "$cleanup" ABRT EXIT HUP INT QUIT
> ```

> [!CAUTION]
> ## SETUID and SETGID
> * Avoid using for shell scripts as may create more problems
>
> ## RESTRICT GUEST USERS
> * There are mant users in the others catetory, they must be restricted

> [!TIP]
> ## REDIRECT SCRIPT OUTPUT FOR LIFETIME
> ```bash
> # Optional, save the "old" STDERR
> exec 3>&2
> # Redirect any output to STDERR to an error logfile instead
> exec 2> /path/to/error_log
>
> # Script with "globally" redirected STDERR goes here
>
> # Turn off redirect by reverting STDERR and closing FH3
> exec 2>&3-
> ```

> [!IMPORTANT]
> ## LOGGING TO SYSLOG
> ```bash
> logger -p local0.notice -t ${0##*/}[$$] test message
>
> # LOG ON REMOTE MACHINE
> echo "<133>${0##*/}[$$]: Test syslog message from bash" > /dev/udp/loghost.example.com/514
> # USING NETCAT
> echo "<133>${0##*/}[$$]: Test syslog message from Netcat" | nc -w1 -u loghost 514
> ```

> [!TIP]
> ## DOING TWO THINGS AT A TIME
> * Bash 4+ uses a command `coproc` to do so, normally a pipe can make data flow only on one side, but to do so in reversemanner or coordinated  manner, this is used

> [!IMPORTANT]
> ## RUN SSH COMMAND ON MULTIPLE HOSTS
> ```bash
> for host in host{1..3}; do
>   echo "${host}"
>   ssh $host 'grep "$HOSTNAME" /etc/hosts'
> done
> ```

> [!TIP]
> ## PRIVATE UTILITY STASH
> * Create a `~/bin` directory
> ```bash
> PATH="$PATH:~/bin"
>
> # SCRIPT
> ```

> [!TIP]
> ## INPUTRC
> * Set input behaviour `~/.inputrc`

> [!IMPORTANT]
> ## SYNC HISTORY BETWEEN SESSIONS
> ```bash
> alias hs='history -a ; history -n'
> ```
> * Also see shell history options

> [!TIP]
> ## CREATE and CD INTO THE DIRECTORY IN SAME COMMAND
> ```bash
> mkdir NAME | cd $-
> ```

> [!TIP]
> ## BASH LOADABLE BUILTINS
> * Having code that is loadable into bash

> [!TIP]
> ## BASH INIT FILES
> - `/etc/profile`
> - `/etc/bashrc` (Red Hat) or `/etc/bash.bashrc` (Debian)
> - `/etc/bash_completion`
> - `/etc/profile.d/bash_completion.sh` and `/etc/bash_completion.d`
> - `/etc/inputrc`
> - `~/.bashrc`
> - `~/.bash_profile`
> - `~/.bash_login`
> - `~/.profile`
> - `~/.bash_history`
> - `~/.bash_logout`
> - `~/.inputrc`

> [!TIP]
> ## SHARING SINGLE SESSION
> * Sometimes it is important for training and troubleshooting purposes to share a single session
> * Use `TMUX`
> ## LOGGING ENTIRE SESSION
> * Do it
> ## PREPEND LINE TO FILE
> * Do it

> [!TIP]
> ## USE SUDO ON GROUP OF COMMANDS
```bash
sudo bash -c 'COMPLEX_COMMAND_CHAIN'
```
