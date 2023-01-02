# Enumerating Services and Processes

## Connessione TCP con host con .Net Namespace

~~~PowerShell
$ipAddress = "192.168.1.1"
$tcpPortDst = "80"
$tcpClient = New-Object System.Net.Sockets.TcpClient
$tcpClient.Connect($ipAddress, $tcpPortDst)
~~~

o in alternativa

~~~PowerShell
$tcpClient = New-Object System.Net.Sockets.TCPClient –Argument $ipAddress,$tcpPortDst
$tcpClient | Select-Object -ExpandProperty Connected
~~~

o in alternativa verificare moduli di terze parti come PSnmap o PortScan

## Creare un listener (aprire in ascolto una porta)

~~~PowerShell
$tcpListener = New-Object System.Net.Sockets.TcpListener -ArgumentList 800 # (800 è il numero di porta, fare un'eccezione sul firewall se necessario)
$tcpListener.Start() # avvia l'ascolto sulla porta 800
$tcpListener.Stop() # ferma l'ascolto sulla porta 800
~~~