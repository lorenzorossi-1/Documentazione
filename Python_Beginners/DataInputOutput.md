# Data types, input, and output

## Primi passi in Python e interprete dei comandi

L'esercitazione da fare alla fine del modulo consiste nello scrivere un piccolo programma in cui calcolare di quante decadi è composta la propria età. Ciò ci consente di vedere cinque cose quali:

- richiedere interattivamente un input all'utente
- salvare il contenuto dell'input in una variabile
- calcolare le decadi e gli anni
- convertire questi numeri in formato stringa (testo)
- presentare il risultato a video

Per provare le singole istruzioni che compongono il programma, possiamo digitare le istruzioni direttamente nell'interprete Python, che possiamo aprire direttamente in un terminale, ad esempio, su Windows (dopo aver installato Python) ci basta digitare il comando py per aprire l'interprete.

Prima di arrivare all'esempio descritto sopra, partiamo con qualcosa di più semplice come il calcolo dell'area, per il quale digiteremo le istruzioni direttamente nell'interprete Python:

~~~python
base = 10
altezza = 20
area = base * altezza
~~~

da notare che base, altezza e area sono tre variabili, e per le prime due variabili abbiamo assegnato un valore di tipo intero int (numero), che è caratterizzato dal fatto di non avere i doppi apici a differenza delle variabili a cui assegnamo come valore una stringa; Python è in grado di rilevare da solo il tipo di dato assegnato a una variabile, ad esempio, se alla variabile base assegnassimo un valore uguale a 10.5, Python assegnerebbe automaticamente un tipo di dati float. Da notare inoltre che gli operatori matematici in Python sono gli stessi usati in altri ambiti dell'informatica: +, -, *, /

Il problema di questo approccio è che se vogliamo cambiare valori alle variabili, dobbiamo riscrivere tutte le istruzioni; per ovviare a questo problema, sarà necessario quindi scrivere le istruzioni in un file con estensione .py che altro non è che uno script, che verrà interpretato da Python (si rammenta che Python è un linguaggio interpretato, quindi non è necessario compilare le istruzioni per trasformarle in linguaggio macchina)

## Conversione di tipo, input e output

Se vogliamo fare uno script dall'esempio precedente, e vogliamo ottenere come output il valore dell'area, dovremo presentare questo valore sullo schermo tramite la funzione print, con il valore (o la variabile) da rappresentare tra parentesi, valore che prende il nome di "argomento":

~~~python
print(area)
~~~

Se volessimo trasformare questa variabile (il cui valore è 200, se prendiamo i valori indicati prima) da intero a decimale, dovremo utilizzare l'espressione float:

~~~python
area = float(area)
~~~

dove area assume il valore 200.0

Per assegnare invece un valore stringa a una variabile, dobbiamo usare gli apici o i doppi apici:

~~~python
nome = 'Giulia'
cognome = "Dall'Acqua"
~~~

nella variabile cognome abbiamo usato i doppi apici per definirne il valore, ciò a causa dell'utilizzo del singolo apice, se avessimo usato gli apici l'interprete Python avrebbe restituito un errore

In Python è possibile concatenare le stringhe tramite l'operatore +:

~~~python
nomecompleto = nome + ' ' + cognome
print(nomecompleto)
Giulia Dall'Acqua
~~~

nell'istruzione precedente abbiamo inserito tra le due variabili uno spazio delimitato dagli apici (anche qui potevamo usare allo stesso modo i doppi apici) per separare nome e cognome nell'output a video.

Il problema del codice precedente è la sua staticità. Per renderlo un po' più interattivo, possiamo fare in modo che il programma accetti i parametri nome e cognome come input, tramite la funzione input, che ha come argomento la domanda da fare all'utente:

~~~python
nome = input('Inserire il nome: ')
cognome = input('Inserire il cognome: ')
nomecompleto = nome + ' ' + cognome
print(nomecompleto)
~~~

Come possiamo vedere, i valori inseriti in input saranno memorizzati all'interno delle variabili nome e cognome. Se proviamo a salvare queste istruzioni in un file ed eseguiamo il programma, ci verrà chiesto per prima cosa di inserire il nome, e il programma non avanzerà fino a quando non premeremo Invio per confermare l'immissione del testo, stessa cosa ovviamente per la richiesta di inserimento del cognome; infine, verrà stampato a video il nome completo sulla base dei valori inseriti.

A questo punto abbiamo le conoscenze per scrivere il programma ipotizzato all'inizio del modulo, cioé il calcolo delle decadi di cui è composta la propria età; sotto c'è il codice commentato:

~~~python
eta = int(input('Digita la tua età: ')) # la stringa in input è convertita in numero intero, per poter fare le operazioni matematiche del caso

decadi = eta // 10 # divisione intera, consente di ottenere il numero intero senza resto
anni = eta % 10 # operazione modulo, serve per ottenere solo il resto dalla divisione

print('Hai esattamente ' + str(decadi) + ' decadi e ' + str(anni) + ' anni.') # usata la funzione str() poiché non è possibile concatenare assieme stringhe e numeri
~~~