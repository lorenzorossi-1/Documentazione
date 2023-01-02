# Performing a Network Discovery

## Ping con .Net Namespace (singolo IP)

~~~PowerShell
$destinationIP = "192.168.1.1"
$ping = New-Object System.Net.NetworkInformation.Ping
$ping.Send($destinationIP)
$ping.Send($destinationIP) | Select-Object Address, Status, RoundTripTime
~~~

## Ping con .Net Namespace - Impostare TimeOut

~~~PowerShell
$PingTimeout = 100 (millisecondi)
$ping.Send($destinationIP, $PingTimeout) | Select-Object Address, Status, RoundTripTime
~~~

## Ping con classe WMI Win32_PingStatus

~~~PowerShell
Get-CimInstance -ClassName Win32_PingStatus -Filter "Address='$DestinationIP' and timeout=$PingTimeout" | Select-Object IPv4Address, StatusCode, ResponseTime, ResponseTimeToLive
~~~

Per quest'ultima alternativa, tenere presente questo link: <https://powershell.one/wmi/root/cimv2/win32_pingstatus>

## Ottenere il mac address e il vendor partendo da un indirizzo IP

~~~PowerShell
$ip = '192.168.1.1'
$macAddress = Get-NetNeighbor | Where-Object IPAddress -eq $ip | Select-Object -ExpandProperty LinkLayerAddress
$macVendor = (Invoke-WebRequest -Uri "https://api.macvendors.com/$macAddress").Content
~~~

N.B. la seconda istruzione è una mia personale rielaborazione (l'autore suggeriva di usare nmap per ottenere il mac address e il vendor)
N.B. per ottenere il mac address è possibile anche interrogare il server DHCP in una rete che ne preveda l'uso