# TCP Analysis with Wireshark

## Visualizing TCP Data Flows and Reassembling Packets

- Nell'analisi delle ritrasmissioni (quindi retransmission, non fast retransmission), è interessante notare la differenza tra RTT (in Wireshark valore iRTT nella sezione [SEQ/ACK analysis]) e il valore RTO che rappresenta il Retransmission Time Out (sezione [SEQ/ACK analysis] -> [TCP Analysis Flags])
- Quando siamo nella situazione in cui abbiamo uno o più pacchetti marcati come "TCP Previous segment not captured" e pacchetti marcati come "TCP ACKed unseen segment" senza avere dei DUP ACKs o ritrasmissioni o pacchetti out-of-order, allora molto probabilmente siamo in presenza di pacchetti non catturati da Wireshark ma che sono correttamente arrivati a destinazione
- TCP Window Update indica che l'host ricevente ha aggiornato la propria window size, cioé il proprio buffer di ricezione, cambierà quindi la quantità di dati che potrà ricevere
- TCP Window Full informa che l'host ricevente, a seguito della trasmissione di un determinato pacchetto, riempirà il buffer di ricezione se il ricevente non elaborerà alcuni dei pacchetti già ricevuti in modo da liberare il buffer
- TCP Zero Window è quando l'host ricevente notifica che la propria window size è uguale a zero, e che quindi per un po' non potrà ricevere traffico
- Il pacchetto TCP Window Update è un pacchetto "speciale" che non è né invio di dati né un ACK, semplicemente è un avviso che è cambiato il valore di receive window, il cui campo in Wireshark prende il nome di "Calculated Window size"