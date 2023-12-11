# Lab 2: Variables et Output avec Terraform**

**Objectif :** Introduire l'utilisation de variables Terraform et les sorties (outputs) pour rendre le code plus flexible.

1. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_3` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_3
   cd lab_3
   ```

1. **Création d'un Fichier Terraform avec des Variables**

   Nous allons améliorer notre code Terraform préalable en introduisant la notion de variable `Input` et `Output`:
   
   Créez un fichier Terraform nommé `main.tf` dans votre répertoire. à partir du code précédent introduisez les 4 variables Input suivantes :
   
   * Variable Input `aws_region` dont la valeur par défaut est `"us-east-1"` et une `description de votre choix`
   * Variable Input `instance_ami` dont la valeur par défaut est `"ami-0230bd60aa48260c6"`, l'AMI de Amazon Linux 2 et une `description de votre choix`
   * Variable Input `instance_type` dont la valeur par défaut est `"t2.nano"`, le type d'instance que nous souhaitons et une `description de votre choix`
   * Variable Input `tag_Name`  dont la valeur par défaut est celle donnée précédemment (Ex : `"jean_d"` pour Jean DUPON) et une `description de votre choix`

   <details><summary>Correction</summary>
   
   Utilisez l'éditeur de texte pour ajouter le code Terraform suivant :

   ```hcl
   variable "aws_region" {
     default     = "us-east-1"
     description = "La région AWS dans laquelle les ressources seront déployées"
   }

   variable "instance_ami" {
     default     = "ami-0230bd60aa48260c6"  # AMI de Amazon Linux 2
     description = "L'ID de l'AMI pour l'instance EC2"
   }

   variable "tag_Name" {
     default     = "jean_d"                     # le Tag de notre instance
     description = "le nom donnée à notre instance"
   }

   variable "instance_type" {
     default     = "t2.nano"  # Type de l'instance
     description = "Le type d'instance EC2 à déployer"
   }

   provider "aws" {
     region = var.aws_region
   }

   resource "aws_instance" "example" {
     ami           = var.instance_ami
     instance_type = var.instance_type

     tags = {
         Name = var.tag_Name
      }

   }

   ```

   Ce code utilise des variables pour la région AWS, l'ID de l'AMI, le type d'instance, le Tag Name.

   </details>

2. **Initialisation du Répertoire Terraform**

   Exécutez la commande suivante pour initialiser le répertoire Terraform :

   ```bash
   terraform init
   ```

3. **Vérification du Plan Terraform avec des Variables**

   Exécutez la commande suivante pour voir le plan Terraform en utilisant les valeurs par défaut des variables :

   ```bash
   terraform plan
   ```

   Vérifiez que la sortie du plan est conforme à vos attentes.

4. **Utilisation de Fichiers de Variables Terraform (variables.tfvars)**

   Créez un fichier nommé `variables.tfvars` pour définir des valeurs spécifiques aux variables qui seront différentes des variables par défaut.
   
   Utilisez `"us-east-2"` et `"t2.micro"`

   <details><summary>Correction</summary>

   ```hcl
   aws_region     = "us-east-2"
   instance_type  = "t2.micro"
   ```

   </details>


5. **Application des Changements Terraform avec des Variables Définies**

   Exécutez la commande suivante pour appliquer les changements en utilisant les valeurs du fichier `variables.tfvars` :

   <details><summary>Correction</summary>

   ```bash
   terraform plan -var-file=variables.tfvars
   terraform apply -var-file=variables.tfvars
   ```

   </details>


   Confirmez en entrant "yes" lorsque vous y êtes invité.

   Vous pouvez utiliser l'argument `--auto-approve` de Terraform pour ne plus avoir à entrer `yes` en mode interractif.

   Pourquoi selon vous vous rencontrez une erreur ?

   <details><summary>Correction</summary>

   Lorsque vous rencontrez une erreur après le `Terraform plan`, c'est en général lié aux ressources Cloud. En lisant l'erreur ça nous donne une indication de ce que le problème peut être

   ```error
    Error: creating EC2 Instance: InvalidAMIID.NotFound: The image id '[ami-0230bd60aa48260c6]' does not exist
        status code: 400, request id: 83bdc13e-c0bd-471f-9f41-f84bcb159c67
   ```

   En effet cette image AMI `ami-0230bd60aa48260c6` est presente dans la région `us-east-1` mais pas dans la région `us-east-2`. De ce fait nous allons le remplacer par l'Id de l'AMI suivante : `ami-06d4b7182ac3480fa`

   </details>

   Apportez les corrections nécessaires et réexécuter le plan.


   <details><summary>Correction</summary>

   Dans le fichier de variable, ajouter la variable 

   ```hcl
   aws_region     = "us-east-2"
   instance_type  = "t2.micro"
   instance_ami   = "ami-06d4b7182ac3480fa"
   ```

   </details>

   Regardez le contenu des fichier states et lister les state à l'aide de la commande `Terraform`

   ```bash
   terraform state list
   terraform state list -state terraform.tfstate.backup
   ```
   
   Supprimer l'ancienne instance dans le state ``

   ```bash
   terraform destroy -state terraform.tfstate.backup
   ```

6. **Utilisation des Outputs Terraform**

   _Pour rappel : Les outputs (sorties) dans Terraform permettent de définir des valeurs que vous souhaitez rendre disponibles après le déploiement des ressources (Ex: l'addresse IP publique de l'instance déployée). Ces valeurs peuvent être utilisées à des fins diverses: pour le reporting, le partage d'informations importantes, ou même pour être utilisées dans d'autres configurations Terraform ou autres outils de configuration (Ex: Ansible)._

   Ajoutez des outputs au fichier `main.tf` pour afficher des informations suivantes sur la ressource créée :
   * IP publique
   * Nom de dns publique

   <details><summary>Correction</summary>

   ```hcl
   output "instance_public_ip" {
     value = aws_instance.example.public_ip
   }

   output "instance_public_dns" {
     value = aws_instance.example.public_dns
   }

   ```

   </details>


7. **Vérification des Outputs Terraform**

   Après l'application des changements, exécutez la commande suivante pour voir les outputs :

   ```bash
   terraform output
   ```

   Cela devrait afficher l'adresse IP publique et le DNS de l'instance EC2 créée.

8. **Suppression de la Ressource**

   Pour éviter des frais inutile ;) , détruisez la ressource en exécutant la commande suivante :

   ```bash
   terraform destroy
   ```

   Confirmez en entrant "yes" lorsque vous y êtes invité.