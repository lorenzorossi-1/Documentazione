# Examine the TLS 1.2 Handshake in Wireshark

## The TLS Handshake

Analizzando la fase di TLS handshake in Wireshark (N.B.: TLS 1.2), una volta indivduata la conversazione TLS da analizzare, si inizia esaminando il pacchetto contrassegnato da "Client Hello", dove le prime due estensioni TLS nel pannello dei dettagli del pacchetto sono:

- cypher suites: elenco dei protocolli di criptazione dei dati, che comprendono:
    - il protocollo di Key Exchange e controllo integrità
    - il protocollo di cifratura e controllo integrità
    - il protocollo di integrità dell'handshake
- server name: espandendone i dettagli, questa estensione passa prima per il Server Name Indication (SNI) extension, e poi per il campo Server Name dove è indicato il nome host del server HTTPS a cui ci si connette

---

Il pacchetto successivo da prendere in esame nell'analisi dell'handshake TLS è quello di tipo "Server Hello", che consiste nella risposta del server alla richiesta del client, dove il server risponde con la cipher suite scelta in base alle proposte inviate dal client nella fase "Client Hello", che consistono nei protocolli di Key Exchange, cifratura, e integrità handshake, con i relativi controlli di integrità.

---

Dopo "Client Hello" e "Server Hello", il pacchetto successivo da esaminare è quello relativo al certificato (identificato in Wireshark come Certificate), da cui, nei dettagli del pacchetto, va esplosa l'alberatura "Handshake Protocol: Certificate" -> Certificates dove è possibile vedere la catena di rilascio del certificato, oltre a contenere la chiave pubblica che il server invia al client.

In questa fase, il server manda al client l'informazione "Server Hello Done", ad indicare che sono stati inviati al client tutti i parametri che servono per iniziare la conversazione TLS

---

Ricevuto il "Server Hello" dal server web, il client HTTPS accetta (di norma) i parametri inviati dal server e invia la propria public key al server;
in questa fase, nel pacchetto si nota anche la dicitura "Change Cipher Spec", che sta a indicare che d'ora in avanti tutti i dati che il client invierà al server saranno criptati.

L'ultima cosa da evidenziare di questo pacchetto è la dicitura "Handshake Protocol: Finished", che indica il termine, da parte del client, della fase di TLS handshake

---

L'ultimo pacchetto della fase di TLS Handshake è del server, che, specularmente al client, invia il pacchetto "Change Cipher Spec", il quale indica che da ora in poi tutti i dati inviati al client saranno criptati in base ai parametri definiti nella fase di TLS handshake.

Anche dal server viene inviato un "Finished" verso il client, indicante il termine, da parte del server, della fase di TLS handshake.

---

Ciò che è descritto nelle note precedenti è possibile vederlo in dettaglio se torniamo alla fase precedente, in cui i dati TLS in Wireshark sono criptati, dove vediamo che nel pacchetto "Client Key Exchange, Change Cipher Spec, Encrypted Handshake Message", compare appunto "Encrypted Handshake Message" al posto di "Finished", proprio perché dopo tutto ciò che viene dopo "Change Cipher Spec" è criptato, passo necessario per fare sì che la fase di Exchange Key non sia visibile