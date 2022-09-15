# Visualizing TLS Encryption

## TLS Encryption Protocols

Data Encryption Protocols sono protocolli di criptazione dei dati, chiamati anche "cipher", di seguito un elenco:

- 3DES (168 bit) (deprecato)
- AES (128 o 256 bit)
- Chacha20

In AES e Chacha20, abbiamo dei protocolli aggiuntivi che servono per validare (in termini di integrità e autenticazione) la crittografia dei dati:

- AES
	- GCM (Galois Counter Mode)
    - CBC (Cypher Block Chaining)
- Chacha20
    - Poly1305

Questi protocolli che controllano l'integrità, semplificando, aggiungono un hash rappresentativo dei dati criptati, in modo che l'altra parte, calcolando l'hash dei dati criptati in arrivo, può fare un confronto con l'hash inviato, e se entrambi gli hash sono identici, ciò significa che i dati non hanno subito modifiche durante la fase di trasporto.

## TLS Handshake Integrity

Nella fase di handshake di TLS, che avviene subito dopo il 3way handshake TCP, vi sono alcune fasi che servono ad assicurare l'integrità dell'handshake:

- client hello (da client verso server)
- server hello, che è la risposta del server verso il client, risposta che contiene il certificato e la server key
- il client invia al server la client key (da qui è creata la chiave simmetrica che viene usata per AES o Chacha20)
- dal client vengono presi alcune informazioni da ognuna delle fasi precedenti, a cui viene applicato un algoritmo per calcolare l'hash per verificare l'integrità
- il server compie le stesse operazioni, generando a sua volta un hash, che se tutto è ok sarà identico a quello inviato dal client
- lo step precedente viene fatto dal server verso il client

L'algoritmo di hash utilizzato per assicurare l'integrità dell'handshake TLS può essere uno tra:

- SHA
- SHA-256
- SHA-384

## Certificates

I certificati HTTPS vengono rilasciati dal server web verso il client.
Tutto parte da un'autorità di certificazione, che dev'essere validata se vogliamo che venga riconosciuta sulla Internet pubblica; questa autorità di certificazione produce una chiave privata, la quale, combinata ad operazioni matematiche, genera un certificato (si tratta di un gruppo di numeri memorizzati in un documento). Il certificato in questione prende il nome di root certificate, che viene usato per generare altri certificati

I vari root certificates riconosciuti pubblicamente sono già installati sul SO e sui browser del proprio PC, e devono essere quindi aggiornati periodicamente.
La catena di creazione del certificato HTTPS inizia con il root certificate che crea un certificato chiamato "intermediate certificate", il quale a sua volta viene usato per creare il certificato server, che è quello installato sul server web che accetta connessioni HTTPS

---

Quando avviene una richiesta HTTPS da un client verso il server web, quest'ultimo, nella fase di TLS handshake, invia un certificato server al client, il quale, tramite alcune formule matematiche, calcola un valore che viene confrontato con il root certificate per verificare che il server è proprio chi afferma di essere (verifica, ad esempio, che il server www.lorenzorossi.net è veramente www.lorenzorossi.net e non un sito "trappola"), cioé il certificato è valido e non è scaduto.

Da ciò si evince che lo scopo del certificato è "autenticare" il server web a cui si vuole accedere in modo sicuro.