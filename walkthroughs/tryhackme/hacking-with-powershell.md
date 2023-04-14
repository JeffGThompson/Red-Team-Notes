# Hacking with PowerShell



## Basic Scripting Challenge



#### Find all files with password in them

```
$path = "C:\Users\Administrator\Desktop\emails\*"
$string_pattern = "password"
$command = Get-ChildItem -Path $path -Recurse | Select-String -Pattern $String_pattern
echo $command
```



#### Find all files with http://

```
$path = "C:\Users\Administrator\Desktop\emails\*"
$string_pattern = "https://"
$command = Get-ChildItem -Path $path -Recurse | Select-String -Pattern $String_pattern
echo $command
```



## Intermediate Scripting

#### Find open ports

```
for($i=130; $i -le 140; $i++){
    Test-NetConnection localhost -Port $i
}
```
