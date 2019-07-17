- [x] print 10th line
```bash
sed -n '10p' file.txt
```
- [x] phone number search
```
grep -e '^([0-9]\{3\}) [0-9]\{3\}-[0-9]\{4\}$' file.txt
```
- [x] Word Frequency
```bash
cat words.txt | tr -s ' ' '\n' | sort | uniq -c | sort -r | awk '{ print $2, $1 }'
```

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
### File

```bash
rm file
rm -r fir

cp -i # interactive if existing
cp -r

mv source destination
mv -i

sort file # sort text in file
sort -r # reverse order

tar [-] c|X|t f tarfiile [pattern] # create, extract, list contents of the tar

gzip # compress to .gz
gunzip #uncompress
gzcat
zcat

du -k # in KB
du -h # human format


stdin 0
stdout 1
stderr 2

> # redirect stdout
>> # append stdout
< # redirect stdin

2>&1
2>file

diff file1 file1
stiff  # side by side
vimdiff # highlight diff in vim


grep 
-i # ignoring case
-c # count occ
-n # line num
-v # invert match

file # file type
williaz$ file Documents/
Documents/: director

strings # display printable strings

# PIPE
command-output | command-input

cut 
-d delimiter 
-f N # display Nth field

tr ":" " " # translate char

column # formats its input into multiple columns
-s # delimiter

using a client
SCP # secure copy
SFTP # SSH

scp source dest
sftp host
ftp host # start a session, plain text transfer

alias ls='ls -ltr'
unalias
unalias -a # all
```

#### Env var
- only affect current session only
```bash
printenv # env variables

printenv HOME # case sensitive

echo $HOME

export VAR="value" # no space

unset VAR # remove

cat ~/.bash_profile


su [usrname]
- # fresh start
-c command
whoami

sudo # Super User Do

sudo su # root
sudo -u user command

sudo -s # root

visudo # sudo conf
```

