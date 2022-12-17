---
title: Module Building
description: 
published: 1
date: 2022-12-13T21:41:10.845Z
tags: 
editor: markdown
dateCreated: 2022-12-13T21:40:03.468Z
---

## Add Nuget repo
```
Nuget.exe sources Add -Name "<REPO NAME>" -Source "" -username "" -password "<PAT>"
```

## Package the module
```
Nuget spec <MODULENAME>
Nuget pack <MODULENAME>.nuspec
```

## Push Nuget package to repo
```
Nuget.exe -Source "<MODULENAME>" -ApiKey AzureDevOpsServices .\<MODULENAME>.nupkg
```

> Note:
> Your nuget.config file is located at %appdata%\NuGet\NuGet.Config for Windows, and at ~/.config/NuGet.Config or ~/.nuget/NuGet/NuGet.Config for Mac/Linux (depending on the OS distribution).
{.is-info}

## Register PowerShell Repo
```powershell
$patToken = "<PAT>" | ConvertTo-SecureString -AdPlainText -Force
$credsAzureDevopsServices = New-Object System.Management.Automation.PSCredential("<USERNAME>",$patToken)
Register-PSRepository -Name <MODULENAME> -SourceLocation "" -PublishLocation "" -InstallationPolicy Trusted -Credential $credsAzureDevopsServices

Install Module

Install-module -name <MODULENAME> -Repository "" -Credential $credsAzureDevopsServices