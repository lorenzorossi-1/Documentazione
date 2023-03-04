# Lists and Loops

## Liste

Una lista in Python è un contenitore per archiviare diversi tipi di dati, vediamo di seguito alcuni esempi:

~~~python
lista_vuota = [] # lista vuota
protocolli = ['HTTP', 'DHCP', 'NTP'] # lista di stringhe
numeri = [3, 6, 9] # lista di numeri interi
lista_mista = [3, 'DHCP', 9] # lista contenenti diversi tipi di dati (interi e stringhe)
lista_liste = [ [3, 6, 9], ['HTTP', 'DHCP', 'NTP'] ] # lista di liste
~~~

Se vogliamo identificare un elemento all'interno di una lista, dovremo usare un indice, che parte da zero fino ad arrivare al termine della lista, ad esempio:

~~~python
protocolli = ['HTTP', 'DHCP', 'NTP'] # lista di stringhe

protocolli[0] # il primo elemento della lista, HTTP
protocolli[1] # il secondo elemento della lista, DHCP
protocolli[2] # il terzo elemento della lista, NTP
~~~

Se volessi aggiungere un altro valore alla lista, posso farlo richiamando il metodo append alla lista appena creata:

~~~python
protocolli.append('DNS') # aggiungo un quarto elemento alla lista

protocolli[3] # richiamo il quarto elemento della lista, DNS
~~~

Tenere presente che un metodo (che rappresenta un'azione applicata ad un oggetto) viene richiamato partendo da un oggetto già esistente, nel nostro caso la lista protocolli, secondo la metodologia:

nomeoggetto.nomemetodo(valore)

Così come possiamo aggiungere elementi ad una lista, li possiamo anche togliere, se ad esempio ci accorgiamo di avere inserito un valore sbagliato, ciò è possibile tramite il metodo remove, che si può richiamare usando il valore dell'elemento, oppure si può usare la funzione del indicando l'indice dell'elemento (in quest'ultimo caso dobbiamo conoscere la sua posizione):

~~~python
protocolli.append('ZIP') # aggiungo un quinto elemento alla lista, errato

protocolli.remove('ZIP') # rimuovo l'elemento con valore 'ZIP' dalla lista

# oppure

del protocolli[4] # rimuovo il quinto elemento dalla lista
~~~

Se vogliamo verificare l'esistenza di un elemento all'interno di una lista, dovremo usare la parola chiave in, come segue:

~~~python
ricerca = 'DHCP'

if ricerca in protocolli:
  print(ricerca + " è presente all'interno della lista protocolli")
else:
  print(ricerca + " non è presente all'interno della lista protocolli")
~~~

Volendo rendere un pochino più interattivo il programma, possiamo scrivere questo codice, in cui l'utente inserisce in input il protocollo da cercare:

~~~python
protocolli = ['DHCP', 'LDAP', 'TCP', 'HTTPS', 'UDP', 'RTSP', 'DNS', 'KERBEROS', 'SNMP']

ricerca = input('Indica il protocollo da ricercare in elenco: ')

if ricerca in protocolli:
    print(ricerca + " è presente all'interno della lista di protocolli")
else:
    print(ricerca + " non è presente all'interno della lista di protocolli")
~~~

## Ciclo for

Avendo sempre come riferimento la lista dei protocolli definita prima, se usiamo la funzione print per mostrare a video la lista, otterremo qualcosa di simile:

~~~python
protocolli = ['DHCP', 'LDAP', 'TCP', 'HTTPS', 'UDP', 'RTSP', 'DNS', 'KERBEROS', 'SNMP']

print(protocolli)

['DHCP', 'LDAP', 'TCP', 'HTTPS', 'UDP', 'RTSP', 'DNS', 'KERBEROS', 'SNMP']
~~~

Ma se volessimo mostrare a video la lista con ogni elemento su una riga separata, allora dovremo usare un ciclo for, come mostrato di seguito:

~~~python
protocolli = ['DHCP', 'LDAP', 'TCP', 'HTTPS', 'UDP']

for protocollo in protocolli:
  print(protocollo)
~~~

dove possiamo notare che dopo la parola chiave if, viene usata la variabile protocollo (il nome della variabile è scelto arbitrariamente, possiamo usare un nome qualsiasi), che rappresenta il valore di ogni singolo elemento della lista protocolli per ogni iterazione all'interno del loop; di seguito viene usata la parola chiave in, seguita dal nome della lista di cui vogliamo rappresentare a video gli elementi.

Nella riga (possono essere naturalmente più di una) successiva viene eseguita l'azione per ogni iterazione del ciclo, in questo caso viene eseguita la funzione print per mostrare a video l'elemento corrente della lista protocolli, inoltre si può notare che l'istruzione print che segue la dichiarazione del ciclo for è indentata, come da regola base di Python; di seguito viene mostrato l'output del codice riportato nel paragrafo precedente:

~~~
DHCP
LDAP
TCP
HTTPS
UDP
~~~

## Programma per calcolare le spese settimanali

Ipotizziamo di voler creare un piccolo programma per calcolare l'ammontare delle spese settimanali per il pranzo, per il quale sotto è riportato il codice completo di commenti, i quali spiegano alcune novità riguardanti la funzione print, come le virgole usate per concatenare stringhe e variabili e la definzione del carattere di separazione usato per la concatenazione:

~~~python
# INSERIMENTO DATI NELLA LISTA spese
spese = [5, 10, 7, 12.5, 9, 15, 6]

esborso = 0

# CICLO FOR DOVE OGNI SPESA VIENE SOMMATA ALLA PRECEDENTE E MEMORIZZATA NELLA VARIABILE esborso
for spesa in spese:
    esborso = esborso + spesa

print('La somma spesa in settimana è di', esborso, 'euro') # NOTARE L'USO DELLE VIRGOLE PER SEPARARE STRINGHE E VARIABILI

print('La somma spesa in settimana è di €', esborso, sep='') # NOTARE L'USO DELL'OPERATORE SEP PER SEPARARE STRINGHE E VARIABILI

totale = sum(spese) # OPERATORE SUM CHE CONSENTE DI FARE LA SOMMA DI UNA LISTA

print('La somma spesa in settimana è di', totale, 'euro')
~~~

## Ciclo for con funzione range()

Rispetto al codice precedente, se volessi aggiungere la possibilità di popolare la lista tramite l'input dell'utente dovrei prevedere l'uso di 7 (o più) richieste di input e 7 (o più) inserimenti dei valori raccolti in input nella lista. Per farlo in modo più elegante è possibile usare la funzione range.

La funzione range completa di argomento (che chiamiamo n) permette di generare una lista di interi che vanno da 0 a n-1, come mostrato di seguito:

~~~python
range(7)

lista = range(7)
~~~

Se vogliamo vedere i valori della lista popolata dalla funzione range, possiamo usare il nostro ciclo for in questo modo:

~~~python
for i in lista:
  print(i)
~~~

il cui output sarà il seguente:

~~~
0
1
2
3
4
5
6
~~~

lo stesso identico range può essere definito in modo più completo indicando un inizio, una fine e il passo da usare tra inizio e fine

~~~python
lista = range(0,7,1)
~~~

per capire meglio, è possibile ad esempio fare una sorta di tabellina del 3 in questo modo

~~~python
lista = range(3,33,3)
~~~

Tornando al programma di inserimento spese, è possibile migliorarlo sensibilmente usando un input per chiedere il numero di spese da inserire, quindi tramite la funzione range, chiedere all'utente per n volte di inserire la spesa del giorno e memorizzarla nella lista delle spese, per poi alla fine calcolare il totale tramite la funzione sum:

~~~python
num_spese = int(input('Indica il numero di spese da inserire: '))
spese = []
totale = 0

for i in range(num_spese):
    spese.append(float(input('Indica la spesa sostenuta in euro: ')))

totale = sum(spese)

print('La somma delle spese è di', totale, 'euro.')
~~~

da notare nella riga di codice all'interno del ciclo for l'utilizzo dell'istruzione float, che consente di convertire il valore ottenuto in un numero decimale.

A questo codice possiamo aggiungere il calcolo della media delle spese sostenute, calcolato dividendo il valore della variabile totale per il numero degli elementi della lista spese, che si ottiene tramite la funzione len(); inoltre, faremo in modo che le due istruzioni print risultanti vengano mostrate a video sulla stessa riga, tramite l'utilizzo della parola chiave end:

~~~python
num_spese = int(input('Indica il numero di spese da inserire: '))
spese = []
totale = 0

for i in range(num_spese):
    spese.append(float(input('Indica la spesa sostenuta in euro: ')))

totale = sum(spese)
media = totale / len(spese) # NOTARE L'UTILIZZO DELLA FUNZIONE len, CHE CALCOLA IL NUMERO DI ELEMENTI DELLA LISTA spese

print('La somma delle spese è di', totale, 'euro.', end=' - ') # NOTARE L'UTILIZZO DELL'ISTRUZIONE end, SEGUITA DAL CARATTERE DI SEPARAZIONE RISPETTO ALLA SUCCESSIVA FUNZIONE PRINT
print('La media delle spese sostenute è di', media, 'euro.')
~~~