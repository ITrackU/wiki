---
title: Basic Scripting Commands
description: Basic Commands you need to know to begin bash scripting
published: true
date: 2025-04-16T12:54:10.875Z
tags: bash, script
editor: markdown
dateCreated: 2025-04-16T12:54:08.072Z
---

# Basic Unix Script Commands

## Introduction

### Definition of the Shell

A shell is a program that acts as an interface between the user and the kernel, typically used in a terminal in text mode. Bash is the shell from the GNU project.

### File Types

- `-` regular file
- `d` directory
- `l` symbolic link
- `c` character device
- `b` block device
- `s` named socket
- `p` named pipe

## File Commands

### Current Directory
```
$ pwd
```

### Navigate into a Directory
```
$ cd /rep
```

### List Files
```
$ ls
```

### Delete a File
```
$ rm file
```

### Delete a Directory
```
$ rmdir /rep
```

### Copy a File
```
$ cp file1 file2
```

### Move/Rename a File
```
$ mv file1 file2
```

### Create Empty File / Update Timestamp
```
$ touch file
```

### Create a Hard Link
```
$ ln file link
```

### Display File Content
```
$ cat file
```

### Search for Files
```
$ find file
```

## Text Processing

### Remove Sections or Characters
```
$ cut [-c]
```

### Display Last N Lines
```
$ tail [-n N]
```

### Display First N Lines
```
$ head [-n N]
```

### Count Lines, Words, Characters
```
$ wc [-l -w -c]
```

### Sort (unique, numeric, reverse)
```
$ sort [-u -n -r]
```

### Search Matching Lines / Invert Match
```
$ grep pattern [-v]
```

### Replace/Delete Characters
```
$ tr [set1] [set2] [-d -s]
```

### Archive / Extract Directory
```
$ tar [-x]
```

### Pipe Command Output to Another
```
$ cmd1 | xargs cmd2
```

### Output to File + Pipe to Another Command
```
$ cmd1 | tee file.txt | cmd2
```

### File Permissions
```
$ chmod 000 file
```

- r = 4, w = 2, x = 1
- Add values to set permissions
- 1st: owner, 2nd: group, 3rd: others

## Arithmetic Operators

### Syntax
- `((expression))` → true if expression is valid
- `$((expression))` → evaluates and substitutes result

### Example:
```
$ echo $((20+30))
50
```

### Floating Point with bc
```
$ echo "scale=8; 17/7" | bc
2.42857142
```

### Logical Operators
```
$ echo $((10 == 10))  # 1
$ echo $((10 >= 4 ? 1 : 0))  # 1
```

## String Operators

### Existence
```
${var-val}     # var if defined, else val  
${var=val}     # var if defined, else set to val  
${var+val}     # val if var is defined, else empty  
```

### Substrings
```
${var:i}       # substring from index i  
${var:i:k}     # substring from i, length k  
${var:i:1}     # character at i  
${#var}        # length of var  
```

### Case Manipulation
```
${var^^}       # uppercase  
${var,,}       # lowercase  
```

### Start/End Pattern Removal
```
${var#pattern}     # shortest match from start  
${var##pattern}    # longest match from start  
${var%pattern}     # shortest match from end  
${var%%pattern}    # longest match from end  
```

### Argument Expansions
```
$0        # script name  
$1        # first argument  
${10}     # tenth argument  
${!#}     # last argument  
$#        # number of arguments  
$*        # all arguments  
"$@"      # all arguments (quoted)  
```

## Here-Document

### Basic
```
$ cat << STOP
multiline text
STOP
```

### With File and Code Insertion
```
$ cat >| file.txt << STOP
Text block
$(command block)
More text
STOP
```

### File Inclusion
```
. /path/to/file
```

### Interpreter Declaration
```
#!/bin/bash
```

## Conditionals and Loops

### If Conditions (One Line)
```
if cmd ; then block ; fi
```

### If Conditions (Multi-line)
```
if cmd ; then
  block
elif cmd2 ; then
  block2
else
  fallback
fi
```

### Case Statement
```
case word in
  val1) block1 ;;
  val2) block2 ;;
  *) default_block ;;
esac
```

### While Loop

#### One Line
```
while cmd ; do block ; done
```

#### Multi-line
```
while cmd ; do
  block
done
```

### For Loop

#### One Line
```
for var in list ; do block ; done
```

#### Multi-line
```
for var in list ; do
  block
done
```

### Functions
```
my_function () {
  # function body
}
```

### Local Variables
```
local var=value
```

## Arrays

### Declare Single Element
```
t[0]=element
```

### Declare Multiple Elements
```
t=(one two three)
```

### Access Values
```
echo "${t[1]}"   # second element  
echo "${t[*]}"   # all elements  
```

### Array Length
```
${#t[*]}
```

## Test Expressions

### File Tests
```
test -e file     # exists  
test -f file     # regular file  
test -d file     # directory  
test -L file     # symbolic link  
test -r file     # readable  
test -w file     # writable  
test -x file     # executable  
test -N file     # modified since last read  
test file1 -nt file2  # newer than  
```

### Integer Tests
```
test x -eq y     # equal  
test x -ne y     # not equal  
test x -lt y     # less than  
test x -le y     # less or equal  
test x -gt y     # greater than  
test x -ge y     # greater or equal  
```

### String Tests
```
test -z str      # empty  
test -n str      # not empty  
test str1 = str2 # equal  
test str1 != str2 # not equal  
test str1 \< str2 # lexicographically before  
test str1 \> str2 # lexicographically after  
```

### Miscellaneous
```
test -v var     # variable exists  
test -o opt     # shell option set  
test ! expr     # logical NOT  
test a -a b     # logical AND  
test a -o b     # logical OR  
test \( expr \) # parentheses  
```

## Useful Snippets

### Get File Name After Last Slash
```
echo "${file##*/}"
```

### Get Filename Without Extension
```
echo "${file%%.*}"
```

### Example Function: List File Extensions in Directory
```
lister_extensions() {
  local base="$1"
  local ext0=${base##*.}
  for file in "$base"*; do
    local ext=${file#$base}
    if [[ "$ext" = "$ext0" ]]; then
      echo "''"
    else
      echo "${ext#.}"
    fi
  done
}
```

### Example Function: Check if Integer
```
est_entier() {
  local number=$1
  expr "$number" + 1 > /dev/null 2>&1
}
```
