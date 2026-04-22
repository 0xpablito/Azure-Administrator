# Lab 06 : Gérer le trafic réseau (Load Balancer & Application Gateway)

## 📝 Description du Lab
Ce lab explore les solutions d'équilibrage de charge sur Azure. L'objectif est de comparer et de mettre en œuvre deux services complémentaires : l'**Azure Load Balancer** (couche 4 - transport) pour la haute disponibilité de base, et l'**Azure Application Gateway** (couche 7 - application) pour le routage intelligent basé sur l'URL.

## 🚀 Objectifs d'apprentissage
* Déployer une infrastructure multi-VM via un template ARM.
* Configurer un **Azure Load Balancer** public pour distribuer le trafic TCP.
* Configurer une **Azure Application Gateway** pour effectuer du routage basé sur les chemins (ex: /image vs /video).
* Tester la résilience et la répartition de charge entre différents serveurs backend.

---

### Tâche 1 : Déploiement de l'infrastructure via Template
**Objectif :** Provisionner l'environnement de test (1 réseau virtuel, 1 groupe de sécurité réseau et 3 VMs) de manière automatisée.

1. Télécharger les fichiers de déploiement (Template et Paramètres) mis à disposition sur le [dépôt officiel du lab](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/tree/master/Allfiles/Labs/06).
2. **Configuration du déploiement :** * Utiliser l'outil **Deploy a custom template** dans le portail Azure.
    * Charger le fichier `az104-06-vms-template.json` dans l'éditeur de modèle.
    * Charger le fichier `az104-06-vms-parameters.json` via l'option **Modifier les paramètres**.
3. **Paramétrage final :**
    * Affecter les ressources au groupe de ressources `rg1`.
    * Définir un **mot de passe administrateur sécurisé** qui sera appliqué aux trois machines virtuelles (`vm0`, `vm1`, `vm2`).
><img width="806" height="867" alt="image" src="https://github.com/user-attachments/assets/9cf672f0-3227-457b-a566-038b4e1bef00" />

### Tâche 2 : Configurer un Azure Load Balancer (Couche 4)
**Objectif :** Équilibrer le trafic public sur le port 80 entre `vm0` et `vm1`.

1. **Création du Load Balancer :**
    * **Nom :** `LoadBalancer` | **SKU :** Standard | **Type :** Public.

><img width="765" height="872" alt="image" src="https://github.com/user-attachments/assets/cb7de8d0-9b52-4db1-88b1-7f4becd84bb0" />

2. **Configuration du Frontend :** Attribution d'une adresse IP publique statique (`lbpip`).

><img width="1453" height="437" alt="image" src="https://github.com/user-attachments/assets/68b39ef6-b50c-46d7-a569-19706658ea26" />

3. **Backend Pool :** Ajout des interfaces réseau (NIC) de `vm0` et `vm1`.

><img width="1448" height="296" alt="image" src="https://github.com/user-attachments/assets/876f68cc-08aa-4e77-ba0a-e31da5a9aee0" />

4. **Définition de la règle d'équilibrage (Load Balancing Rule) :**
    * Configuration d'une règle pour mapper le trafic entrant sur le port **80** vers le pool backend.
    * Création d'une **Sonde de santé (Health Probe)** : Port 80, protocole TCP, pour s'assurer que le Load Balancer n'envoie du trafic qu'aux serveurs opérationnels.

><img width="702" height="845" alt="image" src="https://github.com/user-attachments/assets/64d43aaf-a41d-41be-a7bd-e73ebe5d95d6" />

5. **Vérification de l'équilibreur de charge :**
    * Récupérer l'adresse IP publique depuis la section **Configuration de l'IP de front-end**.
    * En naviguant sur cette adresse et en actualisant la page, on observe que le trafic alterne entre les deux machines du backend pool.

<p align="center">
  <img width="48%" alt="Vérification VM0" src="https://github.com/user-attachments/assets/c4df9a10-e0b5-4a8a-b22c-790df819bbf8" />
  <img width="48%" alt="Vérification VM1" src="https://github.com/user-attachments/assets/a016b4ba-970c-4ba8-ad55-c6937ec93494" />
</p>

### Tâche 3 : Configurer une Azure Application Gateway (Couche 7)
**Objectif :** Implémenter un routage intelligent basé sur le chemin d'URL pour diriger les flux d'images vers un serveur et les flux vidéo vers un autre.

1. **Préparation du réseau :**
    * L'Application Gateway nécessite son propre sous-réseau dédié.
    * Dans le VNet `az104-06-vnet1`, ajouter un sous-réseau nommé `subnet-appgw`.
    * **Plage d'adresses :** `10.60.3.224/27`.
><img width="1467" height="436" alt="image" src="https://github.com/user-attachments/assets/22b241e3-89cc-4150-b9cb-864f02a8b2e2" />

2. **Création de l'Application Gateway :**
    * **Nom :** `appgw` | **Région :** France Central | **Tier :** Standard V2.
    * **Réseau :** Sélectionner `az104-06-vnet1` et le sous-réseau `subnet-appgw`.

><img width="688" height="866" alt="image" src="https://github.com/user-attachments/assets/4ed91aa1-7283-49a7-bb0b-53d87d565f31" />

3.  **Frontend :** Créer une nouvelle adresse IP publique nommée `gwpip`.

><img width="704" height="180" alt="image" src="https://github.com/user-attachments/assets/cfa6ef47-fcda-460e-997e-36065cf37f64" />

4. **Configuration des Backend Pools :**
    * Créer trois pools distincts pour isoler les services :
        * `az104-appgwbe` : Regroupe `vm1` et `vm2` (Pool par défaut).
        * `az104-imagebe` : Cible uniquement `vm1`.
        * `az104-videobe` : Cible uniquement `vm2`.

><img width="677" height="357" alt="image" src="https://github.com/user-attachments/assets/2f4548e7-4b46-4222-888a-fb26e09a3d6d" />

4. **Configuration du routage basé sur le chemin (Path-based routing) :**
    * Ajouter une règle de routage nommée `gwrule`.
    * Configurer un **Listener** HTTP sur le port 80.
    * Dans l'onglet **Cibles de back-end**, configurer le routage par chemin :
        * Chemin `/image/*`  Cible le pool `imagebe`.
        * Chemin `/video/*`  Cible le pool `videobe`.

<p align="center">
  <img src="https://github.com/user-attachments/assets/a5b679c6-1ed3-4046-9e6f-eaa231d4c23f" width="48%" alt="Configuration Application Gateway - Étape 1" />
  &nbsp; <img src="https://github.com/user-attachments/assets/cd7b8520-3d0b-4efc-8457-1ce04c4e250d" width="48%" alt="Configuration Application Gateway - Étape 2" />
</p>

5. **Vérification et Tests :**
    * Une fois le déploiement terminé, vérifier que le **Backend Health** affiche l'état "Healthy" pour tous les serveurs.
<p align="center">
  <img src="https://github.com/user-attachments/assets/755aa51f-498a-44b7-bce9-5b326c334cc3" width="100%" alt="Backend Health Status" />
</p>
    
   * Tester les URLs suivantes via l'IP publique de la Gateway :
        * `http://172.189.118.191/image/`  Doit pointer vers **vm1**.
        * `http://172.189.118.191/video/`  Doit pointer vers **vm2**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/df869c81-7ae4-4de3-89cc-88bb4638ecb6" width="48%" alt="Test URL Image" />
  &nbsp;
  <img src="https://github.com/user-attachments/assets/c5ff6bba-3c50-4678-84c6-9cf3810f457c" width="48%" alt="Test URL Video" />
</p>

##  Conclusion et points clés

Ce lab a permis de comparer concrètement les deux solutions majeures d'équilibrage de charge sur Azure, chacune répondant à des besoins spécifiques :

* **Azure Load Balancer (Couche 4) :** Idéal pour une répartition de charge simple et ultra-performante basée sur le protocole TCP/UDP. Il assure la haute disponibilité sans inspecter le contenu du trafic, ce qui le rend parfait pour des flux réseaux bruts.
* **Azure Application Gateway (Couche 7) :** Véritable "chef d'orchestre" du trafic Web. Grâce à sa capacité à lire les URLs, il permet de segmenter les services (images, vidéos, API) derrière une seule adresse IP.
* **Importance du Health Probe (Sonde de santé) :** Dans les deux cas, la surveillance automatique de l'état des machines est cruciale : si un serveur tombe, le trafic est automatiquement redirigé pour garantir une expérience utilisateur sans interruption.
* **Gouvernance Réseau :** Nous avons vu que l'Application Gateway nécessite une planification plus rigoureuse, notamment avec l'obligation d'un sous-réseau dédié (`/27` minimum).

##  Nettoyage des ressources
**Objectif :** Supprimer les ressources pour éviter les coûts inutiles.

Suppression manuelle du groupe de ressources **rg1** via le portail Azure pour stopper immédiatement la facturation.
><img width="770" height="876" alt="image" src="https://github.com/user-attachments/assets/7ddc52b4-bd93-4fd1-ab3a-37e4a52c2468" />


