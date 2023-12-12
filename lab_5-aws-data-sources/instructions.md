# Lab 5: Utilisation des data source

Jusque là nous avons manipuler :
* les Block de type : Provider, Ressource, variable.
* les variables Input, Output
* l'utilisation des boucle foreach, count
* un peu les états locaux de Terraformer

**Objectif :** Introduire l'utilisation de [Data Source (Source de donnée)](https://developer.hashicorp.com/terraform/language/data-sources) Terraform.

_Les sources de données permettent à Terraform d'utiliser des informations définies en dehors de Terraform, définies par une autre configuration Terraform distincte ou modifiées par des fonctions._

Pour toutes les instances on choisira :
* l'AMI de AMAZON LInux : `ami-0230bd60aa48260c6` 
* type d'instance `t2.nano`
* La paire de clé SSH `prenom-key` (e.g. `jean-key` pour l'utilisateur Jean DUPOND) que vous devez créer dans la console

1. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_5` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_5
   cd lab_5
   ```
2. Création d'un VPC: l'objectif est de créer un nouveau VPC([voir Doc](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc)) et un nouveau sous-réseau ([Voir](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/subnet)). Créez un fichier de configuration Terraform, par exemple `main.tf`, avec les indications suivantes :
* Bloc CIDR du VPC : 10.0.0.0/16
* Ajouter un Tag au VPC : votre prenom
* Configuration du sous-réseau
* Bloc CIDR du Sous réseaux : 10.0.1.0/24

Si vous faites face à une erreur lié à la création de VPC; il peut s'agir de la limite de VPC atteinte dans une région. Faites moi signe pour que je vous assigne une région

Pas de correction pour cette fois ci non ? ;) on fait travailler un peu les méninges.


3. Configuration complexe de EC2. C'est là l'utilité de `data source`. On y va ?
   Votre tâche consiste à créer une instance EC2 qui utilise la configuration suivante :
   
* AMI : Obtenir la dernière AMI Ubuntu Linux ID (il doit s'agir d'Amazon Linux) => Voir doc de [aws_ami](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/ami)
* Type d'instance : `t2.micro`
* VPC dans lequel il sera lancé : le VPC et le sous-réseau que vous avez créés précédement :D
* Inclure l'output qui va afficher l'ID de cette AMI


<details><summary>Correction</summary>

```hcl


provider "aws" {
  region  = "us-east-1"
}

# Création du VPC

resource "aws_vpc" "jean" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "jean"
  }
}

# Creation d'un sous réseaux privé dans le VPC précédent

resource "aws_subnet" "private" {
  vpc_id = aws_vpc.jean.id
  cidr_block = "10.0.1.0/24"
}

# La partie la plus délicate. En fait il faut se référer à la doc pour mieux comprendre chaque data source en particulier aws_ami

data "aws_ami" "ubuntu" {

  most_recent = true            # le plus récent

  owners      = ["099720109477"]    # ID du compte AWS de Canonical qui fournit les images Ubuntu. Ensuite on filtre

   filter {
      name   = "name"
      values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
    }
  filter {
      name   = "virtualization-type"
      values = ["hvm"]
    }
  filter {
      name   = "root-device-type"
      values = ["ebs"]
    }
  }

output "latest_ubuntu_ami_id" {
  value = data.aws_ami.ubuntu.id
}


resource "aws_instance" "app" {
  ami           = data.aws_ami.ubuntu.id        # On importe la valeur de l'ami obtenur depuis la data source
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.private.id
}
```

</details>


On saisit mieux l'utilité des `data source` ?
Simple: 


Supprimer les ressources
Après avoir terminé ce laboratoire, exécutez terraform destroy pour supprimer toutes les ressources que vous avez créées.