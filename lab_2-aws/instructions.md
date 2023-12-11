# Lab 2 : Première ressource Terraform sur AWS

**Objectif :** Créer un fichier Terraform simple avec une ressource de type "aws_instance" qui lance une instance EC2 de base.

1. **Création d'un Répertoire de Travail**

Créez un nouveau répertoire `lab_2` dans votre répertoire personnel courant :

```bash
cd
mkdir lab_2
cd lab_2
```

2. **Création d'un Fichier Terraform**

Créez un fichier Terraform nommé `main.tf` dans votre répertoire. Utilisez un éditeur de texte pour ajouter le code Terraform suivant :

```hcl
provider "aws" {
   region = "us-east-1"                       # la région AWS dans laquelle les ressources seront déployées
}

resource "aws_instance" "example" {
   ami           = "ami-0230bd60aa48260c6"    # AMI de Amazon Linux 2
   instance_type = "t2.nano"                  # Type de l'instance
}
```

Pour des raisons pratiques nous ne changerons pas de région. Nous travaillerons dans la région (`us-east-1`) 

3. **Initialisation du Répertoire Terraform**

Exécutez la commande suivante pour initialiser le répertoire Terraform :

```bash
terraform init
```

Cela téléchargera les plugins nécessaires pour le fournisseur AWS.

4. **Vérification du Plan Terraform**

Exécutez la commande suivante pour voir le plan Terraform, qui affiche les changements que Terraform va appliquer :

```bash
terraform plan
```

Vérifiez que la sortie du plan est conforme à vos attentes.

5. **Application des Changements Terraform**

Une fois que vous avez vérifié le plan, appliquez les changements en exécutant la commande :

```bash
terraform apply
```

Confirmez en entrant "yes" lorsque vous y êtes invité.

6. **Vérification de la Ressource Créée**

Après que Terraform a appliqué les changements avec succès, vous pouvez vérifier la ressource créée dans la console AWS ou en utilisant la commande AWS CLI appropriée.

7. **Ajout des modifications à notre instance**

Notre instance est en cours d'execution. Nous allons apporter des modifications notamment en ajoutant un Tag `Name` à notre instance pour le nommer (Ex: Jean DUPON => jean_d).

Apporter les modifications nécessaire. Vous pouvez consulter la [documentation de Terraform sur la resource `aws_instance`](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance)

<details><summary>Correction</summary>

```hcl
provider "aws" {
   region = "us-east-1"                       # la région AWS dans laquelle les ressources seront déployées
}

resource "aws_instance" "example" {
   ami           = "ami-0230bd60aa48260c6"    # AMI de Amazon Linux 2
   instance_type = "t2.nano"                  # Type de l'instance

   tags = {
      Name = "jean_d"
   }

}
```

</details>

8. **Vérification du Plan Terraform**

Réexécutez la commande suivante pour voir le plan Terraform, qui affiche les changements que Terraform va appliquer :

```bash
terraform plan
```

Vérifiez que la sortie du plan est conforme à vos attentes.

9. **Application des Changements Terraform**

Une fois que vous avez vérifié le plan, appliquez les changements en exécutant la commande :

```bash
terraform apply
```

Confirmez en entrant "yes" lorsque vous y êtes invité.

10. **Vérification de la Ressource modifiée**

Après que Terraform a appliqué les changements avec succès, vous pouvez vérifier la ressource modifiée dans la console AWS ou en utilisant la commande AWS CLI appropriée.

11. **Suppression de la Ressource**

Pour éviter des frais inutiles ;) , détruisez la ressource en exécutant la commande suivante :

```bash
terraform destroy
```

Confirmez en entrant "yes" lorsque vous y êtes invité.
