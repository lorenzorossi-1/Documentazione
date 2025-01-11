# SSH, SCP e SFTP con Posh-SSH in Powershell

## Contesto

Quando abbiamo diversi dispositivi (server, apparati di rete attivi, ecc.) che sono gestiti tramite SSH, possiamo trovarci nella necessità di automatizzare le operazioni più comuni, quali esecuzione di comandi, monitoraggio, backup di switch; una possibile soluzione a queste esigenze di automazione è rappresentata dal modulo di Powershell denominato [Posh-SSH](https://github.com/darkoperator/Posh-SSH), il quale consente di inviare comandi tramite SSH, raccogliere il relativo output, e permette anche di copiare file via SFTP o SCP.

## Installazione di Posh-SSH

L'installazione del modulo Posh-SSH in Powershell è molto semplice, basta digitare il solito comando Install-Module, in genere io preferisco, sui miei PC, installare il modulo per tutti gli utenti:

~~~powershell
Install-Module -Name Posh-SSH -Scope AllUsers
~~~

## Gestione delle credenziali di accesso

Come noto, per accedere via SSH le due modalità di autenticazione più usate sono tramite le classiche credenziali oppure tramite chiave pubblica/privata.

Siccome in alcuni casi, quali apparati di rete datati, l'utilizzo di chiavi non è un'opzione, vedremo come gestire le credenziali d'accesso in modo che possano essere utilizzate in uno script senza doverle inserire in modo interattivo; normalmente le credenziali in Powershell vengono fornite tramite il cmdlet Get-Credential:

~~~powershell
$creds = Get-Credential
~~~

tramite cui vengono richiesti username e password, i quali di solito vengono memorizzati in una variabile come nell'esempio illustrato sopra; per salvare le credenziali con password criptata su disco, in modo che siano poi utilizzabili successivamente in uno script, è possibile esportarle in un file XML:

~~~powershell
Get-Credential | Export-Clixml -Path '.\creds.xml'
~~~

Per memorizzare le credenziali in una variabile da utilizzare in uno script va fatta l'operazione inversa:

~~~powershell
$creds = Import-Clixml -Path '.\creds.xml'
~~~

## Gestione delle sessioni SSH

Quando voglio connettermi via SSH ad un dispositivo tramite i comandi del modulo Posh-SSH, per prima cosa vado ad instaurare una sessione, in modo da poter eventualmente lanciare più comandi riutilizzando la stessa sessione; per farlo, utilizzerò il cmdlet New-SSHSession:

~~~powershell
$credSSH = Import-Clixml -Path '.\creds.xml'
$SessioneSSH = New-SSHSession -ComputerName 192.168.15.1 -Credential $credSSH -AcceptKey
~~~

Se voglio vedere quali sessioni SSH sono attive, mi basterà digitare il cmdlet

~~~powershell
Get-SSHSession
~~~

il cui output sarà simile a quanto segue:

~~~powershell
SessionId  Host                                                            Connected
---------  ----                                                            ---------
    0      192.168.15.1                                                       True
~~~

Per chiudere la sessione in questione, possiamo utilizzare il cmdlet Remove-SSHSession specificando la sessione usando indifferentemente l'id della sessione oppure il nome della variabile in cui abbiamo memorizzato precedentemente la sessione stessa:

~~~powershell
Remove-SSHSession -SessionId 0
Remove-SSHSession -SSHSession $sessioneSSH
~~~

## Esecuzione di uno o più comandi SSH e relativo output

Per lanciare un comando tramite la sessione SSH attiva sull'host remoto useremo il cmdlet Invoke-SSHCommand

~~~powershell
Invoke-SSHCommand -SSHSession $sessioneSSH -Command 'ls -l'
~~~

che genera un output simile a questo

~~~powershell
Host       : 192.168.1.15
Output     : {total 8, drwxrwxr-x 2 lorenzo lorenzo 4096 Jan 10 12:00 prova, drwxrwxr-x 2 lorenzo lorenzo 4096 Jan 10 12:00 test}
ExitStatus : 0
~~~

ExitStatus uguale a zero sta ad indicare che il comando è stato eseguito correttamente, mentre Output rappresenta appunto l'output del comando lanciato sul dispositivo remoto; il passaggio successivo consiste nel raccogliere questo output:

~~~powershell
(Invoke-SSHCommand -SSHSession $sessioneSSH -Command 'ls -l').Output
~~~

da cui otteniamo questo:

~~~shell
total 8
drwxrwxr-x 2 lorenzo lorenzo 4096 Jan 10 12:00 prova
drwxrwxr-x 2 lorenzo lorenzo 4096 Jan 10 12:00 test
~~~

## Copia di file e cartelle tramite SCP

Tramite Posh-SSH è possibile copiare file da e verso il dispositivo remoto sia tramite SCP che SFTP. Se voglio copiare file dall'host remoto all'host locale, procederò con il cmdlet Get-SCPItem, tenendo presente che in questo caso non vengono gestite sessioni:

~~~powershell
Get-SCPItem -Path 'prova/' -Destination . -ComputerName 192.168.1.15 -Credential $credSSH -PathType Directory
~~~

Il punto interessante è l'argomento PathType, in cui viene specificato se copiare un file o una directory, se invece di una directory vogliamo copiare un file in locale l'istruzione sarà simile a

~~~powershell
Get-SCPItem -Path 'prova/prova.txt' -Destination . -ComputerName 192.168.1.15 -Credential $credSSH -PathType File
~~~

Se invece vogliamo copiare un file dall'host locale al dispositivo remoto userò il cmdlet Set-SCPItem, e la cosa strana è che con questo cmdlet non esiste la possibilità di specificare l'argomento PathType, apparentemente sembra copiare solo i file e non le directory, in questo modo:

~~~powershell
Set-SCPItem -Path '.\prova.txt' -Destination '/tmp' -ComputerName 192.168.1.15 -Credential $credSSH -AcceptKey
~~~

Ma in caso volessimo copiare più file, o tutti i file all'interno di una cartella? Personalmente l'unica strada che ho trovato è stata quella di elencare i file, e per ogni file procedere alla copia tramite il cmdlet Set-SCPItem:

~~~powershell
(Get-ChildItem -Path '.\test').FullName | ForEach-Object {
    Set-SCPItem -Path $_ -Destination '/tmp' -ComputerName 192.168.1.15 -Credential $credSSH -AcceptKey
}
~~~

## Copia di file e cartelle tramite SFTP

Con la copia di file tramite SFTP, a differenza di SCP torniamo al concetto di sessioni, pertanto prima di procedere a una qualunque copia procediamo a creare una sessione e a memorizzarla in una variabile, in modo da renderla riutilizzabile senza dover conoscere l'ID:

~~~powershell
$sessioneSFTP = New-SFTPSession -ComputerName 192.168.1.15 -Credential $credSSH -AcceptKey
Get-SFTPItem -SFTPSession $sessioneSFTP -Path '/home/utente/prova' -Destination .
~~~

Così facendo viene correttamente copiata la directory "prova", ma per farlo devo specificare il percorso assoluto della directory remota, se indico solo il percorso relativo verrà sì copiata una cartella "prova", ma con all'interno tutto o parte del percorso della directory remota.

Se invece voglio copiare in locale solo un file, la sintassi rimane la stessa, cambia ovviamente il percorso indicato nell'argomento Path

~~~powershell
Get-SFTPItem -SFTPSession $sessioneSFTP -Path 'prova/prova.txt' -Destination .
~~~

Nel caso della copia dall'host locale al dispositivo remoto, il cmdlet da utilizzare è Set-SFTPItem ma la sintassi è molto simile; se ad esempio volessi copiare una cartella dovrei fare così:

~~~powershell
Set-SFTPItem -SFTPSession $sessioneSFTP -Path '.\test\' -Destination './'
~~~

Anche qui, se volessi copiare solamente un file e non un'intera cartella, devo solo valorizzare diversamente l'argomento Path:

~~~powershell
Set-SFTPItem -SFTPSession $sessioneSFTP -Path '.\test\prova.txt' -Destination './'
~~~

Arrivati qui, posso procedere a chiudere la sessione SFTP:

~~~powershell
Remove-SFTPSession -SFTPSession $sessioneSFTP
~~~