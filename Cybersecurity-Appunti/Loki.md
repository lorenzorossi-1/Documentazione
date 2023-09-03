# Analisi di file con Loki

## Cos'è Loki

[Loki](https://github.com/Neo23x0/Loki) è uno strumento scritto in Python che serve per l'analisi di file in ambito di cybersecurity; per la rilevazione di file infetti o potenzialmente pericolosi si basa su:

- regole Yara
- IoC
- nomi file
- connessioni a server Command and Control (C2)

Loki possiede già diversi IoC e diverse regole Yara, ma nulla vieta di aggiungere IoC e regole dalle nostre fonti preferite, ed è possibile anche inserire i propri IoC e le proprie regole

## Installazione di Loki

L'installazione di Loki non è complicata ma è necessario seguire i passaggi corretti, quantomeno in ambiente Linux (io ho testato l'installazione su Ubuntu 22.04), mentre in ambiente Windows esistono i pacchetti precompilati rilasciati sul repository GitHub del software.

Su Ubuntu 22.04, prima di procedere, bisogna installare i seguenti pacchetti con apt e pip:

~~~shell
apt-get install libssl-dev
pip3 install psutil netaddr pylzma colorama yara-python rfc5424-logging-handler
~~~

quindi possiamo proseguire clonando il repo di Loki:

~~~shell
git clone https://github.com/Neo23x0/Loki.git
~~~

Ora è possibile utilizzare il programma

## Utilizzo di Loki

La prima cosa da fare prima di iniziare una scansione con Loki è aggiornare il software e le "firme" (regole e IoC):

~~~shell
python3 loki-upgrader.py
~~~

Ora è possibile eseguire una scansione con Loki:

~~~shell
sudo python3 loki.py --intense -p /scandirectory/ --logfolder /logdirectory/
~~~

Analizzando il comando, possiamo vedere che per prima cosa Loki viene lanciato con i privilegi di root (modalità consigliata), quindi viene indicata l'opzione --intense, che indica di scansionare tutti i tipi di file e non solo quelli "conosciuti" da Loki, dopodiché abbiamo l'opzione -p seguita dal percorso della directory che vogliamo scansionare, per finire con l'opzione --logfolder in cui indichiamo il percorso della directory in cui andare a scrivere i log del programma.

Durante la scansione, il software produrrà un output dettagliato, che in caso di rilevamento di un file sospetto, indica i dettagli del file, compresi gli hash, lo score ottenuto (cioé la probabilità che l'elemento sia infetto) e altre informazioni utili; sotto viene riportato una parte di questo output, dove sono rilevati due file sospetti, uno identificato tramite una regola Yara e l'altro tramite la corrispondenza con un hash: da notare che quando un file viene individuato tramite la corrispondenza dell'hash, avrà sempre uno score pari a 100, mentre lo score ottenuto tramite una regola Yara di solito ha uno score inferiore a 100, a meno che non si vada a specificare uno score specifico all'interno della regola (possibilità che si ha per eventuali regole personalizzate):

~~~shell
[ALERT]
FILE: /scandirectory/fatture_allegato.html SCORE: 100 TYPE: UNKNOWN SIZE: 4568
FIRST_BYTES: 3c21444f43545950452068746d6c3e0d0a3c6874 / <filter object at 0x7fae710e7f40>
MD5: 3cc58e4cbbf91c7ab04f895182934383
SHA1: fad78f6db3153d5a55a3e865fc8c12659fa6d3bd
SHA256: fefe75e7ccb8539fc70e335f12e0bdb54fc89394becf0e79e9ba16b148dc29fe CREATED: Thu Aug 31 09:00:05 2023 MODIFIED: Thu Aug 31 08:59:49 2023 ACCESSED: Thu Aug 31 09:00:48 2023
REASON_1: Malware Hash TYPE: SHA256 HASH: fefe75e7ccb8539fc70e335f12e0bdb54fc89394becf0e79e9ba16b148dc29fe SUBSCORE: 100 DESC: Phishing verificato
[WARNING]
FILE: /scandirectory/59ff99e510d1fd40c727ef7f387717f9ceb018e604ba1f60aa501006777ca0d7 SCORE: 70 TYPE: EXE SIZE: 721408
FIRST_BYTES: 4d5a90000300000004000000ffff0000b8000000 / <filter object at 0x7fae710e7d00>
MD5: 1cd5012d3da50196fe1aba74d312e255
SHA1: 2f26b8cf8978c93cd02d720549e0344776497954
SHA256: 59ff99e510d1fd40c727ef7f387717f9ceb018e604ba1f60aa501006777ca0d7 CREATED: Wed Aug 30 17:26:25 2023 MODIFIED: Wed Aug 30 17:21:55 2023 ACCESSED: Thu Aug 31 17:44:37 2023
REASON_1: Yara Rule MATCH: lr_amadey SUBSCORE: 70
DESCRIPTION: Sample Amadey REF: https://github.com/lorenzorossi-1 AUTHOR: Lorenzo Rossi
MATCHES: $1: 'x7395276.exe', $2: 'k8750452.exe'
~~~

Da questo output non si vede, ma le due segnalazioni si differenziano anche per colore, dove il primo file, identificato tramite hash, ha la sezione [ALERT] in colore rosso con uno score uguale a 100, mentre il secondo file rilevato tramite una regola Yara ha una diversa sezione chiamata [WARNING], colorata in marrone.

Da notare che nel secondo file vengono forniti i dettagli della regola Yara usata per la rilevazione del file sospetto, comprese le corrispondenze definite appunto nella regola (MATCHES: $1: 'x7395276.exe', $2: 'k8750452.exe').

## Gestione delle regole Yara e degli IoC

Loki conserva sia le regole Yara che gli IoC nella sottodirectory signature-base, al cui interno troviamo questa struttura:

~~~shell
signature-base/
├── iocs
├── misc
└── yara
~~~

quindi, proseguendo in iocs troviamo questi file:

~~~shell
iocs/
├── c2-iocs.txt
├── filename-iocs.txt
├── hash-iocs.txt
├── keywords.txt
├── otx-hash-iocs.txt
└── README.txt
~~~

che rappresentano diversi IoC con nomi file "parlanti", ma la cosa è spiegata ancor meglio all'interno del file README.txt:

~~~shell
Files in this directory will be initialised according to strings in their filename.

The string "hash" in the filename will initialise the file as hash IOC list.
The string "filename" in the filename will initialise the file as filename IOC list.
The string "c2" in the filename will initialise the file as C2 server IOC list.
~~~

mentre all'interno della sottodirectory yara vi sono più di 900 regole che vengono scaricate clonando il repository Github del software e aggiornate, come visto in precedenza, con loki-upgrader

La cosa più interessante è che possiamo aggiungere nuove regole Yara semplicemente copiandole all'interno della directory signature-base/yara, dove le nuove regole possiamo crearle noi oppure possiamo scaricarle da altre fonti (a tal proposito un repository Github molto dettagliato è [Awesome YARA](https://github.com/InQuest/awesome-yara) di InQuest).

Analogamente, è possibile aggiungere IoCs a Loki andando a creare uno o più file all'interno della directory signature-base/iocs seguendo le regole indicate nel file README.txt della directory come visto in precedenza, in questo caso esistono svariate fonti dove raccogliere gli IoCs, sarebbe troppo lungo indicarle qui.