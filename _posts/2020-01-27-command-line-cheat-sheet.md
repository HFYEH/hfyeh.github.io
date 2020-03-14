---
layout: post
title: "Command Line Cheat Sheet"
tags: [bash, cheat-sheet]
---

This is a command line memo.

<!--more-->


## ls

```
ls
# -a: Show all visible and hidden file and directories
# -l: Long form listing
# -h: Human readable sizes
# -S: Sort by size
# -t: Sort by last modified time
# -r: Reverse sort
```

## ag

```
ag <regex to search> <folder to search>
# -Q: Literal search
# -l: List filesname only
# -i: Case insensitive
# -G <pattern>: Scope to files with pattern
```

## cURL

```
curl
# -i: Show response information
# -L: Forwarding redirects
# -O: Downloading files
# -X <method>: HTTP request method
# -d "key=value: Passing data in request body
# -d @<file>: Using a file as request body
# -F: Form parameters
# -H "Accept: Application/json": Setting headers
# -u "user:password": HTTP basic auth
# -D <file>: Saving headers and Cookies
# -b <file>: Sending headers and Cookies
# -c <file>: Saving only Cookies
```

## find
```
find . -name '<pattern>': Find files, directories that matches the pattern under current directory
find . -path '<pattern>': Find flies and directories whose path matches the pattern
-type f: Find only files
-type d: Find only directories
find . -name '<pattern1>' -or -name '<pattern1>': Find anything that match one of listing conditions
-not: Not matching query
find . -mtime -1: Find files modified in the last day
find . -mmin -5: Find files modified in last 5 minitues
find . -size +200k: Find files whose size is greater than 200 kB
```

## sed
```
sed 's/[aeiou]/*/' <file>: Substituding all [aeiou] by * in file
sed 's/[aeiou]/*/g' <file>: Global substituding
sed 's/[aeiou]/*/i' <file>: Case insensitive
sed 's/[aeiou]/*/d' <file>: Deleting lines
sed -n 's/[aeiou]/*/p' <file>: Printing modified content
sed ‐n "s/[aeiou]/pw <file>" <file2>: Writing modified content to file2
-i: Editing in place
-i.tmp: Saving the original file to <file>.tmp
```

## cat
```
-b: Showing line numbers
```

## tree
```
-L: Listing layers
-d: Listing only directories
```
