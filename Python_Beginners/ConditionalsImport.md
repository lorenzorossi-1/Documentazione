# Conditionals and Imports

## Istruzioni condizionali

Come in tutti i linguaggi di programmazione, anche in Python è necessario prendere delle decisioni tramite l'istruzione condizionale if, ciò avviene facendo uno o più confronti, confronti che avvengono tramite 6 operatori di confronto:

- < minore di
- <= minore o uguale a
- == uguale
- \>= maggiore o uguale a
- \> maggiore di
- != diverso da

Un confronto potrebbe essere del tipo: la temperatura è uguale a 35 gradi? che si traduce in due istruzioni da lanciare nell'interprete Python:

~~~python
temperatura = 35
temperature == 35
True
~~~

nella prima riga viene assegnato (operatore =) il valore 35 alla variabile temperatura, nella seconda riga viene fatto un confronto tra il valore della variabile temperatura e 35, siccome i due valori sono uguali il risultato restituito in output è True, come indicato nella terza riga, altrimenti l'output sarebbe stato False.

L'istruzione if consente di decidere cosa fare in base al risultato True o False del confronto; l'esempio più semplice è indicare cosa fare in caso il confronto restituisca il valore True:

~~~python
temperatura = 35
if temperatura > 30:
  print('Troppo caldo!')
  print('Me ne sto in casa')
~~~

L'esempio precedente si può esprimere così: la temperatura corrente è di 35 gradi, se la temperatura è maggiore di 30 gradi, verrà mostrato l'output 'Troppo caldo!' e 'Me ne sto in casa' su due righe. Da notare inoltre che le due istruzioni print fanno parte di un blocco di codice indentato (in genere con due spazi), ciò sta ad indicare che le istruzioni vengono eseguite se il confronto in if è vero; da tenere presente che l'indentazione in Python ha un significato preciso ed è obbligatoria, pertanto se le due istruzioni non fossero indentate, il codice restituirebbe un errore di indentazione, stessa cosa avverrebbe se l'indentazione non fosse precisa (ad esempio una riga indendata con due spazi e la successiva con tre), Python da questo punto di vista insegna ad essere rigorosi, ne guadagna la leggibilità del codice.

Se però l'esito del confronto fosse False? In tal caso le due istruzioni print non verrebbero eseguite, pertanto si può prevedere l'utilizzo dell'istruzione else, che eseguirà un secondo blocco di codice in caso che il confronto fatto con l'istruzione if dia False come risultato:

~~~python
temperatura = 25
if temperatura > 30:
  print('Troppo caldo!')
  print('Me ne sto in casa')
else:
  print('puoi uscire, buon divertimento')
~~~

## Usare più condizioni con l'istruzione if

Ovviamente, abbiamo anche la possibilità di inserire più condizioni, ad esempio possiamo aggiungere, tramite l'istruzione elif, la condizione che se la temperatura è inferiore ai 10 gradi eseguiremo un blocco di codice alternativo, come mostrato di seguito:

~~~python
temperatura = 5
if temperatura > 30:
  print('Troppo caldo!')
  print('Me ne sto in casa')
elif temperatura < 10:
  print('Troppo freddo!')
  print('Me ne sto in casa')
else:
  print('puoi uscire, buon divertimento')
~~~

Con queste condizioni, se la temperatura è maggiore di 30 viene eseguito il primo blocco di codice (condizione if), se la temperatura è minore di 10 viene eseguito il secondo blocco di codice (condizione elif), se la temperatura è compresa tra 10 e 30 (cioé tutti gli altri casi) viene eseguito il blocco di codice che segue l'istruzione else

Le prime due condizioni (quelle indicate con if e elif) possono essere raggruppate assieme tramite l'uso dell'operatore booleano or, con questo significato: se la temperatura è maggiore di 30 oppure (or) minore di 10, allora stampa a video la frase 'me ne sto in casa', se invece la temperatura è compresa tra 10 e 30 gradi, allora stampa a video la frase 'puoi uscire, buon divertimento':

~~~python
temperatura = 25
if temperatura > 30 or temperatura < 10:
  print('Me ne sto in casa')
else:
  print('puoi uscire, buon divertimento')
~~~

Tenere presente che con l'uso di or, è sufficiente che solo uno dei confronti restituisca True per fare sì che tutta la condizione risulta True.

Funziona in modo diverso l'operatore booleano and, che se usato in un confronto, restituisce un risultato True solamente se tutte le condizioni hanno come risultato True; lo possiamo vedere nell'esempio seguente, per il quale indicheremo di uscire solo se la temperatura è inferiore a 35 gradi e se le previsioni del tempo danno tempo diverso da piovoso:

~~~python
temperatura = 25
previsione = 'soleggiato'
if temperatura < 35 and previsione != piovoso:
  print('puoi uscire, buon divertimento')
else:
  print('me ne sto in casa')
~~~

Lo stesso codice si può scrivere usando l'operatore not, con una condizione "not previsione == piovoso", come nel codice che segue:

~~~python
temperatura = 25
previsione = 'soleggiato'
if temperatura < 35 and not previsione == piovoso:
  print('puoi uscire, buon divertimento')
else:
  print('me ne sto in casa')
~~~

I valori booleani possono essere assegnati alle variabili, come mostrato di seguito:

~~~python
piovoso = True
if piovoso:
  print('me ne sto in casa')
else:
  print('puoi uscire, buon divertimento')
~~~

## Importazione moduli

Ipotizziamo di voler sviluppare un piccolo programma che faccia uscire in modo casuale un numero da 1 a 6, così da simulare il lancio di un dado; Python permette questa cosa senza problema poiché possiede le funzioni adatte nella sua libreria standard, ma queste funzioni fanno parte di un modulo, import, che va importato tramite l'istruzione import:

~~~python
import random

lancio_dado = random.randint(1,6)
~~~

in questo modo, la variabile lancio_dado assumerà un valore casuale compreso tra 1 e 6.

## Utilizzo del modulo random e delle sue funzioni

Partendo dall'esempio precedente, è possibile quindi trasformare il nostro piccolo programma in un gioco in cui dobbiamo indovinare il numero che uscirà dal lancio del dado, in modo simile a questo:

~~~python
# IMPORTAZIONE MODULO RANDOM
import random

# ISTRUZIONI
lancia_dado = random.randint(1,6)

indovina = int(input('Indovina il numero del dado: '))

if lancia_dado == indovina:
    print('Dado: ' + str(lancia_dado) + ' - Hai indovinato!')
else:
    print('Dado: ' + str(lancia_dado) + ' - Non hai indovinato, riprova!')
~~~

codice che possiamo salvare in un file chiamato dado.py per essere richiamato poi tramite il comando py dado.py. Da notare l'uso della funzione int per convertire l'input dell'utente da testo a intero, e l'uso della funzione str per convertire la variabile lancia_dado da intero a stringa per poter consentire la concatenazione con il testo adiacente nell'uso della funzione print; inoltre, per poter confrontare sia la situazione a noi favorevole (quando la variabile lancia_dado è uguale alla variabile indovina) che quella sfavorevole, dobbiamo usare le istruzioni condizionali if e else per contemplare entrambe le casistiche.