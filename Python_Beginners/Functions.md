# Functions

## Definizione delle funzioni e loro utilizzo

Le funzioni sono dei mini programmi che servono per svolgere un determinato compito; abbiamo visto in precedenza l'utilizzo di diverse funzioni predefinite di Python, quali print(), input(), str(). L'altra caratteristica interessante delle funzioni è il fatto che le possiamo eseguire ogni volta che vogliamo, così da poter riutilizzare il codice scritto quando serve.

Per definire una funzione useremo la parola chiave def, seguita dal nome della funzione e, eventualmente, uno o più parametri compresi tra parentesi tonde, mentre il codice eseguito dalla funzione sarà indendato rispetto alla definizione, vediamo di seguito un esempio:

~~~python
def saluti(nome):
	print('Ciao', nome)
~~~

Di seguito invece vediamo come richiamare la funzione denominata "saluti" appena scritta:

~~~python
nome_in_input = input('Scrivi il tuo nome: ')

saluti(nome_in_input)
~~~

Da notare che questa parte rappresenta il codice principale del nostro programma, e deve essere scritto DOPO la definizione della funzione, infatti è buona norma definire le funzioni all'inizio del programma, seguite dal corpo principale del programma in cui vado a richiamare le funzioni definite in precedenza:

~~~python
# DEFINIZIONE FUNZIONI
def saluti(nome):
	print('Ciao', nome)

# PROGRAMMA
nome_in_input = input('Scrivi il tuo nome: ')

saluti(nome_in_input)
~~~

## Ambito delle variabili

Quando abbiamo a che fare con le funzioni, dobbiamo distinguere l'ambito (scope) delle variabili; se la variabile viene definita all'interno della funzione, la potremo usare solo all'interno di quella funzione, in questo caso si parla di scope locale.

Se invece una variabile viene definita fuori da una funzione, cioé nel corpo principale del programma, potrà essere usata in tutto il programma, e in questo caso parliamo di scope globale.

Ad esempio, tornando al codice precedente, se non usassimo un parametro nella funzione saluti, possiamo scrivere il codice in questo modo:

~~~python
# DEFINIZIONE FUNZIONI
def saluti():
	print('Ciao', nome)

# PROGRAMMA
nome = input('Scrivi il tuo nome: ')

saluti(nome)
~~~

Il codice funziona, ma non potremmo riutilizzare il codice della funzione saluti, poiché questa funzionerebbe solo se dò un valore alla variabile nome, dovrei cambiare il valore ogni volta che volessi utilizzare la funzione, mentre utilizzando una variabile locale come visto prima, cioé con il parametro nome, possiamo usare la stessa funzione con valori differenti.

## Organizzare il codice tramite le funzioni

L'altra caratteristica interessante delle funzioni è che ci permette di di organizzare il codice in unità logiche, migliorando quindi la leggibilità e la comprensione del programma; sempre partendo dall'esempio precedente, possiamo prendere il corpo principale del nostro programma e inserirlo in una funzione main(), che andremo a richiamare subito dopo averla definita:

~~~python
# DEFINIZIONE FUNZIONI
def saluti(nome):
	print('Ciao', nome)

def main():
	nome_in_input = input('Scrivi il tuo nome: ')
	saluti(nome_in_input)

main()
~~~