# Exchanging the secret key

Quando un client fa una richiesta HTTPS a un server, questo risponde con un certificato, che oltre ad altri scopi, serve anche per iniziare lo scambio della chiave segreta (exchange secret key) che servirà per criptare e decriptare i dati in transito in maniera sicura.

---

Data la nota sopra, osservando il funzionamento del metodo Diffie-Hellman (and Merkle) (DH) il server invia al client due numeri che sono visibili pubblicamente, P e G, che sono entrambi numeri primi

---

Date le note sopra, il passo successivo coinvolge sia client sia server: entrambi definiscono delle chiavi private, rappresentate dai numeri A e B, che non sono disponibili pubblicamente

- A = client
- B = server

---

A questo punto, client e server creano la propria chiave pubblica tramite formule matematiche

- client: G elevato alla A Modulus P = chiave pubblica(a)
- server: G elevato alla B Modulus P = chiave pubblica(b)

---

Arrivati qui, se ho capito bene, client e server si scambiano le chiavi pubbliche, dopodiché viene calcolata la chiave con un'altra formula matematica

- client: chiave pubblica(b) elevato alla A Modulus P = chiave privata
- server: chiave pubblica(a) elevato alla B Modulus P = chiave privata

---

A seconda delle versioni di TLS, è possibile usare diverse metodologie di scambio di chiavi di crittografia

- TLS 1.2
	- RSA (deprecato)
	- Diffie Hellman (deprecato)
	- Elliptical Curve Diffie Hellman
- TLS 1.3
	- Elliptical Curve Diffie Hellman
