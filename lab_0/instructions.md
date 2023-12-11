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


## Installation et Configuration de Terraform

**Objectif :** Installer Terraform et configurer les variables d'environnement.

1. **Installation de Terraform**

Assurez-vous que vous avez Terraform téléchargé depuis le site officiel : [https://developer.hashicorp.com/terraform/install](https://developer.hashicorp.com/terraform/install)

Sur un système Linux, vous pouvez utiliser les commandes suivantes pour télécharger la dernière version stable actuelle `1.6.5` et installer Terraform :

```bash
wget https://releases.hashicorp.com/terraform/1.6.5/terraform_1.6.5_linux_amd64.zip
unzip terraform_1.6.5_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```

2. **Vérification de l'Installation**

Vérifiez si Terraform est correctement installé en exécutant la commande suivante :

```bash
terraform --version
```

Si le système ne trouve pas l'exécutable, veuillez ajouter le chemin d'exécution de Terraform à la variable d'environnement `PATH`. Éditez le fichier de profil (par exemple, `~/.bashrc` sur Linux ou `~/.zshrc` sur Mac) et ajoutez la ligne suivante :

```bash
export PATH=$PATH:/usr/local/bin/
```

Chargez les nouvelles configurations du fichier de profil :

```bash
source ~/.bashrc
```

Vérifiez à nouveau `terraform --version`

Vous devriez voir la version de Terraform que vous venez d'installer.


## Installation et Configuration de l'environnement client AWS

1. **Configuration des Credential et installation de AWS CLI**

Si vous avez un fournisseur de cloud spécifique que vous prévoyez d'utiliser avec Terraform (par exemple, AWS, Azure, Google Cloud), configurez les informations d'identification.

Dans le cadre de Lab, nous allons exploiter le Cloud AWS.

Pour AWS, vous pouvez configurer les variables d'environnement `AWS_ACCESS_KEY_ID` et `AWS_SECRET_ACCESS_KEY`.

```bash
export AWS_ACCESS_KEY_ID="your-access-key-id"
export AWS_SECRET_ACCESS_KEY="your-secret-access-key"
```

Installer [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions)

```bash 
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Assurez-vous de stocker ces informations d'identification de manière sécurisée.

Configurez aws cli

```bash
aws configure
```
