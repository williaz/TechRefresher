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
