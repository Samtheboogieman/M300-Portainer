# **Dokumentation LB2**

---

# Inhaltsverzeichnis
  

- [**Dokumentation LB2**](#dokumentation-lb2)
- [Inhaltsverzeichnis](#inhaltsverzeichnis)
- [Einführung](#einführung)
- [Grafische Übersicht des Portainer Services](#grafische-übersicht-des-portainer-services)
- [Code](#code)
	- [Code-Quelle](#code-quelle)
	- [Vagrantfile](#vagrantfile)
		- [Vagrant Config](#vagrant-config)
		- [Get Tools](#get-tools)
		- [Verifikation des Dockerimages (Optional, nicht in Vagrantfile enthalten)](#verifikation-des-dockerimages-optional-nicht-in-vagrantfile-enthalten)
		- [Install Docker](#install-docker)
		- [Install Portainer](#install-portainer)
		- [Vagrant Config Ende](#vagrant-config-ende)
- [Code Beschreibung](#code-beschreibung)
- [Ergebniss](#ergebniss)

---

# Einführung

Ich habe mich für das Projekt **Ubuntu-Server mit Docker und Portainer** entschieden.

Mein Projekt umfasst die Virtualisierung und Automatisierung die Einrichtung von Ubuntu zum 
Ausführen von Docker. Mit Hilfe von Docker können wir Portainer (Docker-Image)automatisiert starten. Doch darauf werde ich im Code noch genauer eingehen. 
Die Idee ist es schnell eine Portainer umgebung aufzuseten und direkt loslegen können. Mit meinem Vagrantfile geht dies bei einer erstinstallation ungefähr 5min. 

---
<a name="grafische"></a>
# Grafische Übersicht des Portainer Services

![Test](https://github.com/Samtheboogieman/M300-Portainer/blob/master/LB2/images/vagrant.png)

---

# Code

## Code-Quelle

Ich habe die Offizielle Dokumentation von Docker und Portainer verwendet. Daraus konnte ich die nötigen Commands nehmen, um diesen Prozess zu automatisieren.

https://docs.docker.com/engine/install/ubuntu/ 

https://docs.portainer.io/v/ce-2.11/start/install/server/docker/linux

Es folgt eine genau beschreibung meiner Files:

## Vagrantfile


Mein Vagrantfile sieht folgendermassen aus:

### Vagrant Config

	Vagrant.configure(2) do |config|
		config.vm.box = "ubuntu/bionic64"
		config.vm.network "forwarded_port", guest:9443, host:9443, auto_correct: false  
	config.vm.provider "virtualbox" do |vb|
		vb.memory = "1024"
	end
	config.vm.provision "shell", inline: <<-SHELL

### Get Tools

	sudo apt-get update

	sudo apt-get install \
		ca-certificates \
		curl \
		gnupg \
		lsb-release
### Verifikation des Dockerimages (Optional, nicht in Vagrantfile enthalten)
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

	echo \
	"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
	$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

### Install Docker

	sudo apt-get update
	sudo apt-get install docker-ce docker-ce-cli containerd.io -y

### Install Portainer

	docker volume create portainer_data

	docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
		--restart=always \
		-v /var/run/docker.sock:/var/run/docker.sock \
		-v portainer_data:/data \
		portainer/portainer-ce:2.11.1
### Vagrant Config Ende	
	SHELL
	end


---

# Code Beschreibung

| Code| Beschreibung|
| --------------| -----------------|
| Vagrant.configure("2") do  | Diese Zeile im Code beschreibt die API Version. |
| config.vm.box | Dies linie definiert welches Betriebsystem verwendet werden soll. |
| db.vm.network | Hier wird eine Portweiterleitung und alle weitere Einstellungen bezüglich der Netzwerkverbindung konfiguriert. |
| db.vm.provision | Hier wird die Ausführung von einem Shell Skript erlaubt oder verbotet, wobei lesteres in unserem Umfang keinen Sinn ergiebt. |
| config.vm.provider :virtualbox do | Hier entscheiden wir uns für einen Hypervisor der unser VM beherbergt. Zudem können wir noch Ressourcen zuteilen wie CPU oder RAM. |
| sudo apt-get update | In diesem Schrtt aktualisieren wir unser lokales Repositry (dieser Schritt soll öfters gemacht werden um die reibungslose installaton zu gewährleisten|
| sudo apt-get install  ca-certificates  curl gnupg lsb-release | In diesem Schritt installieren wir die nötigen Tools für unsere weiteren Commands|
| sudo apt-get install docker-ce docker-ce-cli containerd.io -y e | Mit diesem Command installieren wir docker vom containerd.io Repository |
|docker volume create portainer_data | Das ist unser erster Docker command. Wir erstellen ein lokales Verzeihniss das docker für die container benutzen kann|
| docker run -d -p 8000:8000 -p 9443:9443 --name portainer|Hier wird der Container erstellt, zu beachten gilt es das die Ports gleich wie bei Vagrant weitergeleitet  werden. Dabei ist die zweite Portnummer der Hostport. |
| --restart=always | Hiermit garantieren wir wenn die VM gestartet wird wird auch der Container gestartet|
| -v /var/run/docker.sock:/var/run/docker.sock | Speicherort für Config dateien die später in Portainer angelegt werden|
| -v portainer_data:/data \ | Speicherort für Daten und Imagedateien die später in Portainer angelegt werden|
| portainer/portainer-ce:2.11.1 | Aus welchem Repository wird das Image für Portainer heruntergeladen. Nach dem doppelpunkt lässt sich noch eine version angeben. Aus stabilitätsgründen lohnt es sich hier nachzuschauen und nicht einfach :latest zu nehmen.|

# Ergebniss

