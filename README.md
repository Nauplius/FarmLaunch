**Repository Moved**

This repository has moved to [tseward/FarmLaunch](https://github.com/tseward/FarmLaunch).

# Farm Launch

## What is this?

SharePoint 2016/2019 have a built-in mechanism to create a farm based on an unattended configuration file rather than using PowerShell. While this cannot implement a complete farm, this is for demonstration purposes/thought experiment to show the capabilities of the internal farm creation logic.

This solution specifically targets SharePoint 2019 and was not tested with SharePoint 2016.

## Why?!

Because it's fun and I needed something to do :-)

## Can I use this?

Yes, but... You shouldn't. While this will build an initial farm, it will not provision any service. This includes Central Administration, Web Applications, or IIS Application Pools. It will _configure_ them but not deploy to the server. This would require extra steps (namely calling `GloballyProvision()` or `Provision()` where applicable). This solution also will not build Managed Metadata, Search, or the User Profile Service as those service applications do not appear to offer this functionality.

This solution was likely born from the need to automatically deploy servers into SharePoint Online where, based on what I can see, there are likely services farms where building out other service applications is not required for a standard SharePoint front end.

## How?

The ConfigFile folder contains a file named `unattend.txt`. You must edit this file with the desired variables, including the SQL Server name where the databases will reside. This file must be copied to `C:\Program Files\Common Files\microsoft shared\Web Server Extensions\16\CONFIG\` prior to running this application. When this application runs, it stores the credentials to be used with IIS Application Pools into a slot on the Thread running the application via the [Thread.SetData](https://docs.microsoft.com/en-us/dotnet/api/system.threading.thread.setdata?redirectedfrom=MSDN&view=netframework-4.7.2#System_Threading_Thread_SetData_System_LocalDataStoreSlot_System_Object_) method. We then create the initial farm via `New-SPConfigurationDatabase` and once built, the farm intiialization process parses and processes the unattend.txt file.

Services will be created but will not be provisioned, as previously mentioned.

## What next?

There is more to add to this solution. I'll continue working on it as I have time.

## Settings

You can see the various options you configured from the unattend.txt by using PowerShell. Using the SharePoint Management Shell, run the following PowerShell.

``` PowerShell
$farm = Get-SPFarm
$farm.InitializationSettings
```
