# Collect Data from Multiple Machines for Analysis

## Ottenere la lista di tutti i log degli eventi con uno o più eventi

~~~PowerShell
Get-WinEvent -ListLog * | Where-Object RecordCount -gt 0
~~~

## Ottenere gli ultimi 3 eventi del log Security

~~~PowerShell
Get-WinEvent -LogName Security -MaxEvents 3
~~~

## Ottenere tutte le proprietà degli ultimi 3 eventi del log Security

~~~PowerShell
Get-WinEvent -LogName Security -MaxEvents 3 | Select-Object *
~~~

## Ottenere gli eventi del log Security tramite parametri indicati con una hashtable

~~~PowerShell
Get-WinEvent -FilterHashtable @{ LogName = 'Security'; Id = 4624 } -MaxEvents 3
~~~

## Link interessanti per il cmdlet Get-WinEvent (da approfondire)

- <https://4sysops.com/archives/search-the-event-log-with-the-get-winevent-powershell-cmdlet/>
- <https://adamtheautomator.com/get-winevent/>
- <https://www.sans.org/blog/working-with-the-event-log-part-3-accessing-message-elements/>

## Schedulare un task

~~~PowerShell
$action = New-ScheduledTaskAction -Execute 'C:\WINDOWS\System32\WindowsPowerShell\v1.0\powershell.exe' -Argument '-File C:\Users\lorenzo\Documents\Script\Get-LocalEvents.ps1'
$trigger = New-ScheduledTaskTrigger -Once -At '13:05'
$principal = New-ScheduledTaskPrincipal -UserId 'lorenzo' -RunLevel Highest
Register-ScheduledTask -TaskName 'Get Security Events - Succesfully logon' -Action $action -Principal $principal -Trigger $trigger
~~~