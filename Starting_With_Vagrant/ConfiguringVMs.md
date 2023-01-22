# Configuring VM Resources

## Creazione file Vagrantfile

Quando si usa il comando 

~~~bash
vagrant init nomebox
~~~

viene creato, nella cartella di partenza, un file chiamato Vagrantfile, che contiene le impostazioni della VM, impostazioni che verranno applicate all'avvio della VM con il comando vagrant up

mentre se uso il comando

~~~bash
vagrant init --minimal nomebox
~~~

viene sempre creato lo stesso file Vagrantfile ma senza quei commenti che "allungano" il contenuto del file

## Specificare RAM e vCPU nel file Vagrantfile

Dato un file Vagrantfile di partenza come il seguente

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
end
~~~

e voglio aggiungere caratteristiche quali le vCPU da usare e la RAM da allocare per la VM in questione, il file Vagrantfile sarà

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end
end
~~~

Se le impostazioni indicate sopra le applichiamo con la VM accesa, non verranno applicate fino alla riaccensione della VM, pertanto possiamo procedere al reboot con il comando

~~~bash
vagrant reload
~~~

## Specificare port forwarding nel file Vagrantfile

In caso voglia effettuare il port forwarding di una porta dell'host verso un'altra porta del guest (ad esempio dalla porta 8080 dell'host alla porta 80 del guest) devo, anche in questo caso, mettere mano alla configurazione contenuta nel file Vagrantfile:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.network "forwarded_port", guest: 80, host: 8080
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end
end
~~~

Per conoscere le varie opzioni di configurazione delle VM gestite con Vagrant, è possibile consultare il sito della documentazione di Vagrant all'URL <https://developer.hashicorp.com/vagrant/docs> e scegliere la sezione Vagrantfile

## Validazione della sintassi del file Vagrantfile

É possibile fare una verifica della sintassi del file Vagrantfile tramite il comando

~~~bash
vagrant validate
~~~

il cui esito ci dirà se il file è sintatticamente corretto oppure no.

## Nome e hostname delle VM nel file Vagrantfile

Possiamo assegnare un nome alla VM di virtualbox associata alla box tramite un cambio al file Vagrantfile, che diventerà:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  
  config.vm.network "forwarded_port", guest: 80, host: 8080
  
  config.vm.provider "virtualbox" do |vb|
    vb.name = "WebServer"
	vb.memory = 2048
    vb.cpus = 2
  end
end
~~~

Possiamo indicare un hostname alla VM al posto di quello predefinito aggiungendo una riga al file Vagrantfile, che diventerà quindi come il seguente:

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
end
~~~

## Autocompletamento di vagrant

Per configurare l'autocompletamento dei comandi di vagrant in un host Linux con bash possiamo digitare il comando

~~~bash
vagrant autocomplete install --bash
~~~
