---
title: My PowerShell Cheatsheet
date: 2025-01-13 18:00:00 +0100
categories: [cheatsheets]
tags: [cheatsheet, powershell]     # TAG names should always be lowercase
description: Commands I encountered when learning a bit about PowerShell.
---

## Getting help
```
help Get-Service
```
`Get-Help` is a bit unhandy as it scrolls down the help page.


## General

Information about output object
```
<some_command_with_object_output> | Get-Member
```

Getting commands that accept a certain input type
```
Get-Command -ParameterType ServiceController
```

Brackets for passing output as paramters
```
Stop-Service -DisplayName (Get-Content -Path $env:TEMP\services.txt)
```
