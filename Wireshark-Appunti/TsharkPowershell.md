# Analisi del traffico di rete con Tshark e Powershell

## Contesto

In questo articolo, partendo da una cattura del traffico di rete già effettuata in precedenza e salvata in un file .pcapng, vedremo come poter fare un'analisi del traffico non dall'interfaccia grafica di Wireshark, ma da CLI, con l'ausilio di Tshark (semplificando, Wireshark da riga di comando) e Powershell, usando in modo opportuno i display filter di Wireshark.

**N.B.** Secondo me la riga di comando non può sostituire completamente certe analisi fatte nella GUI di Wireshark (si pensi ad esempio ai grafici), ma consente di avere un quadro generale in modo più rapido e funzionale.

**N.B.** Questo articolo non tratterà la parte di cattura del traffico di rete, che è ugualmente possibile sia con Tshark, sia con dumpcap (entrambe utility CLI di Wireshark) in ambiente Linux o Windows, sia con altri strumenti, come ad esempio tcpdump in ambiente Linux o [Packet Monitor](https://learn.microsoft.com/it-it/windows-server/networking/technologies/pktmon/pktmon) di Microsoft in ambiente Windows, se ci riferisce agli strumenti da riga di comando.

## Utilizzo di Tshark per l'analisi del traffico

Il primo passaggio per analizzare il traffico di rete da riga di comando consiste nell'aprire il file .pcapng con Wireshark, con un comando simile al seguente:

~~~
tshark.exe -n -r cattura_traffico.pcapng
~~~

L'output di questo comando è abbastanza simile a ciò che vediamo nella visualizzazione predefinita di Wireshark, pertanto non risulta molto utile dato che in tal senso è molto più leggibile la visualizzazione nella GUI; ciò che ci serve è prima di tutto filtrare il traffico in modo da focalizzarci solo su ciò che ci interessa, e inoltre dobbiamo specificare quali campi visualizzare, il tutto in un formato che sia maggiormente leggibile da riga di comando.

Partiamo dal filtraggio del traffico: per questo possiamo utilizzare i display filter che conosciamo e utilizziamo in Wireshark, tramite l'utilizzo dell'opzione -Y seguito dalla sintassi del display filter tra virgolette:

~~~
tshark.exe -n -r cattura_traffico.pcapng -Y "http"
~~~

con questo semplice filtro, l'output si riduce alla visualizzazione del solo traffico HTTP, già molto meglio rispetto a prima, ma potrei voler visualizzare solo determinati campi ed escluderne altri, e per farlo devo digitare qualcosa di simile a quanto segue:

~~~
tshark.exe -n -r cattura_traffico.pcapng -Y "http" -T fields -e _ws.col.Time -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport -e http.host -e http.request.method -e http.user_agent
~~~

ai comandi precedenti, ho aggiunto l'opzione -T che permette di specificare il tipo di output, e diverse opzioni -e che consentono di specificare i singoli campi che vogliamo visualizzare a video; a queste opzioni, aggiungo due ultimi ritocchi con il comando

~~~
tshark.exe -n -r cattura_traffico.pcapng -Y "http" -T fields -t ad -T fields -E "separator=/t" -e _ws.col.Time -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport -e http.host -e http.request.method -e http.user_agent
~~~

ritocchi che consistono nell'opzione -t ad che consente di visualizzare data e ora del transito dei singoli pacchetti invece del tempo trascorso dall'inizio della cattura, e l'opzione -E "separator=/t" che indica il separatore tra i diversi campi (la tabulazione è già la visualizzazione predefinita, ma preferisco specificarlo in modo esplicito)

Ora che abbiamo ottenuto i dati così come li vogliamo, sorge un altro problema: anche filtrando, ad esempio, solo il traffico http, potremmo avere comunque quantità consistenti di dati da analizzare, in questo caso avremmo di capire quali sono gli host che generano il maggior traffico, oppure i siti maggiormente visitati, oppure ottenere una lista degli user agent così da escludere problemi di cybersecurity; per ottenere un maggior dettaglio, una possibile soluzione consiste nel convertire i dati ottenuti con Tshark in un oggetto Powershell, il quale consente di fare le elaborazioni che ci interessano.

## Importare i dati di Tshark in un oggetto Powershell

L'output di Tshark visto nell'ultimo comando del precedente paragrafo è sostanzialmente testo con campi delimitati da tabulazioni, cioé di fatto un csv con le tabulazioni al posto delle virgole, in questo modo bastano alcune righe di codice per importare questi dati in un oggetto Powershell:

~~~powershell
# HEADERS DELL'OGGETTO POWERSHELL
$CsvHeaders = 'DataOra','SrcIP','SrcPort','DstIP','DstPort','Hostname','Method','UserAgent'

# LEGGO IL FILE PCAPNG E SALVO L'OUTPUT IN UN FILE DI TESTO
Start-Process -FilePath 'C:\Program Files\Wireshark\tshark.exe' -ArgumentList '-n -r .\cattura_traffico.pcapng -Y "http" -T fields -t ad -T fields -E "separator=/t" -e _ws.col.Time -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport -e http.host -e http.request.method -e http.user_agent' -WindowStyle Hidden -Wait -RedirectStandardOutput tshark_http.txt

# IMPORTO I DATI NELL'OGGETTO POWERSHELL
$objHttp = Get-Content tshark_http.txt | ConvertFrom-Csv -Delimiter "`t" -Header $CsvHeaders
~~~

Analizziamo questo codice: nella prima istruzione, memorizzo le intestazioni del nostro oggetto nella variabile $CsvHeaders, nella seconda istruzione vado ad eseguire il comando Tshark con le relative opzioni utilizzando il cmdlet Start-Process semplicemente per avere una migliore leggibilità del codice (e volendo anche perché con l'argomento -RedirectStandardError posso reindirizzare gli eventuali errori in un file di testo), con la terza istruzione, vado a leggere il contenuto del file tshark_http.txt che ho esportato con il cmdlet Start-Process e tramite il cmdlet ConvertFrom-Csv lo importo nell'oggetto $objHttp, specificando la tabulazione come delimitatore dei campi e indicando le intestazioni da utilizzare per il mio oggetto.

Ora sono pronto per poter elaborare ulteriormente questi dati.

## Esempi di elaborazione dei dati dell'oggetto Powershell

Ora che ho a dispozione il mio oggetto Powershell con i dati relativi al traffico HTTP presenti nel file .pcapng, posso procedere con diverse elaborazioni che mi mostrano solo i dati che reputo interessanti, in modo che ritengo più semplice rispetto a quanto posso ottenere con Wireshark.

### Traffico HTTP verso l'hostname www.google.it

~~~powershell
$objHttp | Where-Object Hostname -eq "www.google.it" | Select-Object DataOra, SrcIP, DstIP, DstPort, Method, URI | Format-Table -AutoSize
~~~

### Ottenere la lista degli user agent e il numero di corrispondenze in ordine inverso

~~~powershell
$objHttp | Group-Object UserAgent | Sort-Object Count -Descending | Select-Object @{Name='Num';Expression={$_.Count}}, @{Name='UserAgent';Expression={$_.Name}}
~~~

### Ottenere la lista degli IP di destinazione e il numero di corrispondenze in ordine inverso

~~~powershell
$objHttp | Group-Object DstIP | Sort-Object Count -Descending | Select-Object @{Name='Num';Expression={$_.Count}}, @{Name='DstIP';Expression={$_.Name}}
~~~