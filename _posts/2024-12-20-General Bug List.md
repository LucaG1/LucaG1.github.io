---
title: General Bug List
date: 2024-12-20 15:00:00 +0100
categories: [bug lists]
tags: [bug list, general tips]     # TAG names should always be lowercase
description: Different problems I encountered (not project specific)
---

Sometimes I solve a problem, forget the exact solution and run into it again later. Solving a problem again If you should actually remember the solution is really annoying and also inefficient. By keeping track of past bugs and problems I hope to get ahead of repeating myself. Maybe I can also help others with solutions from this list at some point.

## Connecting to Github via ssh on Windows

Useful commands:

`git -vT git@github.com` checks connections to github and prints verbose information.

`(New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)` checks for admin rights


### How to check where an executable is located on Windows?

Use for example `where.exe ssh`. Only `where` would not work for some reason.

### How to activate ssh-agent on Windows ?

I used the comands:

`Get-Service ssh-agent` checks whether ssh-agent is running.

`Get-Service ssh-agent | Set-Service -StartupType Automatic` set ssh-agent to start automatically.

`Start-Service ssh-agent` start ssh-agent manually.

`ssh-agent -list` list key registered with the ssh-agent.

`ssh-add $env:USERPROFILE\.ssh\github_ed25519_key` register key with ssh-agent.

Open questions:

- Will the ssh-agent a some point forget the registered keys?
- Is there a GUI for things like `Get-Service` and `Start-Service`?

### Cloning repositories did not work without ssh-config entry at first

`GIT_TRACE2=1 git clone git@github.com:LucaG1/LucaG1.github.io.git` try cloning and print verbose information

The problem was that git did use a build in ssh version and not the openssh/ssh version installed on Windows. Because of this it used a different directory for ssh keys and could not access the ssh-agent.

To fix this I reinstalled git for windows. In the setup wizard there is an option to use the ssh of Windows. Now everything works fine.


## Problem with `sudo apt update` on WSL

I did not update my WSL for some time. When I tried updating it I got an error along the lines "can not find `apt_get`".

This Stackoverflow question helped [https://stackoverflow.com/questions/56218562/how-to-fix-modulenotfounderror-no-module-named-apt-pkg](https://stackoverflow.com/questions/56218562/how-to-fix-modulenotfounderror-no-module-named-apt-pkg)

Apparently the problem occurs when the python version was updated. I did update python for a project a while ago.

First I tried to downgrade python (unsuccessfully):

```
# First install old python version
sudo apt-get install python3.10

# Using update-alternatives
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.10 10
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.11 11
sudo update-alternatives --config python
```

After this did not work I tried the second answer using

` sudo apt install --reinstall python3-apt`

This worked. However the answer mentioned problems with python setups that can occur because of this.

## Conflict of Hyper-V and VirtualBox when trying minikube

First I tried this from the Windows Documentation:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

However it did not work.

Then I tried disabling it via:

To disable:

`bcdedit /set hypervisorlaunchtype off`

To enable:

`bcdedit /set hypervisorlaunchtype auto`

Now everything seems fine. However I'm not sure if I need to reactivate Hyper-V at some point.

## Clang missing for instaling python-ldap

`sudo apt install clang`
