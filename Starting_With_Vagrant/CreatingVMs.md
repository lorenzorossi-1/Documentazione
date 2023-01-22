# Creating VMs Effortlessy

## Inizializzazione di vagrant: box e VM

Dopo aver installato vagrant, se usiamo un semplice comando "vagrant version" per ottenere la versione di vagrant installata, verrà creata nel profilo del proprio utente una cartella chiamata .vagrant.d, dove vagrant memorizza diverse informazioni, come le immagini delle VM.

Per inizializzare vagrant con il download e la creazione di una VM, è possibile utilizzare il comando vagrant init seguito dal nome di una box come nell'esempio seguente:

~~~bash
vagrant init generic/ubuntu2204
~~~

Per conoscere il nome delle varie "boxes" disponibile per vagrant, andare sul sito <https://app.vagrantup.com/boxes/search> e cercare una box adatta al proprio scopo.

## Comandi basilari di vagrant

Per utilizzare il comando per installare e avviare la VM digitare

~~~bash
vagrant up
~~~

Vagrant non interagisce direttamente con le VM, ma agisce tramite un intermediario che prende il nome di "provider", che altri non è che l'hypervisor (ad esempio virtualbox), provider che quindi dovrà essere installato per far sì che vagrant possa lavorare

In caso di VM Linux, è possibile accedere direttamente alla bash shell della VM tramite il comando

~~~bash
vagrant ssh
~~~

Per vedere lo stato delle VM definite su vagrant, andare nella cartella in cui ho inizializzato la VM e quindi digitare il comando

~~~bash
vagrant status
~~~

Per fare lo shutdown di una VM, digitare il comando

~~~bash
vagrant halt
~~~

Per fermare e eliminare ogni traccia di una VM definita con vagrant, digitare il comando

~~~bash
vagrant destroy
~~~

## Creazione e gestione locale delle box

Per creare una box di una VM esistente, usare il comando

~~~bash
vagrant package --base win11
~~~

dove "win-11" è il nome della VM come definito nell'hypervisor

Per aggiungere una box "locale" a vagrant, digitare il comando

~~~bash
vagrant box win11 win-11.box
~~~

dove win11 è il nome della box e win-11.box è il file della box che abbiamo creato con il comando precedente

Il passaggio successivo consiste nell'inizializzare la box dentro a una directory dedicata con il comando

~~~bash
vagrant init win11
~~~

dove di nuovo win11 è il nome che abbiamo dato alla box (non il nome file)