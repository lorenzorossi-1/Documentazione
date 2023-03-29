# Uso dei protocolli di discovery con Powershell

## Contesto

Sugli host di una di rete possono essere configurati i cosidetti protocolli di discovery, quali LLDP e CDP, che consentono di descrivere e rilevare la topologia di una rete, infatti tipicamente questi protocolli sono configurati sugli apparati attivi come gli switch; tramite l'utilizzo di questi protocolli possiamo quindi vedere quali dispositivi sono collegati alla diverse porte di uno switch, questo è utile quando dobbiamo fare troubleshooting sulla rete o abbiamo problemi nella propagazione di alcune VLAN.

Come per tante altre operazioni su reti e PC, l'automazione ci può dare una grossa mano nel velocizzare i tempi di risoluzione dei problemi, ed è qui che entra in gioco Powershell: grazie ad uno specifico modulo chiamato PSDiscoveryProtocol, possiamo ottenere la cattura dei pacchetti LLDP e CDP generati dagli apparati di rete attivi, ma non solo, possiamo ottenere questi pacchetti (e ovviamente le informazioni contenute al loro interno, che è ciò che ci interessa) anche su PC remoti su cui possiamo collegarci tramite WS-MAN.

## Installazione modulo PSDiscoveryProtocol

L'installazione del modulo in questione è semplicissima e identica a quella di altri moduli Powershell:

~~~powershell
Install-Module -Name PSDiscoveryProtocol
~~~

Dopo le autorizzazioni del caso, possiamo cominciare ad utilizzare immediatamente il modulo.

## Ottenere le informazioni dal PC locale

Il primo e più semplice utilizzo consiste nell'ottenere le informazioni che possiamo raccogliere sull'host locale, dove tipicamente si tratta di ottenere i dati dell'apparato attivo a cui è collegato il nostro PC:

~~~powershell
$lldp = Invoke-DiscoveryProtocolCapture -Type LLDP
Get-DiscoveryProtocolData -Packet $lldp
~~~

Il primo comando avvia la cattura dei pacchetti LLDP, cattura che dura 30 secondi (possiamo specificare una durata diversa con l'argomento -Duration), così come da impostazione predefinita degli annunci fatti dal protocollo LLDP, ed il risultato della cattura viene memorizzato nella variabile $lldp; nella riga successiva, vengono estrapolate le informazioni ottenute dal pacchetto LLDP e presentate a video::

~~~
PortDescription   : 1
Device            : SW-Rete
ChassisId         : ABCDEFG123
TimeToLive        : 120
SystemDescription : Aruba JL321A 2930M-48G Switch
Port              : 1
IPAddress         : {192.168.0.200}
VLAN              : 1001
Computer          : PC01.domain.local
Connection        : Ethernet
Interface         : Realtek PCIe GbE Family Controller
Type              : LLDP
~~~

Da questo output possiamo evincere che il PC denominato PC01 è collegato alla porta 1 dello switch Aruba chiamato SW-Rete che ha indirizzo IP 192.168.0.200, ottenere questa informazione ci può risparmiare un "viaggio" dalla presa di rete a muro fino al patch panel dell'armadio di rete per poi seguire il percorso del cavo che dal patch panel va allo switch, con un notevole guadagno di tempo.

## Ottenere le informazioni da un PC remoto

La cosa ancora più simpatica e utile soprattutto in reti di discrete dimensioni consiste nel fatto che con le stesse modalità posso fare questa interrogazione su un PC remoto, e in una rete distribuita geograficamente ciò può tornare molto utile.

Per poter procedere, devo semplicemente aggiungere un argomento al comando Invoke-DiscoveryProtocolCapture visto precedentemente:

~~~powershell
$lldp = Invoke-DiscoveryProtocolCapture -Type LLDP -ComputerName PC01
Get-DiscoveryProtocolData -Packet $lldp
~~~

Dove PC01 è il nome del PC remoto su cui devo poter accedere tramite WS-MAN. Tutto qua, con questo comando otterrò le stesse informazioni (con valori chiaramente diversi) che ho ottenuto con il comando precedente, per cui evito di riportarne l'output.

## Esportare il pacchetto in formato PCAP

Ciò che viene effettuato tramite questo comando non è altro che la cattura di un pacchetto di rete relativo a LLDP o CDP, e il modulo PSDiscoveryProtocol consente di esportare questo pacchetto in formato PCAP, cosa utile se si vuole analizzare il pacchetto (a scopo di studio o altro) tramite Wireshark, in questo modo:

~~~powershell
Export-Pcap -Packet $lldp -Path C:\Temp\lldp.pcap
~~~