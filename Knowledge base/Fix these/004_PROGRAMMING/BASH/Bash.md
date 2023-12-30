Pitfalls: https://mywiki.wooledge.org/BashPitfalls#for_f_in_.24.28ls_.2A.mp3.29

style guide: https://google.github.io/styleguide/shellguide.html

BASH questions: http://tiswww.case.edu/php/chet/bash/FAQ

### Basic template

```
#!/usr/bin/env bash

set -o errexit
set -o pipefail

# Globals
COMMIT_MSG_LEN=20
MAIN_BRANCH="main"

error(){
    echo "ERROR: $*" >&2
}

info(){
    echo "INFO: $*" >&2
}

usage(){
    cat <<EOF
BetterCommit (bc) is a small utility which will help you make an habit of writing better commit messages.

Usage: ${0##*/} <subcommands>

Available commands:
    add         Adds all the changes to staging area
    commit      Adds and commits all the changes
    branch      Creates a new branch
    pr          Creates a new pull request

Example usage:

    $ ${0##*/} add

EOF
}
```



## main

```
function main(){
	echo $1 <= first command line argument

}


main "$@"
```



### read input

```
read -r -p "enter number" input
echo $input
```




### switch case

```
case "$VAL" in
	"VAL")
		do this
	*)
		do this
ecac
```



### looping

```
for i in range {1..50}
do
	echo "$i"
done
```