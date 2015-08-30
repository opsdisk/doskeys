### Introduction

Ever wanted to have persistent, BASH-like aliases for Windows?  Unfortunately, Windows makes this a little more convoluted, but it is still possible!

In the Unix world, users have the ability to create a file, usually called `.bash_aliases`, that contains user-defined shortcuts for executing commands.  The example below greps out a case-insensitive string from a process list:

    alias psg='ps -ef | grep -i $1'

Where `$1` is the first argument (a string in this case) to grep for.  An example would be:

    user@box:~# psg fire
    root       4604   4409 71 19:33 ?        00:00:04 /usr/bin/firefox

In the Windows world, these command line shortcuts (macros) are created using Doskey, defined as

> The Doskey utility lets you encapsulate command strings as easy-to-enter macros. (https://technet.microsoft.com/en-us/magazine/ff382652.aspx)

### Create Your Macros

The first step is to create a text file of your macros, and save it as whatever you like, [doskey_macros.txt](https://github.com/opsdisk/doskeys/blob/master/doskey_macros.txt) is used in this example.  Below are some of my most used macros:

    h=doskey /history
    ps=tasklist $*
    ls=dir /a /x $*
    lt=dir /a /x /od $*
    d=cd %USERPROFILE%\desktop
    p=ping yahoo.com -n 1 || ping 8.8.8.8 -n 1
    findgrep=dir /s /a /b \*$1*
    n=notepad $*
    e=explorer .
    cya=shutdown /f /s /t 0
    reboot=shutdown /f /r /t 0
    pspath=wmic process get processid,parentprocessid,executablepath
    psg=tasklist | findstr /i $1
    nsg=netstat -nao | findstr /i $1
    nd=mkdir $1 $t cd $1
    cp=copy $*
    mv=move $*
    ifconfig=ipconfig $*
    macros=doskey /macros

Most of them should be self-explanatory...play around with them to see how they work. Just like with the BASH aliases, `$1` represents the first user-defined argument, `$*` represents all the user-defined arguments, and `$t` is used to chain commands (like `&` on the command line).  More details and options can be found here: https://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/doskey.mspx?mfr=true

In order to load the Doskey macros after opening a cmd.exe shell, the command is:

    doskey /macrofile=doskey_macros.txt

That's kind of a pain to do every time you launch a shell, so how can we make it persistent so that it loads every time?  

### Make the Doskey Macros Persistent

The `autorun` registry key found here `hklm\software\microsoft\command processor` can be used to load your Doskey macros automatically when cmd.exe is launched.

    reg query "hklm\software\microsoft\command processor" /v autorun
    
    reg add "hklm\software\microsoft\command processor" /v autorun /t reg_expand_sz /d "doskey /listsize=999 /macrofile=c:\users\opsdisk\doskey_macros.txt" /f

When cmd.exe is launched, it automatically loads the doskey_macros.txt file!  To my knowledge, these do not work for PowerShell (powershell.exe) shells. 

The code can be found here: https://github.com/opsdisk/doskeys

Comments, suggestions, and improvements are always welcome. Be sure to follow [@opsdisk](https://twitter.com/opsdisk) on Twitter for the latest updates.
