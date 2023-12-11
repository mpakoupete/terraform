# Mise en place de l'environment de Lab

## Installation OpenStack

Pour ce cours sur Terraform pour Openstack, nous allons utiliser la version lancer 1 VM [DevStack](https://docs.openstack.org/devstack/latest/) (_DevStack est un projet open source qui permet de déployer un environnement de développement complet pour OpenStack_) dont les caractéristiques sont les suivantes :
* 6 VCPUs
* 12 GB Ram

### Step 1 : Assurez-vous que ces 2 logiciels sont installés :

* [Vagrant](https://www.vagrantup.com/downloads)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

Sur notre machine Lab, elle le sont déjà ;)

### Step 2 : Télécharger le fichier Vagrantfile contenu dans le répertoire 

* Sur la machine de Lab placez vous dans votre répertoire courant
* Ajoutez la ligne `* 0.0.0.0/0 ::/0` dans le fichier `/etc/vbox/networks.conf`. S'il n'existe pas, créez le.
* Créer un répertoire `setup`
* Télécharger le fichier `Vagrantfile` contenu dans le répertoire `lab_files` du présent repo Git et le mettre dans `setup`
* Placez-vous dans le répertoire `setup` où se trouve le fichier Vagrantfile
* Ensuite, démarrez l'exécution avec la commande vagrant `$ vagran up` => La mise en place de l'environnement OpenStack se fera automatiquement et prendra environ 20min

### Installation et configuration d'un éditeur de text
   
Pour l'édition des fichiers manifest Terraform nous utiliserons un éditeur de text. Installez l'éditeur de text de votre choix et les plugins qui permettent de faciliter l'édition des fichiers Terraform.

Installation de [Visual Studio Code sur Linux RHEL, Fedora, et CentOS distributions](https://code.visualstudio.com/docs/setup/linux#_rhel-fedora-and-centos-based-distributions)

Installer les plugins suivants :
* Github Theme
* HashiCorp Terraform
* Vagrant
* vscode-icons