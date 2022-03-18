# **Dokumentation LB2**

---

# Inhaltsverzeichnis

- [**Dokumentation LB2**](#dokumentation-lb2)
- [Inhaltsverzeichnis](#inhaltsverzeichnis)
- [Einführung](#einführung)
- [Grafische Übersicht des Services](#grafische-übersicht-des-services)
- [Code](#code)
	- [Code-Quelle](#code-quelle)
	- [Vagrantfile](#vagrantfile)

---

# Einführung

Ich habe mich für das Projekt **Ubuntu-Server mit Docker und Portainer** entschieden.
Mein Projekt umfasst die Virtualisierung und Automatisierung die Einrichtung von Ubuntu zum 
Ausführen von Docker. Mit Hilfe von Docker können wir Portainer (Docker-Image)automatisiert starten. Doch darauf werde ich im Code noch genauer eingehen. 
Die Idee ist es schnell eine Portainer umgebung aufzuseten und direkt loslegen können. Mit meinem Vagrantfile geht dies bei einer erstinstallation ungefähr 5min. 

---
<a name="grafische"></a>
# Grafische Übersicht des Services

---

# Code

## Code-Quelle

Ich habe die Offizielle Dokumentation von Docker und Portainer verwendet. Daraus konnte ich die nötigen Commands nehmen, um diesen Prozess zu automatisieren. 
https://docs.docker.com/engine/install/ubuntu/
https://docs.portainer.io/v/ce-2.11/start/install/server/docker/linux

Es folgt eine genau beschreibung meiner Files:

## Vagrantfile

Mein Vagrantfile sieht folgendermassen aus:


	Vagrant.configure(2) do |config|
		config.vm.box = "ubuntu/bionic64"
		config.vm.network "forwarded_port", guest:9443, host:9555, auto_correct: false  
	config.vm.provider "virtualbox" do |vb|
		vb.memory = "1024"
	end
	config.vm.provision "shell", inline: <<-SHELL
	#Get Docker Image

	sudo apt-get update

	sudo apt-get install \
		ca-certificates \
		curl \
		gnupg \
		lsb-release

	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

	echo \
	"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
	$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

	#Install Docker

	sudo apt-get update
	sudo apt-get install docker-ce docker-ce-cli containerd.io -y

	#Install Portainer

	docker volume create portainer_data

	docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
		--restart=always \
		-v /var/run/docker.sock:/var/run/docker.sock \
		-v portainer_data:/data \
		portainer/portainer-ce:2.11.1
	
	SHELL
	end




| Code| Beschreibung|
| --------------| -----------------|
| Vagrant.configure("2") do |config| | Diese Zeile im Code beschreibt die API Version. |
| config.vm.box | Dies linie definiert welches Betriebsystem verwendet werden soll. |
| db.vm.network | Hier wird eine Portweiterleitung und alle weitere Einstellungen bezüglich der Netzwerkverbindung konfiguriert. |
| db.vm.provision | Hier wird die Ausführung von einem Shell Skript erlaubt oder verbotet, wobei lesteres in unserem Umfang keinen Sinn ergiebt. |
| config.vm.provider :virtualbox do |vb| | Hier entscheiden wir uns für einen Hypervisor der unser VM beherbergt. Zudem können wir noch Ressourcen zuteilen. |


