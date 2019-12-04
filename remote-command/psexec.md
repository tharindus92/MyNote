# psexec





This can be done by using `PsExec` which can be downloaded [here](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)

```text
psexec \\computer_name -u username -p password ipconfig

PsExec.exe \\10.101.4.35 -u Zone -p zone@123 -s -c "C:\a.bat"

```

If this isn't working try doing this :-

1. Open RegEdit on your remote server.
2. Navigate to HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
3. Add a new DWORD value called LocalAccountTokenFilterPolicy
4. Set its value to 1.
5. Reboot your remote server.
6. Try running PSExec again from your local server.



You need to add the 'admin$' share which is your **C:\Windows** location.

1. Go to `C:\windows` and right-click --&gt; `Properties`
2. Hit `advance sharing`
3. Click the check box `Share this folder`
4. Enter the name `admin$` and hit Permissions
5. I would recommend removing 'Everyone' and adding just the users that the PsExec command will use to execute.

Run the PsExec command again and this should resolve your issue.

**Edit:**

You can also turn on your AutoShareServer in the registry, which will automatically create the admin shares.

1. Start regisry `regedit`
2. Search for key `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters\AutoShareServer`
3. Change the `AutoShareServer` key to `1`

## Create Services

C:\Windows\system32&gt;sc create "rudi-eth-app" binpath="C:\Program Files \(x86\)\Windows Resource Kits\Tools\srvany.exe" start=auto \[SC\] CreateService SUCCESS

C:\Windows\system32&gt;"HKLM\SYSTEM\CurrentControlSet\Services\rudi-eth-app\Parameters" /v Application /d "java -jar C:\Users\tharindus\Desktop\rudi-eh-app\rudi-eh-app-0.0.1-SNAPSHOT.jar" The system cannot find the path specified.

C:\Windows\system32&gt;reg add "HKLM\SYSTEM\CurrentControlSet\Services\rudi-eth-app\Parameters" /v Application /d "java -jar C:\Users\tharindus\Desktop\rudi-eh-app\rudi-eh-app-0.0.1-SNAPSHOT.jar" The operation completed successfully.

C:\Windows\system32&gt;sc start rudi-eth-app



sc create "rudi-eth-app" binpath="C:\Program Files \(x86\)\Windows Resource Kits\Tools\srvany.exe" start=auto reg add "HKLM\SYSTEM\CurrentControlSet\Services\rudi-eth-app\Parameters" /v Application /d "java -jar C:\rudi-eh-app\rudi-eh-app-0.0.1-SNAPSHOT.jar" sc start rudi-eth-app

```text
 sc create "rudi-eth-app" binpath="C:\Program Files (x86)\Windows Resource Kits\Tools\srvany.exe" start=auto 
reg add "HKLM\SYSTEM\CurrentControlSet\Services\rudi-eth-app\Parameters" /v Application /d "java -jar C:\Rudi_EH_Application\rudi-eh-app-0.0.1-SNAPSHOT.jar"
sc start rudi-eth-app

```

