# Using the Common Information Model (CIM) Cmdlets

## Ottenere la lista dei servizi con avvio automatico

~~~PowerShell
Get-CimInstance -ClassName Win32_Service -Filter "StartMode = 'Auto'"
~~~

## Ottenere la lista dei servizi con avvio automatico e in stato "Stopped"

~~~PowerShell
Get-CimInstance -ClassName Win32_Service -Filter "StartMode = 'Auto' AND State = 'Stopped'"
~~~

## Ottenere la lista dei processi che iniziano con W

~~~PowerShell
Get-CimInstance -ClassName Win32_Process -Filter "Name like 'W%'"
~~~

## Ottenere la lista dei metodi CIM per la classe Win32_Process

~~~PowerShell
Get-CimClass -ClassName Win32_Process | Select-Object CimClassMethods
~~~

## Ottenere la lista dei metodi CIM per la classe Win32_Process in modo più dettagliato, con anche eventuali parametri

~~~PowerShell
$cmethods = Get-CimClass -ClassName Win32_Process | Select-Object CimClassMethods
$cmethods.CimClassMethods
$cmethods.CimClassMethods["Terminate"].Parameters
~~~

## Avviare il processo con nome notepad.exe

~~~PowerShell
Invoke-CimMethod -ClassName Win32_Process -Name Create -Arguments @{ CommandLine = 'notepad.exe' }
~~~

## Terminare il processo con nome notepad.exe

~~~PowerShell
Invoke-CimMethod -Query "SELECT * FROM Win32_Process WHERE name = 'notepad.exe'" -MethodName "Terminate"
~~~