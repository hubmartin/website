---
title: Black Magic Probe + VS Code
description:
date: 2018-04-02
menu:
  main:
    parent: tutorials
---


[Black Magic Probe](https://github.com/blacksphere/blackmagic/wiki) is an awesome debugger. It is really small, it contains GDB server in the device and it has also USB-UART converter inside. You don't need to run GDB server on your PC which makes it easier if you periodically switch between Windows, macOS or Linux.
Visual Studio Code is interesting new IDE which is also multi-platform. We also use it with our [BigClown](http://bigclown.com/) open-source IoT electronics.

![VS Code](vscode.jpg)

![LCD Module](lcd-module.jpg)

Finally after few hours with google and my colleague I was able to start debugging.
I'm using default C/C++ plugin from Microsoft, no other plugins installed.


The biggest issue was how to pass the ELF file. It was not possible to send it as an argument to the arm-none-eabi-gdb.exe. For some reason it was not possible to pass it in the "text" commands because of some issue with escape characters. I tried \\, \\\\, / but it always disappeared. The on some forum there was a clever hack to do pass the "cd" command which worked. Not sure why it didn't worked when I passed the same `${workspaceRoot}` to the `"text": "file ${workspaceRoot}"`.



launch.json:

```
{
            "name": "Black Magic Probe",
            "type": "cppdbg",
            "request": "launch",
            "preLaunchTask": "build",
            "cwd": "${workspaceRoot}",
            "MIMode": "gdb",
            "targetArchitecture": "arm",
            "logging": {
                "engineLogging": true
            },
            "program": "${workspaceRoot}\\out\\debug\\firmware.elf",
            "miDebuggerPath": "arm-none-eabi-gdb",
            "customLaunchSetupCommands": [
                {
                  "text": "cd ${workspaceRoot}\\out\\debug"
                },
                {
                  "text": "target extended-remote \\\\.\\COM9" /* replace with your COM or /dev/ttyX */
                },
                {
                  "text": "monitor swdp_scan"
                },
                {
                  "text": "attach 1"
                },
                {
                  "text": "file firmware.elf"
                },
                {
                  "text": "load"
                },
                {
                    "text": "cd ${workspaceRoot}" /* set bath back so VScode can find source files */
                },
                {
                  "text": "set mem inaccessible-by-default off"
                },
                {
                  "text": "break main"
                }
              ],
            "serverLaunchTimeout": 10000,
            "windows": {
                "miDebuggerPath": "arm-none-eabi-gdb.exe"
            }
        }

```