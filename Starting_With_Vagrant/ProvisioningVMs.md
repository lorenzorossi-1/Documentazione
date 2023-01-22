# Provisioning VMs

## Copiare file dall'host al guest con vagrant

Per caricare dei file dall'host al guest senza usare le synced folder (la nomenclatura di vagrant usata per definire quelle che in VirtualBox si chiamano Shared Folders) possiamo usare il comando

~~~bash
vagrant upload nomefile.txt nomeVM
~~~

dove per nomeVM si indica il nome della VM, che si può omettere se nella directory è stata definita una sola VM

## Lanciare uno script definito nel file Vagrantfile

Se voglio fare partire uno o più script bash all'avvio di una VM Linux per effettuare l'installazione di programmi o la configurazione di alcune impostazioni (pratica che prende il nome di provisioning) posso aggiungere una riga al mio Vagrantfile come indicato di seguito

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.hostname = "webserver"
  
  config.vm.provider "virtualbox" do |vb|
    vb.name = "WebServer"
	vb.memory = 2048
    vb.cpus = 2
  end
  
  config.vm.provision "shell", path: "script/install_packages.sh"
end
~~~

dove in particolare mi interessa la penultima riga, in cui "shell" indica che la VM eseguirà uno script bash, mentre "script/install_packages.sh" è il percorso relativo rispetto alla directory in cui è presente il Vagrantfile sull'host

É possibile assegnare un nome allo script di cui fare il provisioning, come mostrato di seguito (riga contenuta nel file Vagrantfile):

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provision "shell", name: "packages", path: "script/install_packages.sh"
end
~~~

questo nome serve perchè può essere richiamato dalla riga di comando di vagrant (utile in caso abbiamo più script e vogliamo lanciarne solamente uno a VM accesa):

~~~bash
vagrant provision --provision-with packages
~~~

## Copiare file dall'host al guest tramite Vagrantfile

Un altro tipo di provisioning riguarda i file, per cui con un'opportuna istruzione nel file Vagrantfile è possibile copiare uno o più file dall'host al guest in modo riproducibile

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provision "file", source: "file/test.txt", destination: "$HOME/"
end
~~~

tenere presente che tramite il provisioning di tipo file non è possibile copiare file con i privilegi di root, pertanto se dobbiamo copiare file in directory protette, dovremo prima copiarli ad esempio nella home directory e quindi, tramite uno script, copiare o spostare i file dalla home dir alla directory protetta

## Lanciare comandi bash sulla VM tramite Vagrantfile

Tramite il provisioning è possibile non solo lanciare script, ma anche lanciare comandi della shell con l'opzione inline:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provision "shell", name: "testCommand", inline: "echo ciao mondo"
end
~~~

Allo stesso modo è possibile avere più comandi nella stessa riga usando l'espediente chiamato HEREDOCS:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provision "shell", name: "testCommand", inline:<<-PROVA
    echo ciao mondo
    echo mondo ciao
  PROVA
end
~~~

Se abbiamo necessità di eseguire uno script o un comando non come root ma come utente vagrant (ad esempio), lo possiamo specificare tramite l'opzione privileged nell'istruzione provision del file Vagrantfile:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provision "shell", name: "testCommand", privileged: false, inline:<<-PROVA
    echo ciao mondo
    echo mondo ciao
  PROVA
end
~~~

## Considerazioni sull'avvio del provisioning

Tenere presente che le istruzioni di provisioning del file Vagrantfile vengono eseguite solo alla prima accensione della VM o comunque dopo un destroy della VM, a meno che non venga lanciato il provisioning con il comando

~~~bash
vagrant provisioning
~~~

oppure

~~~bash
vagrant provisioning --provision-with nomeProvisioning
~~~

Un'altra opzione può essere quella di aggiungere un parametro all'istruzione di provisioning nel file Vagrantfile in modo da eseguire il provisioning ad ogni avvio della VM, ad esempio

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provision "shell", name: "testCommand", run: "always", inline: "echo ciao mondo"
end
~~~