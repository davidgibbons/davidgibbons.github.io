---
layout: post
category : automation
tagline: "How to use a blink1 LED to show call status of skype on Windows"
tags : [ blink1, skype, powershell ]
---
{% include JB/setup %}

## I recently got a [Blink(1) LED from ThingM] (http://amzn.to/1nQz1HG) and wanted to to see if i could use it to notify those around me to my status of a call on skype.

First I strugged with getting access to the skype objects that everyone was referencing on the internet, it turns out they're only available within in the 32bit version of powershell. 

I then wrapped that in a batch file to make it easier to call. 

    if ($env:Processor_Architecture -ne "x86")   
    { 
    &"$env:windir\syswow64\windowspowershell\v1.0\powershell.exe" -noninteractive -noprofile  -ExecutionPolicy Bypass -file $myinvocation.Mycommand.path
    exit
    }
    
    
    $skype = New-Object -ComObject Skype4Com.Skype
    
    while($true)
    {
    Start-Sleep -s 15
    
        if ([string]::IsNullOrEmpty($skype.ActiveCalls)) {
            c:\tools\blink1-tool.exe --green -q
        } else {
            c:\tools\blink1-tool.exe --red -q
        }
    }


I then saved this someplace easy to access and wrote a small batchfile to call it, although making a shortcut would have probably also worked:
    
    Powershell.exe -File "C:\Users\dgibbons\Downloads\skype.ps1" -executionpolicy bypass


What this does is sit in the background and every 15 seconds, checks the status of $skype.ActiveCalls, if it's not empty, it sets the blink1 LED red, otherwise it sets it green. 
This just can live and run in the background and keeps my skype status visible to my officemates around me. I checked the performance from the Task Manager and while it does put a tiny bit of load
on the system, it's like 1% or so.
