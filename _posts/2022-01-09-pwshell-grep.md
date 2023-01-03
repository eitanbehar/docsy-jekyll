---
title:  "Grep - Powershell style"
date:   2022-01-09 
badges: 
 - type: info
   tag: grep
tags:
  - powershell  
---

Use this powershell command to find text in files, grep style:

```powershell
 gci -r | sls 'some text'
```
 or

```powershell
 Get-ChildItem -Recurse | Select-String 'some text'
```
