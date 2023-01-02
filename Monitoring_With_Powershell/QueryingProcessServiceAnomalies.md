# Querying Exported Data For Process Or Service Anomalies

## Esportare il log security da riga di comando

~~~bat
wevtutil export-log Security C:\Logs\log-security.evtx
~~~

oppure

~~~bat
wevtutil epl Security C:\Logs\log-security.evtx
~~~

oppure alternativa con WMI (da approfondire):

<https://itluke.online/2018/10/31/how-to-backup-export-an-event-log-to-an-evtx-file-with-powershell/>

## Id evento da controllare nel log Security

- password spraying (controllare): id evento 4625,4648,4771 (l'autore indica soprattutto l'id evento 4648)
- download tramite powershell (controllare): id evento 4688
- creazione di un servizio (controllare): id evento 4698,4699 (l'autore indica soprattutto l'id evento 4698)

## Ottenere gli eventi con Id 4648 da un log esportato precedentemente su file

~~~PowerShell
Get-WinEvent -FilterHashtable @{ Path = 'C:\Logs\log-security.evtx'; Id = 4648 }
~~~

## Ottenere gli eventi con Id 4648,4688,4698 da un log esportato precedentemente su file

~~~PowerShell
$EventIDs = 4648,4688,4698
Get-WinEvent -FilterHashtable @{ Path = 'C:\Logs\log-security.evtx'; Id = $EventIDs }
~~~

## Ottenere gli eventi con Id 4648,4688,4698 nelle ultime 2 ore da un log esportato precedentemente su file

~~~PowerShell
$EventIDs = 4648,4688,4698
$StartTime = (Get-Date).AddHours(-2)
Get-WinEvent -FilterHashtable @{ Path = 'C:\Logs\log-security.evtx'; Id = $EventIDs; StartTime = $StartTime }
~~~

## Ottenere gli eventi con Id 4688 nelle ultime due ore che contengono il valore 'C:\Windows\System32\csrss.exe'

~~~PowerShell
$StartTime = (Get-Date).AddHours(-2)
Get-WinEvent -FilterHashtable @{ Path = 'C:\Logs\log-security.evtx'; Id = 4688; StartTime = $StartTime; Data = 'C:\Windows\System32\csrss.exe' }
~~~

## Ottenere gli eventi con Id 4648 nelle ultime due ore in cui il valore TargetUserName è uguale a 'lorenzo'

N.B. questo comando è valido solo su Powershell 6 e superiori

~~~PowerShell
$StartTime = (Get-Date).AddHours(-2)
Get-WinEvent -FilterHashtable @{ Path = 'C:\Logs\log-security.evtx'; Id = 4648; StartTime = $StartTime; 'TargetUserName' = 'lorenzo' }
~~~

## Ottenere gli eventi con Id 4648 delle ultime due ore filtrando con query XML

~~~PowerShell
$QueryXML = @'
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">*[System[(EventID=4648) and TimeCreated[timediff(@SystemTime) &lt;= 7200000]]]</Select>
  </Query>
</QueryList>
'@

Get-WinEvent -FilterXml $QueryXML
~~~

## Ottenere gli eventi con Id 4648 delle ultime due ore filtrando con query XPath

~~~PowerShell
$XPath = "*[System[(EventID=4648) and TimeCreated[timediff(@SystemTime) <= 7200000]]]"
Get-WinEvent -FilterXPath $XPath -LogName 'Security'
~~~