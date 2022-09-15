# An Overview of Git and GitHub

## Setting up your environment

Comandi basilari di configurazione globale di git:

- git config --global user.name "Lorenzo Rossi" | imposto il mio nome
- git config --global user.email "indirizzo@dominioposta.test" | imposto il mio indirizzo di posta elettronica
- git config --edit --global | edito il file di configurazione dei parametri globagli nell'editor predefinito impostato in fase di installazione

## Foundations of Git

i file sorgenti in git possono assumere tre stati:

- committed
- modified
- staged

---

comandi usati per gestire i file sorgenti e i repository:

- git init | inizializzazione di un repo locale
- git clone | download di un repo remoto (es. GitHub)
- git add | aggiungere uno o più file all'area staging
- git commit | fare il commit di uno o più file verso il repo locale

## Getting started with Git

comandi usati per gestire gli stati dei file in git, posizionandosi all'interno della cartella in cui abbiamo i nostri sorgenti:

- git init | inizializzazione del repo della cartella corrente. mostrando il branch principale (master)
- git status | mostra lo stato del repo, i file da aggiungere allo staging ed eventualmente i file di cui dover fare il commit
- git add lib/ | aggiunge la cartella lib/ all'area di staging, stessa cosa per altri file o cartelle
- git commit -m "commento" | esegue il commit nel repo locale, tra virgolette un commento che indica l'oggetto del commit
- git log | mostra la history dei commit
- git commit -am "altro commento" | esegue un nuovo commit, la nuova opzione 'a' potrebbe stare per append