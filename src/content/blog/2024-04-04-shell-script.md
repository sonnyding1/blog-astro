---
title: Shell script
pubDatetime: 2024-04-04T23:50:00-07:00
postSlug: shell-script
featured: false
draft: false
tags:
  - notes
description: Some notes on writing shell scripts
---
This post serves as a memo for myself in case I need to re-learn writing shell scripts in the future. The note content strictly follows [this tutorial](https://www.shellscript.sh/) by Steve Parker.

## What is sh?

`sh` or the Bourne shell is not Bash! Bash stands for Bourne Again SHell, which is shell with a set of extra features. Bash is equivalent to GNU bash. GNU is a collection of softwares that could be used for an operating system. GNU is a free set of softwares, Linux is a free kernel, so people often use them together, therefore there is GNU/Linux.

## First script

```sh
#!/bin/sh
echo Hello World
```

First line indicates that no matter if you are in zsh or csh or anything else, you should run this script using Bourns shell.

Second line calls `echo` with 2 arguments, therefore spacing between `Hello` and `World` in this case won't make any difference to the output of the script. Meanwhile, if we have `echo "Hello    World"`, then `echo` only receives 1 argument, and the spaces are preserved.

## Variables

```sh
#!/bin/sh
MY_MESSAGE="Hello World"
echo $MY_MESSAGE
```

Note that there should be no space between variable name and the equal sign, nor should there be spaces between the equal sign and the string.

Variables do not need to explicitly declare their types.

### read

```sh
#!/bin/sh
echo What is your name?
read MY_NAME
echo "Hello $MY_NAME - hope you're well."
```

Take user inputs using `read`.

### Scope

When we run a shell script, a new shell is spawned to run the script. The new shell ends when the script ends. So, variables declared inside the script cannot go outside of the script. Unless we use **export**, which enables us to pass variables into the shell script. We can also **source** a script:

```sh
$ . ./myvar2.sh
```

This enables us to both pass variables into and out of the shell script. In fact, sourcing means instead of spawning a new shell to run the script, just run on the current shell. So the scope of the variables is no longer limited to the script itself.

### Concatenation

```sh
#!/bin/sh
echo "What is your name?"
read USER_NAME
echo "Hello $USER_NAME"
echo "I will create you a file called ${USER_NAME}_file"
touch "${USER_NAME}_file"
```

Above is an example of concatenation in shell scripts. We needed `{}` because else the script will call an unwanted variable.

## Escape characters

", \\, $, \`, need to be escaped.

## Loops

### for loop

```sh
#!/bin/sh
for i in 1 2 3 4 5
do
  echo "Looping ... number $i"
done
```

### while loop

```sh
#!/bin/sh
INPUT_STRING=hello
while [ "$INPUT_STRING" != "bye" ]
do
  echo "Please type something in (bye to quit)"
  read INPUT_STRING
  echo "You typed: $INPUT_STRING"
done
```

To make a while loop that just keeps running forever, do `while :`.

```sh
#!/bin/sh
while read input_text
do
  case $input_text in
        hello)          echo English    ;;
        howdy)          echo American   ;;
        gday)           echo Australian ;;
        bonjour)        echo French     ;;
        "guten tag")    echo German     ;;
        *)              echo Unknown Language: $input_text
                ;;
   esac
done < myfile.txt
```

The above example shows 2 new things:

1. `while` can read a file line by line.
2. Shell script has switch case.

## test/conditional

In the while loop example, we had an interesting line:

```sh
while [ "$INPUT_STRING" != "bye" ]
```

Notice how this line has many spaces. This is because `[` is a function, it is called test. Because of that, we need spaces to identify the different arguments being passed in.

```sh
$ type [
[ is a shell builtin
$ which [
/usr/bin/[
$ ls -l /usr/bin/[
lrwxrwxrwx 1 root root 4 Mar 27 2000 /usr/bin/[ -> test
```

Here is how to do if else:

```sh
if  [ something ]; then
 echo "Something"
 elif [ something_else ]; then
   echo "Something else"
 else
   echo "None of the above"
fi
```

Note that `;` can join 2 lines into 1. Also, `\` splits 1 command into 2 lines or more.

`&&` and `||` exist, but they are not logical operators, they are abbreviations for if else. More like ternary operators.

```sh
#!/bin/sh
[ $X -ne 0 ] && echo "X isn't zero" || echo "X is zero"
[ -f $X ] && echo "X is a file" || echo "X is not a file"
[ -n $X ] && echo "X is of non-zero length" || \
      echo "X is of zero length"
```

## More variables

Introduce some more variables.

- $0: the basename of the script
- $1 .. $9: the first 9 arguments passed into the script
- $@: all parameters starting from $1
- $*: all parameters starting from $1, don't preserve spaces. Avoid using it if possible
- $#: number of parameters starting from $1
- $?: the exit value of the last run command
- $$: the PID of the current running shell
- $!: the PID of the last ran background process
- IFS: internal field separator, determines what characters separate a string into different arguments

We can take more than 9 parameters by shifting:

```sh
#!/bin/sh
while [ "$#" -gt "0" ]
do
  echo "\$1 is $1"
  shift
done   
```

## Default value

`echo ${VAR:-default_value}` returns `default_value` if `VAR` is unset, but it doesn't actually modify `VAR`'s content.

`echo ${VAR:=default_value}` actually modifies `VAR`'s content if it is not defined.

## External programs

We can use backticks to run external programs:

```sh
$ MYNAME=`grep "^${USER}:" /etc/passwd | cut -d: -f5`
$ echo $MYNAME
Steve Parker
```

## Functions

```sh
#!/bin/sh
# A simple script with a function...

add_a_user()
{
  USER=$1
  PASSWORD=$2
  shift; shift;
  # Having shifted twice, the rest is now comments ...
  COMMENTS=$@
  echo "Adding user $USER ..."
  echo useradd -c "$COMMENTS" $USER
  echo passwd $USER $PASSWORD
  echo "Added user $USER ($COMMENTS) with pass $PASSWORD"
}

###
# Main body of script starts here
###
echo "Start of script..."
add_a_user bob letmein Bob Holness the presenter
add_a_user fred badpassword Fred Durst the singer
add_a_user bilko worsepassword Sgt. Bilko the role model
echo "End of script..."
```

Above is an example of a function.

Special variables like $1 .. $9 are scoped, but all other variables aren't. This means we can easily change some variables within a function, and the variable remains changed after the function has completed.

### Return

```sh
#!/bin/sh

adduser()
{
  USER=$1
  PASSWORD=$2
  shift ; shift
  COMMENTS=$@
  useradd -c "${COMMENTS}" $USER
  if [ "$?" -ne "0" ]; then
    echo "Useradd failed"
    return 1
  fi
  passwd $USER $PASSWORD
  if [ "$?" -ne "0" ]; then
    echo "Setting password failed"
    return 2
  fi
  echo "Added user $USER ($COMMENTS) with pass $PASSWORD"
}

## Main script starts here

adduser bob letmein Bob Holness from Blockbusters
ADDUSER_RETURN_CODE=$?
if [ "$ADDUSER_RETURN_CODE" -eq "1" ]; then
  echo "Something went wrong with useradd"
elif [ "$ADDUSER_RETURN_CODE" -eq "2" ]; then 
   echo "Something went wrong with passwd"
else
  echo "Bob Holness added to the system."
fi
```

Above is an example of using return in a function.