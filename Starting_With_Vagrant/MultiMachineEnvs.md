# Multi-Machine Environments

## Istruzione config.vm.define in Vagrantfile con più di una VM

In Vagrant è possibile gestire più VM con un unico Vagrantfile, per le quali si può condividere la stessa box oppure si possono usare box diverse. In questo caso possiamo quindi indicare un'istruzione per definire una specifica VM, a cui indichiamo un "blocco" per poter indicare più istruzioni per questa specifica VM:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.define "WebServer" do |web|

  end
  
end
~~~

dove WebServer è l'identificativo assegnato alla VM (cosa diversa dall'hostname)

Se usiamo questa riga con una VM default già presente, questa non sarà visibile usando il comando "vagrant status", pertanto dovremo definire la nostra VM default con un identificativo aggiuntivo rispetto a quello indicato in precedenza:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  config.vm.define "WebServer" do |web|

  end
  
  config.vm.define "Default" do |default|

  end
  
end
~~~

Dove al posto di default possiamo usare un qualsiasi identificativo.

## Definire diversi OS per diverse VM in Vagrantfile

Con l'esempio precedente, entrambe le VM avrebbero come OS Ubuntu 22.04, ma se voglio specificare per una singola VM un altro OS, devo scrivere in modo diverso il mio Vagrantfile:

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  config.vm.define "WebServer" do |web|

  end
  
  config.vm.define "ReverseProxy" do |proxy|
    proxy.vm.box = "centos/7"
  end
  
end
~~~

## Definire reti private in Vagrantfile

In vagrant possiamo definire una rete virtuale privata per ogni VM, con un indirizzo IP sulla classe 192.168.56.0/24 (che è l'indirizzamento della rete privata predefinita in VirtualBox, in cui questo tipo di rete prende il nome di host-only), modificando in modo opportuno il nostro file Vagrantfile

~~~ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  config.vm.define "WebServer" do |web|
    web.vm.network "private_network", ip: "192.168.156.11"
  end
  
  config.vm.define "ReverseProxy" do |proxy|
    proxy.vm.box = "centos/7"
	proxy.vm.network "private_network", ip: "192.168.56.12"
  end
  
end
~~~

In questo modo, le due VM avranno sia la rete NAT per l'accesso ad Internet, sia una rete privata per fare sì che le due VM comunichino tra di loro