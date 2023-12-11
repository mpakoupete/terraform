# Lab 1: Première ressource Terraform sur OpenStack

**Objectif :** Créer un fichier Terraform simple avec une ressource de type "openstack_compute_instance_v2" qui lance une instance Nova dans notre Cloud Privé OpenStack.Mais avant cela, explorons les différents composants de OpenStack : 

## Exploration graphique de OpenStack

Si l'installation s'est bien déroulé sur votre post lab vous devez pouvoir vous connecter grâce au lien : http://10.0.0.10/
* user : admin
* mot de passe: secret

Explorer l'interface, notamment les 3 grande sections : Project, Admin, Identity
* Dans Project, explorer les sections filles : Compute, Volumes, Network
* Dans Admin vous pouvez administrer les ressources propres à OpenStack: ajouter/modifier des images, ajouter ou supprimer les configurations...
  * Combien d'hyperviseurs il y'a t'il ?
  * Combien d'images il y'a t'il ?
  * Explorer les Flavors. Qu'est ce que les Flavors ? (Lorsqu'on parle de type d'instance AWS, GCP ou Azure, ça vous parle ?)
* Faites un parallele de OpenStack avec AWS

## Exploration de OpenStack en ligne de commande

### Commande OpenStack

Pour certaines opérations, nous aurons besoin d'utiliser commande `openstack` en ligne de commande. [Guide d'utilisateur](https://docs.openstack.org/python-openstackclient/latest/cli/index.html)

Syntaxe : `openstack [commande] [options] [arguments]`

**Exemples :**

* `openstack image list` pour lister les images
* `openstack server list --all-projects`  pour lister toutes les instances de tous les projets.

Mais, `Openstack` a besoin de cibler et de s'authentifier afin que ces commandes puissent fonctionner.


### Installation du client Openstack

Sur la machine hôte, [installer le client](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html) `openstack``

```bash
pip install python-openstackclient
```

Configurer ces variables d'environnement du client

```bash
AUTH_URL=http://10.0.0.10/identity
PROJECT_NAME=demo
DOMAIN_NAME=default
USER_DOMAIN_NAME=default
USERNAME=admin
PASSWORD=secret
```

tester que cela fonctionne bien:

```bash
openstack \
  --os-auth-url $AUTH_URL \
  --os-project-name $PROJECT_NAME \
  --os-project-domain-name $DOMAIN_NAME \
  --os-user-domain-name $USER_DOMAIN_NAME \
  --os-auth-type=v3password \
  --os-username $USERNAME \
  --os-password $PASSWORD \
  image list
```

<details><summary>Resultat de la commande : </summary>

```bash
+--------------------------------------+---------------------------+--------+
| ID                                   | Name                      | Status |
+--------------------------------------+---------------------------+--------+
| 09f686fc-0131-4a13-99e3-741f78732a1b | cirros-0.6.2-x86_64-disk  | active |
+--------------------------------------+---------------------------+--------+
```

</details>

## Créer une instance OpenStack à l'aide de Terraform

1. **Création d'un Répertoire de Travail**

   Créez un nouveau répertoire `lab_1` dans votre répertoire personnel courant :

   ```bash
   cd
   mkdir lab_1
   cd lab_1
   ```

2. **Création d'un Fichier Terraform**

Consultez la [Documentation officiel du provider](https://registry.terraform.io/providers/terraform-provider-openstack/openstack/latest/docs) et créez un fichier Terraform nommé `main.tf` dans votre répertoire pour déployer une instance nommée `lab1` et dont l'image est `cirros` et la paire de clée est `lab1` que vous aurez créé au préalable dans l'interface de OpenStack. Il sera connecté au réseau `shared`

<details><summary>Correction</summary>

Utilisez un éditeur de texte pour ajouter le code Terraform suivant :

```bash
# Pour trouver project Id ou Tenant Id qui sont les même dans OpenStack
openstack \
  --os-auth-url $AUTH_URL \
  --os-project-name $PROJECT_NAME \
  --os-project-domain-name $DOMAIN_NAME \
  --os-user-domain-name $USER_DOMAIN_NAME \
  --os-auth-type=v3password \
  --os-username $USERNAME \
  --os-password $PASSWORD \
project show admin
```

```hcl
terraform {
required_version = ">= 0.14.0"  # Terraform doit être exécuté avec une version de Terraform égale ou supérieure à 0.14.0

# Indique que le code Terraform dépend du fournisseur OpenStack pour interagir avec l'API OpenStack et déployer les ressources cloud.
  required_providers { 
    openstack = {
      source  = "terraform-provider-openstack/openstack"         # L'emplacement du fournisseur OpenStack. ici, il s'agit de la source dans le référentiel Terraform Registry pour le fournisseur OpenStack avec le nom "terraform-provider-openstack/openstack"
      version = "~> 1.53.0"          #  version spécifique du fournisseur OpenStack requise. Ici
    }
  }
}

# Configuration du Provider OpenStack 

provider "openstack" {
  auth_url = "http://10.0.0.10/identity"
  user_name = "admin"
  password = "secret"
  region = "RegionOne"
  tenant_name = "admin"                             # tenant est aussi Project 
  tenant_id = "2787ecad673d46e68980e0f4bcad7aa5"    # Aussi Project ID
}

resource "openstack_compute_instance_v2" "instance-lab1" {
  name            = "lab1"
  image_id        = "09f686fc-0131-4a13-99e3-741f78732a1b"  # Id de mon image cirros
  flavor_id       = "c1"
  key_pair        = "lab1"                                #
  security_groups = ["default"]

  network {
    name = "shared"
  }
}
```

</details>

3. **Initialisation du Répertoire Terraform**

Exécutez la commande suivante pour initialiser le répertoire Terraform :

```bash
terraform init
```

Cela téléchargera les plugins nécessaires pour le fournisseur OpenStack.

2. **Vérification du Plan Terraform**

Exécutez la commande suivante pour voir le plan Terraform, qui affiche les changements que Terraform va appliquer :

```bash
terraform plan
```

Vérifiez que la sortie du plan est conforme à vos attentes.

3. **Application des Changements Terraform**

Une fois que vous avez vérifié le plan, appliquez les changements en exécutant la commande :

```bash
terraform apply
```

Confirmez en entrant "yes" lorsque vous y êtes invité.

4. **Vérification de la Ressource Créée**

Après que Terraform a appliqué les changements avec succès, vous pouvez vérifier la ressource créée dans la console OpenStack ou en utilisant la commande openstack CLI appropriée.

```bash
openstack \
--os-auth-url $AUTH_URL \
--os-project-name $PROJECT_NAME \
--os-project-domain-name $DOMAIN_NAME \
--os-user-domain-name $USER_DOMAIN_NAME \
--os-auth-type=v3password \
--os-username $USERNAME \
--os-password $PASSWORD \
server list --all-projects
```

ou si vous rencontrez des error à cause des variables :

```bash
openstack \
--os-auth-url "http://10.0.0.10/identity" \
--os-project-name demo \
--os-project-domain-name default \
--os-user-domain-name default \
--os-auth-type=v3password \
--os-username admin \
--os-password secret \
server list --all-projects
```

5. **Ajout des modifications à notre instance**

Notre instance est en cours d'execution. Nous allons apporter des modifications notamment en ajoutant un security Group `ssh-allow` à notre instance.
Les règles de ce Security Group sont les suivantes : Autoriser tous les traffic entrant SSH

Avant d'exécuter le plan vérifiez que votre instance n'est pas accessible via SSH:

Vérification un peu pénible :( , n'oubliez pas que nous sommes sur OpenStack :

* Placez vous dans le répertoire `setup` ou vous avez lancez l'instance Vagrant
* exécutez `vagrant status` => vous devez voir la VM 
* exécutez `vagrant ssh` => Il se connectera par défaut à la seue instance que nous avons. Et voilà, nous sommes sur la VM openstack
* exécutez `sudo ip netns`    => Vous devez voir au moin un Network namespace créer. Et bien, nous allons passer par ce net namespace pour faire un ssh à notre instance
* Copier les contenu de votre clé privé dans un fichier que vous nommerez : `lab1-key.pem`
* exécutez `sudo ip netns exec <ID_du_NETNS> ssh -i lab1-key.pem cirros@<IP_de_INSTANCE>` . Exemple : `sudo ip netns exec ovnmeta-ed0ebcce-585d-4ff5-8262-97fc9301b631 ssh -i demo.pem 10.0.0.37`

cela ne devrait pas fonctionner car le SG par défaut ne nous l'autorise pas.

Apporter les modifications nécessaire afin que le SSH fonctionne. Vous pouvez consulter la documentation de Terraform sur la resource [`openstack_networking_secgroup_rule_v2`](https://registry.terraform.io/providers/terraform-provider-openstack/openstack/latest/docs/resources/networking_secgroup_rule_v2)

<details><summary>Correction</summary>

```hcl
terraform {
    required_version = ">= 0.14.0"
    required_providers { 
        openstack = {
        source  = "terraform-provider-openstack/openstack"
        }
    }
}

provider "openstack" {
    auth_url = "http://10.0.0.10/identity"
    user_name = "admin"
    password = "secret"
    region = "RegionOne"
    tenant_name = "admin"
    tenant_id = "2787ecad673d46e68980e0f4bcad7aa5"
}

resource "openstack_compute_instance_v2" "instance-lab1" {
    name            = "lab1"
    image_id        = "09f686fc-0131-4a13-99e3-741f78732a1b"
    flavor_id       = "c1"
    key_pair        = "lab1"
    security_groups = ["default","ssh-allow"]        # On ajoute le Security Group
    depends_on = [ openstack_networking_secgroup_rule_v2.secgroup_rule_1 ]    # On ajoute cette dépendance car cette modification de l'ajout de Security Group nécessite que le SG existe au préalable

network {
    name = "shared"
  }
}

# On crée l'enveloppe du Security Group ssh-allow et par la suite nous allons définir les règles

resource "openstack_networking_secgroup_v2" "secgroup_1" {
    name        = "ssh-allow"
    description = "Autoriser les flux SSH entrant"
}

# Définition des règles de Security Group et nous le rattachons aux Security Group ssh-allow précedemment créé grace à l'option security_group_id
resource "openstack_networking_secgroup_rule_v2" "secgroup_rule_1" {
    direction         = "ingress"
    ethertype         = "IPv4"
    protocol          = "tcp"
    port_range_min    = 22
    port_range_max    = 22
    remote_ip_prefix  = "0.0.0.0/0"
    security_group_id = openstack_networking_secgroup_v2.secgroup_1.id        # association de cette règle au SG ssh-allow dont la ressource terraform est identifé par secgroup_1
}
```

</details>

6. **Vérification du Plan Terraform**

Réexécutez la commande suivante pour voir le plan Terraform, qui affiche les changements que Terraform va appliquer :

```bash
terraform plan
```

Vérifiez que la sortie du plan est conforme à vos attentes.
Combien d'ajout avons ?
Combien de modification ?
Combien de suppression ?

7. **Application des Changements Terraform**

Une fois que vous avez vérifié le plan, appliquez les changements en exécutant la commande :

```bash
terraform apply
```

Confirmez en entrant "yes" lorsque vous y êtes invité.

8.  **Vérification de la Ressource modifiée**

Après que Terraform a appliqué les changements avec succès, vous pouvez vérifier la ressource modifiée dans la console OpenStack ou en utilisant la commande OpenStack CLI appropriée.
Puis, reessayez le SSH.

9. **Ajouter une règle HTTP similare SSH**

Faites le Plan, apply et notez les changements apportés.

10. **Supprimer la règle HTTP créée**

Il vous est demander de supprimer la règle HTTP créée. Comment ferez vous ?

<details><summary>Correction</summary>

Il nous suffit de retirer le bloc que nous avons ajouter. Lorsque nous ferons le apply, Terraform compare avec le state précédent et comprend que vous souhaitez retirer/supprimer la règle

</details>

Faites le Plan, apply et notez les changements apportés.
