# Working with Repositories

## Cloning from and Pushing to Github

dopo aver creato un repository su GitHub, lo si può clonare in modo trasparente tramite le chiavi SSH utilizzate in precedenza su un proprio repo locale, per poi copiare alcuni file sul repo locale, metterli in staging e quindi fare un commit locale, per poi terminare con un push sul repo Github.
Di seguito sono elencati i comandi:

- git clone git@github.com:lorenzorossi-1/nomeRepo.git | clono in locale il repository denominato nomeRepo
- git status | ottengo lo stato attuale del repo locale dopo aver copiato file e cartelle che mi interessano
- git commit -m "commento" | esegue un commit sul repo locale, con un commento ai file di cui si è fatto il commit
- git push origin main | esegue un push (cioé un commit remoto) sul repository associato nomeRepo, sul branch "origin main"
