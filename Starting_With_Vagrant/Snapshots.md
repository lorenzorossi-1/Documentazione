# Snapshots

## Effettuare e ripristinare snapshots

Vagrant è in grado di gestire le snapshots, che hanno lo stesso significato che hanno su un qualsiasi hypervisor. Per effettuare una snapshot digitare

~~~bash
vagrant snapshot save nomeSnapshot
~~~

dove nomeSnapshot è un nome identificativo, che verrà richiamato quando si renderà necessario ripristinare o eliminare una snapshot, mentre per visualizzare le snapshot effettuate digitare

~~~bash
vagrant snapshot list
~~~

Per ripristinare una snapshot potremo usare il comando

~~~bash
vagrant snapshot restore nomeSnapshot
~~~

dove anche in questo caso nomeSnapshot rappresenta l'identificativo della snapshot
