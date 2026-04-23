# Lab 07 : Gérer le stockage Azure (Azure Storage)

## 📝 Description du Lab
Ce lab est dédié à la mise en œuvre et à la sécurisation des solutions de stockage Azure. L'objectif est de configurer des comptes de stockage pour les **Blobs** et les **Fichiers (Azure Files)**, tout en appliquant des règles de gouvernance comme la redondance géographique, les politiques de cycle de vie et la restriction d'accès via le réseau virtuel (VNet).

## 🚀 Objectifs d'apprentissage
* Créer un compte de stockage avec redondance géographique (**GRS**).
* Configurer des politiques de **Cycle de vie** (Lifecycle Management) pour optimiser les coûts.
* Sécuriser les conteneurs de Blobs via l'immuabilité et les jetons **SAS**.
* Restreindre l'accès au stockage via des **Service Endpoints** et un réseau virtuel.

---

## 🛠 Étapes réalisées

### Tâche 1 : Créer et configurer un compte de stockage
**Objectif :** Déployer une infrastructure de stockage résiliente et sécurisée.

1. **Création du compte :**
    * **Nom :** stoackageazurelabpablo | **Région :** France Central.
    * **Redondance :** **Geo-redundant storage (GRS)** pour garantir la disponibilité en cas de panne régionale.
    * **Sécurité :** Accès réseau public désactivé.

><img width="747" height="870" alt="image" src="https://github.com/user-attachments/assets/8ccc224f-ac14-4bb0-ae1b-1e2d42496d18" />

2. **Configuration de la sécurité réseau :**
    * Depuis le compte de stockage, aller dans l'onglet **Networking**.
    * Activer l'accès en choisissant l'option **Enabled from selected networks and IP addresses**.
    * Cliquer sur **Add your client IPv4 address** pour autoriser votre machine actuelle à accéder aux données.

><img width="1409" height="671" alt="image" src="https://github.com/user-attachments/assets/f275fdab-4162-4e20-84d1-d726901756f7" />

3. **Vérification de la redondance :**
    * L'onglet **Redundancy** permet de visualiser la localisation du datacenter principal ainsi que celle du datacenter secondaire. 
    * Cette vue confirme que les données sont répliquées dans une région géographique distante (grâce au choix du mode GRS lors de la création).

><img width="1296" height="518" alt="image" src="https://github.com/user-attachments/assets/0dccf606-0185-43b9-9225-d4dbdfed80b0" />

4. **Gestion du cycle de vie (Lifecycle Management) :**
    * Création d'une règle `Basculer en stockage cool` : Les données non modifiées depuis **30 jours** basculent automatiquement vers le stockage **Cool** (moins coûteux).

><img width="721" height="440" alt="image" src="https://github.com/user-attachments/assets/0c63d6e6-7fc6-4037-8b75-fcd5067ea97b" />

---

### Tâche 2 : Créer et sécuriser le stockage Blob
**Objectif :** Gérer les données non structurées avec un contrôle d'accès granulaire.

1. **Conteneur et Immuabilité :**
    * Création du conteneur `data` en accès privé.
    * Application d'une **politique de rétention de 180 jours** (Immutable storage) pour empêcher toute suppression.

><img width="1615" height="314" alt="image" src="https://github.com/user-attachments/assets/6c3d0bb6-e62e-4e2f-9c41-17a1c93c3d07" />


2. **Test d'accès anonyme (Échec attendu) :**
    * Upload d'un fichier dans le dossier virtuel `/securitytest`.
    * Tentative d'accès au fichier via son URL publique directe.
    * **Résultat :** Le navigateur affiche une erreur `PublicAccessNotPermitted`. Cela confirme que le conteneur est correctement sécurisé et n'autorise pas l'accès anonyme.

><img width="1150" height="120" alt="image" src="https://github.com/user-attachments/assets/5fb81a67-c4f4-4739-b99a-7d5b8fc79c79" />


3. **Génération et test du jeton SAS (Succès) :**
    * Génération d'une **Shared Access Signature (SAS)** pour autoriser l'accès en lecture sur une durée limitée.
    * Grâce au lien **Blob SAS URL** généré, l'accès au fichier est désormais possible tout en conservant le conteneur en mode privé.


><img width="1601" height="738" alt="image" src="https://github.com/user-attachments/assets/4c5c1a89-b530-43f3-a810-0bdae378e530" />



---

### Tâche 3 : Azure Files et restriction réseau (VNet)
**Objectif :** Déployer un partage de fichiers et verrouiller son accès au niveau réseau.

1. **Création du partage de fichiers :**
    * Création d'un partage nommé `share1`.
    * **Tier :** *Transaction Optimized* (choisi pour optimiser les performances lors de manipulations fréquentes de fichiers).

><img width="496" height="263" alt="image" src="https://github.com/user-attachments/assets/29dc3570-f680-4563-9516-39f937f5add2" />

2. **Utilisation du Storage Browser :**
    * L'outil **Storage Browser**, intégré directement dans le portail Azure, permet une gestion simplifiée de l'arborescence.
    * Il est possible de créer des dossiers, de charger des fichiers et de gérer l'intégralité du contenu de `share1`.

><img width="1678" height="515" alt="image" src="https://github.com/user-attachments/assets/b93d32ac-d6c0-4185-8f8a-d1595cc52ebf" />
> Note : Azure Files est la solution idéale pour remplacer ou étendre les serveurs de fichiers traditionnels (on-premises) car il supporte le protocole SMB, permettant de monter le partage comme un lecteur réseau classique sur Windows, Linux ou macOS.

3. **Sécurisation par Service Endpoints :**
    * Création d'un VNet `vnet1`.
    * Activation du **Service Endpoint** `Microsoft.Storage` sur le sous-réseau par défaut.

><img width="1895" height="592" alt="image" src="https://github.com/user-attachments/assets/2ef0de21-5b22-4e36-a1cd-d3af0841a440" />

4. **Restriction de l'accès au réseau virtuel :**
    * Dans les paramètres **Networking** du compte de stockage, sélection de l'option **Add existing virtual network** pour lier `vnet1`.
    * **Nettoyage de la Whitelist :** Suppression de l'adresse IP publique de la machine locale dans la section *IPv4 Addresses*. Désormais, seul le trafic provenant du VNet est autorisé.

><img width="1449" height="245" alt="image" src="https://github.com/user-attachments/assets/ca72bea6-c5f1-41a0-84aa-c2ea53e8fa87" />

5. **Test de validation :**
    * Tenter d'accéder au **Storage Browser** pour visualiser le contenu du partage de fichiers ou des blobs.
    * **Résultat attendu :** Un message d'erreur s'affiche (`Not authorized`).

><img width="739" height="480" alt="image" src="https://github.com/user-attachments/assets/70060052-6bc1-4567-9875-5fac3af04c80" />



---

## Conclusion et points clés

* **Optimisation des coûts :** Le stockage GRS et les politiques de cycle de vie permettent de protéger les données tout en minimisant la facture Azure.
* **Sécurité "Zero Trust" :** L'utilisation combinée des jetons SAS et des Service Endpoints garantit que les données ne sont accessibles que par les utilisateurs autorisés et depuis des réseaux spécifiques.
* **Résilience :** La redondance GRS offre une protection contre les sinistres à l'échelle d'une région entière.

---

##  Nettoyage des ressources
**Objectif :** Supprimer le groupe de ressources pour éviter la facturation.

`Remove-AzResourceGroup -Name "rg1"`

><img width="486" height="135" alt="image" src="https://github.com/user-attachments/assets/ab2a0db3-e2bf-4917-ac50-20d7ecb67026" />
