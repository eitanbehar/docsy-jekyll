---
title: Debugging Javascript scripts in VS Code
tags: [howto], [javascript]
---

To debug Javascript code in VS Code you will need: 
* VS Code, and two extensions: Live Server, Chrome Debugger.
* Download the JS file for the corresponding site
```
https://<source?/script.js?
```
* Change JS reference to the local file
```
<script src="../scripts/script.js
```
* Format JS file (use some online beautifier, e.g https://beautifier.io/)
* Set your launch.json file
```
{
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Launch localhost",
                "type": "chrome",
                "request": "launch",
                "url": "http://localhost:5500/index.html",
                "webRoot": "${workspaceFolder}/",
                "runtimeExecutable": "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chrome.exe"
            }
        ]
}
```
* Set break point
* Start debugging (F5)


