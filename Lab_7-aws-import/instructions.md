# Lab 7 : Importer une infrastructure existant et l'incorporer dans votre state terraform

**Objectif :** L'objectif est ici de savoir comment importer une infrastructure existante qui a été créée en dehors de Terraform et l'incorporer dans votre state terraform

_Il est important de noter que La commande CLI `import`` terraform ne peut qu'importer des ressources dans l'état. L'importation via la commande CLI ne génère pas de configuration. Si vous souhaitez générer la configuration d'accompagnement pour les ressources importées, utilisez plutôt le bloc d'importation._

1. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_7` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_7
   cd lab_7
   ```

2. **Importantion**

Dans le répertoire principal, créer le fichier `main.tf` avec le code suivant :

```hcl
provider "aws" {
  region = "us-east-1"
}

import {
  id = "Id de l'instance à importer"        # vous pouvez utiliser l'Id de l'instance du lab 6
  to = aws_instance.instanceimporte
}

import {
  id = "<id du Security Group>"
  to = aws_security_group.sgimporte      # Optionnel, vous utiliser n'importe quelle Id
}
```

3. **Initialisation du Répertoire Terraform**

Exécutez la commande suivante pour initialiser le répertoire Terraform :

```bash
terraform init
```

Cela téléchargera les plugins nécessaires pour le fournisseur AWS.

4. **Vérification du Plan Terraform**

Exécutez la commande suivante pour voir le plan Terraform avec l'argument `-generate-config-out=<manifest d'import>.tf`, qui affiche les changements que Terraform va appliquer :

```bash
terraform plan -generate-config-out=monimport.tf
```

Il y'a une erreur conflictuelle que vous pouvez ignorer pour l'instant

Vérifiez dans le manifest `monimport.tf` qu'il contient bien les ressources importée avec le maximum de variables ajustables.

Mais pour l'instant il n'a fait que importer. Le state et vide; vérifiez avec la commande `terraform show`
 
5. **Application des Changements Terraform**

L'exécution du plan précédent a permis d'importer et de créer le manifest. exécutez anouveau un `terraform plan` pour appliquer les manifested présents dans votre répertoire courant. Cette fois-ci traitez l'eureur en commentant les deux lignes 
- ipv6_address_count
- ipv6_addresses

Réexécutez à nouveau le plan `terraform plan`; tout devrait être Ok sans erreur.

```
Plan: 2 to import, 0 to add, 0 to change, 0 to destroy.
```

Une fois que vous avez vérifié le plan, appliquez les changements en exécutant la commande :

```bash
terraform apply
```

Confirmez en entrant "yes" lorsque vous y êtes invité. Cela devrait maintenant créer le fichier state sans toute fois rien modifier notre infrastructure en réalité car ces ressources existent déjà. => Regardez la sortie du plan :  0 to add, 0 to change, 0 to destroy.

```bash
terraform show
```

Ne détruisez l'infrastructure.


