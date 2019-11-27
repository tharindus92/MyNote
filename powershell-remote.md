# powershell remote

Enable-PSRemoting -Force -SkipNetworkProfileCheck

Enter-PSSession -ComputerName 10.101.4.35 -Credential Zone

### Save powershell password

read-host -assecurestring \| convertfrom-securestring \| out-file C:\cred.txt

$password = get-content C:\cred.txt \| convertto-securestring

$credentials = new-object -typename System.Management.Automation.PSCredential -argumentlist "Zone",$password

Enter-PSSession -ComputerName 10.101.4.35 -Credential $credentials

### Exec Command

```text
Invoke-Command -ComputerName COMPUTER -ScriptBlock { COMMAND } -credential USERNAME
```



