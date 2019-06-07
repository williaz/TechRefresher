```bash
#!/path/to/interpreter

VAR_NAME="value(notice non space)"
$VAR_NAME
${VAR_NAME}

VAR_NAME=$(command)
VAR_NAME=`command` # old way

# IF condition
if [ condition-is-true ] # space in []
then 
    commands
elif [ condition ]
then
    commands
else
    commands
fi

# FOR Loop
for VAR in ITEM_1 ITEM_2
do
    commands
done

# Positional Param
$0(the script filename), $1, $2

read # to accept input
```


### exit
- All command return an exit status
- 0-255
- 0 = success; others = error condition
- $? contains the value of the exit status of the previously executed command
- &&, ||
- ; will keep execute next command
- exit

```bash
mkdir fstDir || mkdir secDir
mkdir nexDir && cd nexDir
echo $?
```
### function
- DRY(don't repeat yourself)
- call with name only
- all variable are Global scope, use local for local scope
- use return to return exist code; or the exist status of the last command executed in the unction
- parameters start from $1, ($0 is still script name)

```bash
$ function hell(){
> case "$1" in
>     *.txt)
>       echo "it is file"
>       ;;
>     *.html)
>       echo "it is webpage"
>       ;;
>     *.*)
>       echo "something else"
>       ;;
> esac
> }

$ hell a.txt
it is file
$ hell a.thml
something else
$ hell a.html
it is webpage
```

### wildcard
- \* matches 0 or more char
- ? exactly 1 char
- \[] A char class, matches exactly one char
  - \[!] NOT included
  - Named char class: \[\[:alnum:]]
```bash
ca[nt]*
catch
candy

ls -ltr abc?.txt

[!abc]*
ddd

touch a.txt aa.txt
ls [[:alpha:]].txt
a.txt

```
- escape \\
```bash
*\?
yes?
```

### case
- wildcard
 - \|
```bash
case "$VAR" in
    pattern_1|pattern_3)
         # commands
         ;;
    pattern_2)
        # commands
         ;;
    [yY]*)
        # commands
        ;;
esac
```


