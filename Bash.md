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

