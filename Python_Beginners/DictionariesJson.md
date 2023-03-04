# Dictionaries and Reading JSON

## Dizionari

Nell'esempio visto precedentemente relativo ai protocolli di rete, abbiamo visto gli acronimi che corrispondono ad una definizione; in Python potremmo avere due liste separate, una per gli acronimi e una per le definizioni, come segue:

~~~python
proto_name = ['HTTPS', 'DNS', 'TCP']
proto_def = ['Hyper Text Transfer Protocol Secure', 'Domain Name System', 'Transmission Control Protocol']
~~~

cosa corretta ma non funzionale, nel senso che a parte condividere gli indici, le due liste non sono legate in alcun modo tra loro; per ovviare a questa problematica, in Python esistono i dizionari, i quali sono formati da coppie chiavi/valore, in cui ad una chiave corrisponde il rispettivo valore. Nel caso dei protocolli di rete avremo un dizionario così composto:

~~~python
protocolli = { 'HTTPS': 'Hyper Text Transfer Protocol Secure',
'DNS': 'Domain Name System',
'TCP': 'Transmission Control Protocol' }
~~~

Per ottenere un valore del nostro dizionario, lo devo richiamare attraverso la propria chiave, come mostrato di seguito:

~~~python
print(protocolli['TCP'])

# VALORE IN OUTPUT: Transmission Control Protocol
~~~

Così come per le liste, anche un dizionario può contenere qualsiasi tipo di valore, dalle stringhe, ai numeri, alle liste fino ad altri dizionari.

Per inserire coppie chiave/valore ad un dizionario, oltre alla sintassi vista precedentemente, è possibile partire da un dizionario vuoto e usare una sintassi differente, in modo forse più leggibile:

~~~python
protocolli = {}

protocolli['HTTPS'] = 'Hyper Text Transfer Protocol Secure'
protocolli['DNS'] = 'Domain Name System'
protocolli['TCP'] = 'Transmission Control Protocol'
~~~

La stessa sintassi appena vista per aggiungere una coppia chiave/valore può essere usata anche per modificare il valore corrispondente ad una chiave già esistente.

Se invece volessimo eliminare una coppia chiave/valore, dovrò procedere con l'istruzione del:

~~~python
del protocolli['HTTPS']
~~~

Per richiamare un valore del dizionario tramite la sua chiave abbiamo un'alternativa rispetto a quanto visto in precedenza, cioé usare il metodo get:

~~~python
protocolli.get('HTTPS')
~~~

Questo ci è utile quando richiamiamo una chiave del dizionario che non esiste, poiché in tal modo non ci viene restituito un errore, ma il valore None (che indica appunto l'assenza di un valore):

~~~python
definizione = protocolli.get('HTTP')

print(definizione)

# NON RESTITUISCE NULLA COME OUTPUT
~~~

Questa condizione può essere utilizzata con l'istruzione condizionale if, in modo da evidenziare che una determinata chiave non è presente nel dizionario:

~~~python
definizione = protocolli.get('HTTP')

if definizione:
  print(definizione)
else:
  print('La chiave ricercata non esiste nel dizionario')
~~~

## Ciclo for sui dizionari

Sempre rimanendo nell'ambito dei protocolli di rete, possiamo elencare all'utente un insieme di acronimi disponibili, così l'utente può selezionare quello di cui  desidera conoscere la definizione, ciò si ottiene tramite l'utilizzo di un ciclo for, che consente di ottenere tutte le chiavi del nostro dizionario; se l'utente inserisce un acronimo che non esiste nel nostro dizionario, otterrà un messaggio di avviso. Di seguito il codice di esempio:

~~~python
# DEFINIZIONE DIZIONARIO, SIMILE ALLE HASH TABLE DI POWERSHELL
protocolli = {}
protocolli['HTTP'] = 'Hyper Text Transfer Protocol'
protocolli['DHCP'] = 'Dynamic Host Configuration Protocol'
protocolli['DNS'] = 'Domain Name System'
protocolli['TCP'] = 'Transmission Control Protocol'
protocolli['IP'] = 'Internet Protocol'

print('Sono disponibili le definizioni per questi protocolli di rete:')
for key in protocolli: # DA NOTARE L'USO DELLA PAROLA CHIAVE 'KEY'
    print(key)
protocollo_scelto = input('Scegli il protocollo di cui ottenere la definizione: ')

definizione = protocolli.get(protocollo_scelto) # DA NOTARE L'USO DEL METODO GET, COSI' SE IL PROTOCOLLO SCELTO NON ESISTE NON VIENE RESTITUITO UN ERRORE
if definizione == None: # SE LA VARIABILE definizione NON E' VALORIZZATA, ALLORA VERRA' RESTITUITO L'AVVISO NELLA RIGA SOTTO, ALTRIMENTI VERRA' MOSTRATA LA STRINGA INDICATA DOPO L'ISTRUZIONE else:
    print('Le definizione per il protocollo richiesto non è disponibile')
else:
    print('La definizione per il protocollo', protocollo_scelto, 'è:', definizione)
~~~

## Dizionari e liste

Come già accennato in precedenza, i dizionari possono contenere diversi tipi di valore, tra cui le liste. Ipotizziamo di avere due liste con i protocolli di rete che usano come metodo di trasporto TCP e UDP:

~~~python
TCP = ['HTTP', 'SSH', 'SMB']
UDP = ['DNS', 'NTP', 'SNMP']
~~~

Volendo usare delle liste annidate, potrei fare in questo modo

~~~python
protocolli = [ ['HTTP', 'SSH', 'SMB'], ['DNS', 'NTP', 'SNMP'] ]
~~~

metodo però poco pratico, poiché per richiamare i due elenchi di protocolli dovrei fare in questo modo:

~~~python
print('Protocollli TCP:\t', protocolli[0])
print('Protocolli UDP:\t', protocolli[1])

# la sequenza di caratteri \t indica una tabulazione
~~~

Un metodo più pratico è trasformare le due liste annidate in un dizionario con due chiavi associate a un valore di tipo lista:

~~~python
protocolli = { 'TCP': ['HTTP', 'SSH', 'SMB'], 'UDP': ['DNS', 'NTP', 'SNMP'] }

print('Protocolli TCP:\t', protocolli['TCP'])
print('Protocolli UDP:\t', protocolli['UDP'])
~~~

Ma esiste un modo più elegante per mostrare a video il dizionario con la lista dei valori dei protocolli, tramite un ciclo for:

~~~python
protocolli = {}
protocolli['TCP'] = ['HTTP', 'SSH', 'SMB']
protocolli['UDP'] = ['DNS', 'NTP', 'SNMP']

for chiave, valore in protocolli.items():
  print('Protocolli', chiave, ':\t', valore)
~~~

dove vi sono due elementi nuovi: dapprima, dopo l'enunciazione dell'istruzione for, abbiamo indicato le due variabili che rappresentano la chiave e il valore del nostro dizionario, mentre di seguito istanziamo il dizionario protocolli accedendo ai suoi elementi tramite la proprietà items(), così da poter accedere appunto sia all'elemento chiave che all'elemento valore

## Usare i dizionari per rappresentare oggetti

Un dizionario può servire ottimamente per memorizzare le proprietà o attributi di un oggetto. Rimanendo sempre nell'ambito dei protocolli di rete, potremmo avere una definizione delle proprietà di un protocollo di rete, come acronimo, definzione, trasporto, porta, come mostrato di seguito:

~~~python
proto = {}

proto['acronimo'] = 'HTTPS'
proto['definizione'] = 'Hyper Text Transfer Protocol Secure'
proto['trasporto'] = 'TCP'
proto['porta'] = 443
~~~

Che potrei mostrare a video in questo modo:

~~~python
print('La definizione del protocollo', proto.get('acronimo'), 'è', proto.get('definizione'), 'la cui porta è', proto.get('trasporto'), proto.get('porta'))
~~~

## Usare dizionari annidati

Come già detto, i dizionari possono contenere liste ma anche dizionari annidati; grazie a questa possibilità, potremo strutturare meglio l'esempio precedente inerente i protocolli TCP e UDP con le relative definizioni e le porte corrispondenti:

~~~python
protocolli = {
    "TCP":
        [
            {"acronimo": "HTTPS", "definizione": "Hyper Text Transfer Protocol Secure", "porta": 443},
            {"acronimo": "SSH", "definizione": "Secure SHell", "porta": 22},
            {"acronimo": "SMB", "definizione": "Server Message Block", "porta": 445}
        ],
    "UDP":
        [
            {"acronimo": "DNS", "definizione": "Domain Name System", "porta": 53},
            {"acronimo": "NTP", "definzione": "Network Time Protocol", "porta": 123},
            {"acronimo": "SNMP", "definizione": "Simple Network Management Protocol", "porta": 161}
        ]
}

print("\nProtocolli TCP")
for protocolloTCP in protocolli["TCP"]:
    print(protocolloTCP)

print("\nProtocolli UDP")
for protocolloUDP in protocolli["UDP"]:
    print(protocolloUDP)

print("\nProtocolli TCP")
for protoTCP in protocolli["TCP"]:
    print("Il protocollo", protoTCP["acronimo"], "usa la porta", protoTCP["porta"])

print("\nProtocolli UDP")
for protoUDP in protocolli["UDP"]:
    print("Il protocollo", protoUDP["acronimo"], "usa la porta", protoUDP["porta"])
~~~

Nella prima parte andiamo a definire il dizionario dei protocolli di rete, con due chiavi, TCP e UDP; per rappresentare gli acronimi e le definizioni dei vari protocolli di rete, definiamo una lista che al suo interno contiene tre dizionari, ognuno con chiavi acronimo, definizione e porta, con i relativi valori.

Una volta definiti i dati, li mostriamo a schermo, prima mostrando tutti i dati relativi alla voce TCP e UDP, poi andando a selezionare le singole voci dei dizionari annidati, rappresentando in forma scritta l'acronimo di ogni protocollo TCP e UDP, con la relativa porta; l'output completo si presenterà quindi così:

~~~
Protocolli TCP
{'acronimo': 'HTTPS', 'definizione': 'Hyper Text Transfer Protocol Secure', 'porta': 443}
{'acronimo': 'SSH', 'definizione': 'Secure SHell', 'porta': 22}
{'acronimo': 'SMB', 'definizione': 'Server Message Block', 'porta': 445}

Protocolli UDP
{'acronimo': 'DNS', 'definizione': 'Domain Name System', 'porta': 53}
{'acronimo': 'NTP', 'definzione': 'Network Time Protocol', 'porta': 123}
{'acronimo': 'SNMP', 'definizione': 'Simple Network Management Protocol', 'porta': 161}

Protocolli TCP
Il protocollo HTTPS usa la porta 443
Il protocollo SSH usa la porta 22
Il protocollo SMB usa la porta 445

Protocolli UDP
Il protocollo DNS usa la porta 53
Il protocollo NTP usa la porta 123
Il protocollo SNMP usa la porta 161
~~~

Questa struttura di dati è praticamente identica a quella di un file JSON, un formato di scambio di dati molto usato, soprattutto quando si vanno ad estrarre dati esposti tramite API.

## Uso di API, JSON e installazione di pacchetti con pip

Sul Web è possibile scaricare dati grezzi che varie organizzazioni mettono a disposizione esponendo delle API, che restituiscono appunto dati grezzi, spesso in formato JSON, a cui abbiamo accennato poc'anzi.

La cosa avviene più o meno in questo modo: un client fa una richiesta HTTP - che prende il nome di request - a un server Web, e quest'ultimo risponde con un flusso di dati (in questo caso JSON), che prende il nome di response.

JSON sta per JavaScript Object Notation, così chiamato perché è nato con JavaScript ma può essere usato da pressoché tutti i linguaggi di programmazione. JSON è ampiamente utilizzato per scambiare dati da e verso server web, e come abbiamo visto in precedenza, può essere considerato un insieme di dizionari e liste, spesso annidati tra loro.

Python consente di effettuare richieste HTTP tramite la libreria requests, che però non fa parte della libreria standard, pertanto deve essere installata, e qui entra in gioco pip. Pip è un package manager che serve per installare un qualsiasi pacchetto che fa parte dell'enorme Python Package Index, un repository che consente di estendere le possibilità di sviluppare codice in Python, in modo da non dover reinventare la ruota.

Pip è già installato in modo predefinito con Python, ma in Windows è possibile che si renda necessario mettere il percorso dell'eseguibile nella variabile d'ambiente PATH per fare in modo che parta da qualsiasi posizione.

Per installare la libreria requests, è sufficiente digitare questo comando:

~~~bash
pip3 install requests
~~~

Ora che abbiamo installato la libreria requests, bisogna usarla; un esempio molto semplice è quello di accedere ad una API che consente di conoscere quali e quante persone sono nello spazio al momento attuale, ciò è possibile con la API che mette a disposizione il sito open-notify.org: se accediamo all'indirizzo http://api.open-notify.org/astros.json, vengono restituiti i relativi dati in formato json, di cui viene indicato un esempio (con un estratto dei dati) alla data attuale (per avere una formattazione ottimale in formato json andare sul sito https://jsonformatter.curiousconcept.com):

~~~json
{
   "message":"success",
   "number":14,
   "people":[
      {
         "craft":"ISS",
         "name":"Sergey Prokopyev"
      },
      {
         "craft":"ISS",
         "name":"Dmitry Petelin"
      },
      {
         "craft":"ISS",
         "name":"Frank Rubio"
      }
   ]
}
~~~

Come possiamo vedere, si tratta, come nel caso precedente, di un insieme di dizionari e liste, cosa che abbiamo già imparato a gestire, vediamo quindi un esempio di codice (commentato) per richiedere, tramite la libreria requests, questi dati e presentarli nel formato che riteniamo più opportuno:

~~~python
import requests # IMPORTAZIONE DELLA LIBRERIA REQUEST, PASSAGGIO OBBLIGATO PER POTERLA USARE

response = requests.get('http://api.open-notify.org/astros.json') # OTTENGO LA RISPOSTA DAL SITO IN QUESTIONE USANDO L'ISTRUZIONE requests.get CON L'URL COME ARGOMENTO

dati_json = response.json() # CON IL METODO .json() HO LA DECODIFICA DEI DATI JSON DALLA RESPONSE DEL SERVER WEB

# RAPPRESENTAZIONE DEI NOMINATIVI DELLE PERSONE NELLO SPAZIO
print('Le persone attualmente nello spazio sono', dati_json.get('number'), "con i seguenti nominativi:")
for dato in dati_json['people']:
    print(dato.get('name'))
~~~

che restituisce un output (tagliato per brevità) simile a questo:

~~~
Le persone attualmente nello spazio sono 14 con i seguenti nominativi:
Sergey Prokopyev
Dmitry Petelin
Frank Rubio
~~~