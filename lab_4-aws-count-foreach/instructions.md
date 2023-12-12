# Lab 4: Count, foreach avec Terraform

**Objectif :** Introduire l'utilisation de  Count et foreach Terraform pour itérrer sur du code le code plus flexible.

Pour toutes les instances on choisira :
* l'AMI de AMAZON LInux : `ami-0230bd60aa48260c6`
* type d'instance `t2.nano`
* La paire de clé SSH `prenom-key` (e.g. `jean-key` pour l'utilisateur Jean DUPOND) que vous devez créer dans la console

1. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_4` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_4
   cd lab_4
   ```

2. Créez un fichier de configuration Terraform, par exemple `main.tf`, avec les indications suivantes :
* Une variable  `instance_count` dont la valeur par défaut est 2
* Créera `instance_count` nombre de répliques d'instance dont les caractéristiques sont mentionnées au début du lab : type d'instance `t2.nano` et AMI.

Simple non ? vous pouvez jetez un coup d'oeil à la correction pour celui-ci ;)

<details><summary>Correction</summary>

```hcl
  provider "aws" {
    region = "us-east-1"
  }

  # Définissez le nombre d'instances EC2 à créer
  variable "instance_count" {
    default = 2
  }

  # Créez des instances EC2 en utilisant Count
  resource "aws_instance" "example" {

    count = var.instance_count        # La présence de count créera 2 répliques identique de cette instance dans le cloud

    ami           = "ami-0230bd60aa48260c6"
    instance_type = "t2.nano"
    key_name      = "mawaki-key" # Remplacez par votre nom de clé SSH
  }

```

</details>

3. Améliorons le code précédant. Nous avons constaté qu'il est difficile d'identifier les instances créées. Nest-ce pas ?
   Eh bien, modifiez le code pour inclure le tag Name nuivant `webserver-{prenom}-{count}`. En gros on doit voir dans la console 2 instances nommées de cette façons :
   - webserver-jean-1
   - webserver-jean-2

N'ouvrez pas de si tôt la correction. ;) on fait travailler un peu Mr le cerveau et Mme la doc.

<details><summary>Correction</summary>

```hcl

  provider "aws" {
    region = "us-east-1"
  }

  # Définissez le nombre d'instances EC2 à créer
  variable "instance_count" {
    default = 2
  }

  variable "tag_Name" {
  default     = "webserver-jean"                     # le Tag de notre instance
  description = "le nom donnée à notre instance"
}

  # Créez des instances EC2 en utilisant Count
  resource "aws_instance" "example" {

    count = var.instance_count        # La présence de count créera 2 répliques identique de cette instance dans le cloud

    ami           = "ami-0230bd60aa48260c6"
    instance_type = "t2.micro"
    key_name      = "mawaki-key" # Remplacez par votre nom de clé SSH

    tags = {
      Name = "${var.tag_Name}-${count.index}"    # Incorporation du nom et de l'index dans le Tag
    }

  }

```

</details>


4. Jouer sur la variable par défaut `instance_count` en mettant à 3 par exemple pour constater l'effet.

5. Detruisez l'environment précédent.

6. Nous allons à présent utiliser foreach. Imaginons que nous souhaitons créer 3 instances suivants : `web`, `app` et `db` et pour chacun de ces serveurs/instances il doit accédé à un Bucket S3 dont le nom est `wizetraining-{prenom}.com-bucket-web`, `wizetraining-{prenom}.com-bucket-app`, `wizetraining-{prenom}.com-bucket-db`.(ex: `wizetraining-jean.com-bucket-db` ) Cette architecture fera intervenir les notions de :
- Instance EC2
- Bucket S3
- IAM Role appropié à chaque instance pour accéder à son Bucket S3.

Commençons en douceur ;) . Créez un fichier de configuration Terraform, par exemple `main.tf`, qui créera les 3 instances pour les trois fonctions web, app, db. Pensez à utiliser une variable exemple `my_apps` qui contient la list  `web`, `app` et `db`.

On réfléchit un peu ?


<details><summary>Correction</summary>

```hcl

  provider "aws" {
    region = "us-east-1"
  }

  # Définissez la varible
  variable "my_apps" {
    default = ["web", "app", "db"]
  }

  # Créez des instances EC2 en utilisant Count
  resource "aws_instance" "example" {

    for_each = toset(var.my_apps)         # Parcourir la liste      

    ami           = "ami-0230bd60aa48260c6"
    instance_type = "t2.micro"
    key_name      = "mawaki-key" # Remplacez par votre nom de clé SSH

    tags = {
      Name = each.key                    # Il prend pour chaque itérration la clé
    }

  }

```

</details>

7. Faites de même pour les Bucket S3 en se basant sur le manifest précédent. 

<details><summary>Correction</summary>

```hcl

  provider "aws" {
    region = "us-east-1"
  }

  # Définissez la varible
  variable "my_apps" {
    default = ["web", "app"]
  }

  # Créez des instances EC2 en utilisant Count
  resource "aws_instance" "example" {

    for_each = toset(var.my_apps)        

    ami           = "ami-0230bd60aa48260c6"
    instance_type = "t2.micro"
    key_name      = "mawaki-key" # Remplacez par votre nom de clé SSH

    tags = {
      Name = each.key
    }

  }

  # Créez des buckets S3 en utilisant Foreach
  resource "aws_s3_bucket" "example" {
    for_each = toset(var.my_apps)

    bucket = "wizetraining-jean.com-bucket-${each.key}"
  }
      
```

</details>

8. Pour chacune des instances, associez l'IAM Role `EC2AccessS3Admin` déjà existant dans le cloud.

<details><summary>Correction</summary>

```hcl
provider "aws" {
  region = "us-east-1"
}

# Définissez la varible
variable "my_apps" {
  default = ["web", "app"]
}

# Créez des instances EC2 en utilisant Count
resource "aws_instance" "example" {

  for_each = toset(var.my_apps)        

  ami           = "ami-0230bd60aa48260c6"
  instance_type = "t2.micro"
  key_name      = "mawaki-key" # Remplacez par votre nom de clé SSH

  tags = {
    Name = each.key
  }

  iam_instance_profile = "EC2AccessS3Admin"

}

# Créez des buckets S3 en utilisant Foreach
resource "aws_s3_bucket" "example" {
  for_each = toset(var.my_apps)

  bucket = "wizetraining-jean.com-bucket-${each.key}"
}
```

</details>

9. Ajouter un output à votre code pour lister les IP publiques et les Nom de DNS publiques de vos instances.

10. Tester et vérifier que les instances EC2 ont accès aux Buckets en seconnectant à ces instances par ssh et en exécutant simplement la commande :
     `aws s3 ls`
     `aws s3 ls s3://wizetraining-jean.com-bucket-web/`
     `touch /tmp/test-file`
     `aws s3 cp /tmp/test-file s3://wizetraining-jean.com-bucket-web/ `
     `aws s3 ls s3://wizetraining-jean.com-bucket-web/` ==> vous devez voir le contenu du bucket


11. Détruisez votre installation