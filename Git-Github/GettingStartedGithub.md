# Getting started with Github

## Working with SSH

è possibile utilizzare ssh per poter lavorare con i repo su GitHub, per farlo, creare una coppia di chiavi in locale, quindi:

- andare sul proprio accunt Github
- andare nella sezione Setting -> SSH and GPG keys -> SSH keys
- cliccare su New SSH Key
- aprire con un editor di test il file C:\Users\%username%\.ssh\id_rsa.pub (chiave pubblica) e copiare il contenuto
- incollare il contenuto nella casella di testo relativa alla SSH key nella relativa sezione su Github
- testare nel client bash Git il funzionamento con il comando "ssh -T git@github.com"