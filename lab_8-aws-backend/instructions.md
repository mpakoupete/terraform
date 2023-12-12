# Lab 8: Utilisation des worskspaces et different Backends

**Objectif :** Sécuriser notre fichier d'état, facilité la collaboration avec un backend remote du type terraform Cloud.

Cette partie du lab nécessité que vous créez un compte sur terraform Cloud ou bien d'utiliser votre compte GitHub pour se connecter.

1. **Voir les Workspace**

Dans le `lab 7` j'avais demandé de ne pas détruire l'infrastructure, donc vous avez certainement un fichier state présent : 

* Confirmez le avec `terraform state list` et ensuite `terraform show`

Lister les workspaces 

```bash
terraform workspace list
```

Vous devrez certainement voir le workspace par défaut.

Visualisez le contenu du state

```bash
terraform show
```

Visualisez la structure des ressources créées

```bash
terraform graph
```

Créer un nouveau workspace; il vous y placera automatiquement

```bash
terraform workspace new hello
```

Lister à nouveau les workspace:

```bash
terraform workspace list
```

Vérifiiez que dans ce workspace il n'y pas de state 

```bash
terraform show
```

Revenez dans le précédent workspace par défaut

```bash
terraform workspace select default
```

le problème jusque là que nous n'avons pas résolu c'est que ces state sont locaux. Nous voulons des state distant sur un backend bien gardé

2. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_8` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_8
   cd lab_8
   ```

3. **Création de compte Terraform Cloud**


**Etape 1:** Créez un compte sur https://app.terraform.io

Vous pouvez suivre [ce tutorial](https://developer.hashicorp.com/terraform/tutorials/cloud-get-started/cloud-workspace-create) de Hashicorp pour la creation

**Etape 2:** Dans le répertoire local `lab_8` utilisez `terraform login`pour se connecter à votre compte en ligne. Elle vous reconduira dans votre espace pour copier un Token que vous reviendrez coller dans votre terminal.

**Etape 3:** Créez un workspace dans votre espace compte Terraform Cloud


**Etape 4:** Dans le répertoire local `lab_8` créer un fichier manifest `backend.tf` (le nom importe peut) avec le contenu suivant

```hcl
terraform {
  cloud {
    organization = "<votre org>"

    workspaces {
      name = "<le nom du workspace créé>"
    }
  }
}
```

```bash
terraform init
```

4. **Travailler avec le remote backend**

Pour tester, nous allons copier les manifests `main.tf` du `lab 6` et le mettre dans notre répertoire. Ou tout autre manifest de votre choix.

Réinitialisez, visualiser le plan et appliquer

```bash
terraform init
terraform plan
terraform apply
```

Vous rencontrez certainement une erreur de crédential car l'exécution est en remote et là, il n'y a pas de crédential. Pour ce faire au lieu de mettre les clés AWS dans le code, ce qui n'est pas une bonne pratique de sécurité; nous allons plutôt utiliser les Variable dans Terraform Cloud pour définir ces variables. ([Voir cette documentation pour cela](https://developer.hashicorp.com/terraform/tutorials/cloud-get-started/cloud-create-variable-set))

Réexécutez et visualisez l'exécution dans Terraform Cloud.

Une meilleur pratique de sécurité est de gérer le code terraform dans un repo central (ex: GitHub) et configurer le compte terraform Cloud pour déclencer le provisioning de l'infrastructure chaque fois que le repos git est mis à jour. Bien sûr, vous devez mettre en place un minimum de controle sur les Pull Request de votre repo Git.



