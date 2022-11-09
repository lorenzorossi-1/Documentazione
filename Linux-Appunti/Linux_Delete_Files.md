# Considerazioni sull'eliminazione di file in Linux

## Utilizzo di rm

Il comando più classico per eliminare file su Linux è rm, che consente appunto di eliminare file, ma anche directory; in particolar modo, rm diventa un comando molto potente quando viene associato all'opzione -r (che consente di cancellare elementi anche nelle sottodirectory della directory di partenza) e all'opzione -f (forza la cancellazione senza chiedere conferma all'utente), ma soprattutto diventa molto potente (e molto rischioso) utilizzare i caratteri jolly, che consentono di specificare più file e cartelle.

Ad esempio, se all'interno della directory /home/utente abbiamo tre directory, indicate (con molta fantasia) come segue

~~~
dir-uno
dir-due
dir-tre
~~~

per cancellare una delle tre directory possiamo digitare il comando

~~~shell
rm -rf dir-uno
~~~

e tutta la directory dir-uno, incluso ovviamente tutto il suo contenuto, verrà eliminata.

Analogamente, se ci troviamo all'interno della directory dir-quattro, con questo elenco di sottodirectory

~~~shell
dir-quattro/
├── dir-quattro_uno
├── dir-quattro_due
├── dir-quattro_tre
├── dir-quattro_quattro
~~~

e cancelliamo la directory dir-quattro_quattro con il comando

~~~shell
rm -rf dir-quattro_quattro/*
~~~

otterremo lo stesso risultato ma utilizzando una sintassi molto più pericolosa, perché se solo sbagliamo a digitare il comando, in questo modo

~~~shell
rm -rf dir-quattro_quattro/ *
~~~

cancelleremo tutte le directory presenti nella directory in cui ci troviamo. Oltre all'ovvia soluzione di stare attenti a ciò che si digita, possiamo prendere alcune contromisure per evitare che questo accada.

## Utilizzo di find per eliminare file e directory

Una possibile contromisura contro le eliminazioni indesiderate di file e cartelle, è quella di usare il comando find, in modo da rendere l'eliminazione un processo in due passaggi:

- uso di find in modalità ricerca, così da vedere quali saranno gli elementi che verranno cancellati nel secondo passaggio, come se fosse un'anteprima
- uso di find in modalità di eliminazione, che compie il processo di cancellazione vero e proprio

Partendo dalla struttura di directory viste prima ma con l'aggiunta di alcune sottodirectory di dir-quattro_quattro

~~~shell
dir-quattro/
├── dir-quattro_uno
├── dir-quattro_due
├── dir-quattro_tre
└── dir-quattro_quattro
    ├── dir-quattro_quattro_uno
    ├── dir-quattro_quattro_due
    └── dir-quattro_quattro_tre
~~~

digiterò il seguente comando

~~~shell
find dir-quattro_quattro
~~~

che mi restituirà questo output

~~~shell
dir-quattro_quattro
dir-quattro_quattro/dir-quattro_quattro_tre
dir-quattro_quattro/dir-quattro_quattro_uno
dir-quattro_quattro/dir-quattro_quattro_due
~~~

che potrò analizzare in sicurezza per verificare che ciò che viene mostrato sia esattamente ciò che voglio davvero eliminare.

Fatta questa verifica, potrò quindi procedere con l'eliminazione vera e propria:

~~~shell
find dir-quattro_quattro -delete
~~~

il successivo output del comando tree sarà il seguente

~~~shell
dir-quattro/
├── dir-quattro_uno
├── dir-quattro_due
└── dir-quattro_tre
~~~

Che è esattamente il risultato che si voleva ottenere.