# How to Remove Services in Windows 10



Remove services from the list in the services.msc utility in Microsoft Windows with one of these two options.

#### Option 1 – Command

1. You can also remove services using a command line. Hold down the **Windows Key**, then press “**R**” to bring up the Run dialog.
2. Type “_**SC DELETE servicename**_“, then press “**Enter**“.

“servicename” should be the exact name of the service.

#### Option 2 – Registry

1. Hold down the **Windows Key** and press “**R**“.
2. Type “**regedit**“, then select “**OK**“.
3. Navigate to the following location in the registry:
   * **HKEY\_LOCAL\_MACHINE**
   * **System**
   * **CurrentControlSet**
   * **Services**
4. There are folders under “**Services**” that contain each service. The “**DisplayName**” values in each of these folders equals the Service Name. Go through the list or use the “**Edit**” &gt; “**Find**” menu to search for the service you wish to remove.

#### FAQ

_**These steps only delete the name of the service. How do I delete the actual program that is running as a service?**_

You can track it down with these steps:

1. Right-click the taskbar and choose “**Task Manager**“.
2. Select the “**Services**” tab.
3. Right-click the service you wish to delete, then choose “**Go to details**“.
4. The process related to the service is highlighted. If you wish to remove it, you can track it down by right-clicking it and selecting “**Open file location**“. From there, either uninstall the related program from “**Add/Remove Programs**” or delete the EXE file as desired.

