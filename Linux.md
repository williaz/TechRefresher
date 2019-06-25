### Linux Directory Structure
```bash
/     # root
/bin  # binary
/etc  # sys confg
/opt  # opt/3rd party software
/home
/usr
/tmp  # typically cleeared on reboot
/var

App(not for base OS) installed in /usr/local or /opt
```
### Basic Command
```bash
ls
cd
pwd
cat
echo
man
exit
clear
.
..
cd - # go to prev dir
$OLDPWD
mkdir -p dir1
rmdir -p dir2
rm -rf dir3

# exp
man ls
which ls
$PATH
echo $OLDPWD
```
### listing file

```bash
>ls -l
drwxr-xr-x   10 williaz  staff    320 Jul 10  2016 git

# permisions, num of links, owner, group, num of bytes, last modify time, file name
# r(read), w(write), x(execute)
# d rwx r-x r-x
# type, user, group, other
ls -F # reveal file type
/ dir
@ link
* executable

-a # all, hidden files
-- color
-d # list dir??
-l
-r # reverse order
-R # list files recursively
-t # sort time, most recent first
```
### Permission
```bash
chmod
ugoa # user, group, other, all
+-=
rwx  # read, write, execute

# d rwx r-x r-x
    111 101 101 # binary
    7    5   5  # Octal
```
- chgrp
```bash
~ williaz$ groups
staff com.apple.sharepoint.group.1 everyone localaccounts _appserverusr admin _appserveradm _lpadmin _appstore _lpoperator _developer _analyticsusers com.apple.access_ftp com.apple.access_screensharing com.apple.access_ssh
~ williaz$ ls -ltr sp.txt
-rw-r--r--  1 williaz  staff  0 May 23 20:59 sp.txt
~ williaz$ chgrp everyone sp.txt 
~ williaz$ ls -ltr sp.txt
-rw-r--r--  1 williaz  everyone  0 May 23 20:59 sp.txt
```
- The umask acts as a set of permissions that applications cannot set on files
```bash
umask -S
```
### Finding
- find \[path] \[exp]: recursively find
```bash
-name
-iname  # ignore case
-ls
-exec command {} \;
```
- locate
   - faster
   - use index
   - not real time
   - may not be enabled
   
### View
```bash
cat file  # display
more/less file  # browse
head/tail file # default 10 lines
```
- nano, or pico
  - ctrl + X

### Vi
```bash
vi 
vim
view  # read only
^  # top
$  # bottom
:n # cursor to line n
:$ # curser on last
:set nu  # turn on line no.
:set nonu # off
:help [cmd]

u  # undo
ctl + R  #redo

x # delete a char
dd # delete a line
dw # del a word
D # delete from [curr to right]

/  # forwaed search
?  # backward search

```
