---
layout: post
title: "Windows Services Kickstarter in Powershell"
tags: ["windows", "windows service", "powershell", "kickstart"]
---

<div class="message">
Since some legacy Windows services are getting more and more unstable and fall apart without any means of recovery, a kickstarter is needed to ensure those important(?) services don't just disappear quietly without any struggle.
</div>

The idea is to use a combination of Powershell script and Task Scheduler to trigger the job every now and then.

#PowerShell

Powershell is really easy to pick up. It only requires basic knowledge of Command Prompt, and the rest is just C#.

{% highlight powershell %}
function ServiceKickstarter($serverName, $serviceName)
{
    $service = Get-Service -ComputerName $serverName -Name $serviceName
    if ($service.Status -eq "Stopped")
    {
        Write-Host $serviceName "is" $service.Status
        Write-Host "Starting" $serviceName
        $service.Start()
        while ($service.Status -ne "Running")
        {
            Write-Host $serviceName "is" $service.Status
            $service.Refresh()
            Start-Sleep(1)
        }
    }
    Write-Host $serviceName "is" $service.Status
}

function AllServicesKickstarter ($serverName, $servicesName)
{
    $length = $servicesName.length
    for ($i=0; $i -lt $length; $i++)
    {
        "Start " + $servicesName[$i]
        startService $serverName $servicesName[$i]
    }
}

$server = "<server alias>"
$service = "<service name>"
$services = "<service name>", "<another service name>", "<some other service name>"

ServiceKickstarter $server $service
#AllServicesKickstarter $server $services
{% endhighlight %}

NOTE: the server name is not required, if the script is to run from the target server directly.

#Permission

In order to run the script, you may have to sort out execution policy as well, because the restrictions on some servers. And you will see the following message:

{% highlight yaml %}
File … cannot be loaded.  The file is not digitally signed.  The script will not be executed on the system.
{% endhighlight %}

This has everything to do with server execution policy. You can either choose to sign the script, following the [tutorial](http://blogs.technet.com/b/heyscriptingguy/archive/2010/06/17/hey-scripting-guy-how-can-i-sign-windows-powershell-scripts-with-an-enterprise-windows-pki-part-2-of-2.aspx). Alternatively, change your signing policy.

The execution policy details provided by MSDN are:

{% highlight yaml %}
Restricted - No scripts can be run. Windows PowerShell can be used only in interactive mode.
AllSigned - Only scripts signed by a trusted publisher can be run.
RemoteSigned - Downloaded scripts must be signed by a trusted publisher before they can be run.
Unrestricted - No restrictions; all Windows PowerShell scripts can be run.
{% endhighlight %}

Instead of using default (which allows you to run nothing), 'RemoteSigned' is the level that can satisfy our requirement, and manage not to piss sysadmins off.

#Scheduling

The last job is to create the schedule task to make sure the script will check the target service(s). That's just to follow [Task Scheduler Wizard](http://www.7tutorials.com/how-create-task-basic-task-wizard) and you will get there eventually.
