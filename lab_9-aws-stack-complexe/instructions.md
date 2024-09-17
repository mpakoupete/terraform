# Lab 9: Deployment d'une architecture complexe

**Objectif :** Savoir organiser son code pour déployer une architecture complexe dans le Cloud via Terraform.

2. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_9` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_9
   cd lab_9
   ```
3. **Mise ensemble de tout ce que nous avons vu**

Juque là nous avons suffisemment des ressources, fonctionalité de terraform... mais tout était à chaque fois mis dans un seul fichier. Vous aller à présent développer une infrastructure avec les carractéristiques suivantes :

* Ecrire un fichier `provider.tf` contiendra la déclaration du provider
* Ecrire un ficher `vcp.tf` qui contiendra toutes les ressources réseaux
* Ecrire un fichier `main.tf` qui contiendra toutes les ressources liée à EC2 et qui fait appel au module créé précedement dans un répertoire local `modules`
* Ecrire un fichier `s3.tf` qui contiendra la création des buckets S3
* Ecrire un fichier `backend.tf` contiendra la déclaration du backend Terraform Cloud
* Ecrire un fichier `variables.tf` qui contiendra toutes les variables
* Ecrire un fichier `output.tf` qui contiendra tous les outputs


4. **Déployez un Cluster kubernetes EKS**

Vous avez pour mission de déployer un cluster kubernetes EKS, un de vos collègue a développé le code terraform contenu dans le répertoire `eks-deploy`
* Visualisez le code Terraform contenu dans ce dit repertoire et interprétez le.
* Quel inconvenient trouvez vous à ce code ?
* Développez un nouveau code plus simple en prenant avantage de l'utilisation des [modules public de Terraform](https://registry.terraform.io/browse/modules)

<details><summary>Correction</summary>

https://github.com/hashicorp/learn-terraform-provision-eks-cluster/blob/main/main.tf

Une explication de la procédure : https://developer.hashicorp.com/terraform/tutorials/kubernetes/eks 

</details>