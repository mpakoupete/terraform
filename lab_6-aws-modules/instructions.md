# Lab 6: Utilisation des modules

**Objectif :** Créer un module Terraform réutilisable pour déployer des instances EC2 avec des configurations spécifiques.

les modules aident les développeurs à réutiliser le code TF dans leurs projets sans avoir à réécrire les ressources TF.
Les modules sont un moyen d'empaqueter et de réutiliser le code commun dans le projet.

_Par exemple, supposons que vous ayez une conception de réseau standard ou une conception des instances EC2 standard pour vos comptes et projets AWS. Au lieu d'écrire les définitions de ressources longues dans chaque projet, vous pouvez créer un module de mise en réseau ou un module d'instcance EC2 qui standardise votre déploiement. Lorsqu'un nouveau projet démarre, vous pouvez importer cette conception de réseau dans le projet avec peu d'efforts et poursuivre votre route._

1. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_6` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_6
   cd lab_6
   ```

2. **Création d'un Module Terraform pour les Instances EC2**

   Dans le sous-répertoire `ec2`, créez un fichier `main.tf` avec le code suivant :

   ```hcl
    variable "mon_instance_ami" {
     description = "L'ID de l'AMI pour l'instance EC2"
   }

   variable "mon_instance_type" {
     description = "Le type d'instance EC2 à déployer"
   }

   variable "instance_tags" {
     description = "Les tags à appliquer à l'instance EC2"
     type        = map(string)
   }

   resource "aws_instance" "example" {
     ami             = var.mon_instance_ami
     instance_type   = var.mon_instance_type
     key_name        = var.instance_tags["Name"]  # le nom de la clé = nom (ex: jean)
     subnet_id       = var.instance_tags["SubnetID"]  # Utilisation d'un tag pour l'ID du sous-réseau
     security_groups = [var.instance_tags["SecurityGroup"]]  # Utilisation d'un tag pour le groupe de sécurité
     tags            = var.instance_tags  # Application de tous les tags spécifiés
   }
   ```

Ce module Terraform définit une ressource AWS EC2 et expose trois variables `mon_instance_type`, `mon_instance_ami`, et une variable de Map de tring `instance_tags` pour permettre la personnalisation lors de son utilisation dans un fichier Terraform principal.


3. **Utilisation du Module dans le Fichier Principal**

   Dans le répertoire principal, mettez à jour le fichier `main.tf` avec le code suivant :

   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   module "ec2_instance" {
     source        = "./ec2"
     mon_instance_ami =  "ami-0230bd60aa48260c6"  # AMI de Amazon Linux 2
     mon_instance_type = "t2.micro"
     instance_tags = {
       Name          = "jean"   # Mettez votre nom ici
       SubnetID      = "subnet-625cb863"  # Fournissez un subnet Id existant
       SecurityGroup = "sg-eb8f65fa"  # Fournissez un Security Group Id existant
     }
   }
   ```

   Assurez-vous de remplacer les valeurs comme l'ID du sous-réseau (`subnet-12345678`) et l'ID du groupe de sécurité (`sg-87654321`) par des valeurs appropriées de votre infrastructure.

   Ne détruisez pas cette infrastructure. Nous allons l'utiliser au prochain Lab
