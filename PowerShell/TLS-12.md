---
title: TLS 1.2
description: 
published: 1
date: 2022-12-13T21:34:32.656Z
tags: 
editor: markdown
dateCreated: 2022-12-13T21:34:23.307Z
---

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
Install-Package -Name GitVersion.CommandLine -ProviderName nuget -Scope CurrentUser -Force -SkipDependencies
```