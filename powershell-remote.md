# powershell remote

Enable-PSRemoting -Force -SkipNetworkProfileCheck

Enter-PSSession -ComputerName 10.101.4.35 -Credential Zone

### Save powershell password

read-host -assecurestring \| convertfrom-securestring \| out-file C:\Jenkins.txt

$password = get-content C:\cred.txt \| convertto-securestring

$credentials = new-object -typename System.Management.Automation.PSCredential -argumentlist "Zone",$password

Enter-PSSession -ComputerName 10.101.4.35 -Credential $credentials

### Exec Command

```text
Invoke-Command -ComputerName COMPUTER -ScriptBlock { COMMAND } -credential USERNAME
```

### Copy File

```text
$cs = New-PSSession -ComputerName 10.101.15.105 -Credential $credentials

Copy-Item -Path .\rudi-eh-app.tar -Destination C:\Users\tharindus\Desktop\rudi-eh-app.tar -ToSession $cs
```

### Remote Execute Code

```text
Invoke-Command -ComputerName "10.101.4.35" -credential $credentials -ErrorAction Stop -ScriptBlock {Invoke-Expression -Command:"cmd.exe /c 'C:\Users\Zone\Desktop\deploy.bat'"}

```

