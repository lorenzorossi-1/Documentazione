# Sincronizzazione di Directory tra Server Linux con rsync e SSH

Questa guida descrive come configurare e utilizzare `rsync` tramite SSH per sincronizzare directory tra due server Linux, denominati `SrcServer` (sorgente) e `DstServer` (destinazione).

## Configurazione Iniziale

### 1. Generazione delle Chiavi SSH

Il primo passo consiste nel generare una coppia di chiavi SSH sul server sorgente per stabilire una connessione sicura e automatizzata:

```bash
ssh-keygen -t rsa -b 4096 -C "SrcServer"
```

Questo comando genera:
- Una chiave privata (`~/.ssh/id_rsa`)
- Una chiave pubblica (`~/.ssh/id_rsa.pub`)

La chiave pubblica dovrà essere copiata sul server di destinazione nel file `~/.ssh/authorized_keys` dell'utente target, operazione che può essere automatizzata tramite il comando:

```bash
ssh-copy-id username@server-ip
```

### 2. Configurazione SSH Client

Per semplificare le connessioni successive, è consigliabile creare un file di configurazione SSH:

```bash
touch ~/.ssh/config
```

Il contenuto del file `~/.ssh/config` deve includere:

```
Host DstServer
        HostName 172.16.1.2
        User utente
        IdentityFile ~/.ssh/id_rsa
        Port 22
```

Questa configurazione permette di utilizzare semplicemente `ssh DstServer` invece di specificare ogni volta tutti i parametri di connessione.

### 3. Test della Connessione

Prima di procedere con la sincronizzazione, verificare che la connessione SSH funzioni correttamente:

```bash
ssh DstServer
```

Se la configurazione è corretta, dovrebbe essere possibile connettersi senza inserire password.

## Comandi di Sincronizzazione

### Sincronizzazione Base

Il comando base per sincronizzare la directory `~/Documents/Dati/` dal server sorgente al server di destinazione è:

```bash
rsync -avz --log-file=~/rsync.log -e ssh ~/Documents/Dati/ DstServer:/home/utente/Dati/
```

**Parametri utilizzati:**
- `-a`: modalità archivio (preserva permessi, timestamp, link simbolici, ecc.)
- `-v`: modalità verbosa (mostra i file trasferiti)
- `-z`: compressione durante il trasferimento
- `--log-file=~/rsync.log`: registra l'attività in un file di log
- `-e ssh`: utilizza SSH come protocollo di trasporto

### Sincronizzazione con Eliminazione

Per mantenere la directory di destinazione identica a quella sorgente, eliminando anche i file che sono stati rimossi dalla sorgente:

```bash
rsync -avz --log-file=~/rsync.log --delete -e ssh ~/Documents/Dati/ DstServer:/home/utente/Dati/
```

Il parametro `--delete` rimuove dalla destinazione i file che non esistono più nella directory sorgente.

## Note Importanti

- **Attenzione al trailing slash**: `~/Documents/Dati/` (con slash finale) sincronizza il contenuto della directory, mentre `~/Documents/Dati` (senza slash) sincronizza la directory stessa
- **Backup preventivo**: prima di utilizzare `--delete`, assicurarsi di avere un backup della destinazione
- **Monitoraggio**: il file di log `~/rsync.log` consente di monitorare l'attività e identificare eventuali problemi
- **Automazione**: questi comandi possono essere facilmente integrati in script o cron job per automatizzare il processo di sincronizzazione

## Troubleshooting

In caso di problemi, verificare:
1. La connettività SSH (`ssh DstServer`)
2. I permessi delle directory coinvolte
3. Lo spazio disponibile sul server di destinazione
4. Il contenuto del file di log per errori specifici

## Trasparenza

Questo documento è stato generato tramite Claude su mie precise indicazioni e con una revisione finale del documento.