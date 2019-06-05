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


### wildcard
- * matches 0 or more char
- ? exactly 1 char
- \[] A char class, matches exactly one char
  - \[!] NOT included
```bash
ca[nt]*
catch
candy

[!abc]*
ddd

ls -ltr abc?.txt
```
- Named char class
```bash
[[:alnum:]]
```
- escape \\
```bash
*\?
yes?
```

