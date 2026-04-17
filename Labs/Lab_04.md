# Lab 04 : Implémenter le réseau virtuel (VNet)

## 📝 Description du Lab
Ce lab se concentre sur les fondamentaux du réseau Azure. L'objectif est de concevoir une infrastructure réseau segmentée, de sécuriser les flux de trafic via des groupes de sécurité et de configurer la résolution de noms DNS.

##  Objectifs d'apprentissage
* Créer et configurer des réseaux virtuels (VNets) et des sous-réseaux (Subnets).
* Sécuriser le trafic réseau à l'aide de **Network Security Groups (NSG)** et **Application Security Groups (ASG)**.
* Configurer des zones **Azure DNS** publiques et privées.

---

## 🛠 Étapes réalisées

### Tâche 1 : Créer un réseau virtuel via le portail Azure
**Objectif :** Mettre en place un réseau virtuel et créer des sous-réseaux

1. Rechercher et sélectionner Réseaux virtuels dans le portail Azure, puis cliquer sur Créer.
2. Configurer le réseau nommé ServiceCentraux avec les paramètres suivants :
* Région : France Central
* Espace d'adressage IPv4 : 10.20.0.0/16
3. Dans l'onglet Adresses IP, ajouter les deux sous-réseaux suivants :
* Sous-réseau 1 : Nom ServicesPartages | Adresse : 10.20.10.0/24
* Sous-réseau 2 : Nom BaseDeDonnees | Adresse : 10.20.20.0/24
> Bien que la gestion de l'adressage et du subnetting soit un acquis du CCNA, il est crucial de noter qu'Azure réserve 5 adresses IP par sous-réseau, contre 2 dans un environnement standard. En plus des adresses de réseau et de diffusion, Azure s'approprie trois adresses supplémentaires dédiées à la passerelle par défaut, au service DNS et à la maintenance interne de la plateforme.
4. Vérifier les paramètres, lancer la création de la ressource, puis accéder à la ressource une fois qu’elle est déployée.
><img width="668" height="869" alt="image" src="https://github.com/user-attachments/assets/6a2eb75f-1f27-455b-9098-8103ab20c4e3" />
5. Télécharger les fichiers `Template`et `Parameters` pour l'étape suivante.
> <img width="680" height="853" alt="image" src="https://github.com/user-attachments/assets/fcf99850-a775-4fb8-89de-417b88222b9f" />

---

### Tâche 2 : Déployer un VNet via un template
**Objectif :** Utiliser l'automatisation (IaC) pour déployer un second réseau.

1. Ouvrir le fichier `template.json` dans un éditeur de code (**VS Code**) pour préparer la configuration du nouveau réseau.
2. Effectuer les changements pour le réseau virtuel nommé **ReseauProduction** :
    * Remplacer toutes les occurrences de `ServiceCentraux` par `ReseauProduction`.
    * Remplacer l'espace d'adressage `10.20.0.0` par `10.30.0.0`.
3. Effectuer les changements pour les sous-réseaux :
    * Remplacer `ServicesPartages` par `Capteur1`.
    * Mettre à jour l'adresse : remplacer `10.20.10.0/24` par `10.30.20.0/24`.
    * Remplacer `BaseDeDonnees` par `Capteur2`.
    * Mettre à jour l'adresse : remplacer `10.20.20.0/24` par `10.30.21.0/24`.
4. Remplacer l'occurence de ServiceCentraux par `ReseauProduction`dans le fichier parameters.json.
><img width="1219" height="379" alt="image" src="https://github.com/user-attachments/assets/63850ee4-ddd5-4636-b59a-781889d13bc7" />
5. Déployer le nouveau réseau virtuel à l’aide du modèle personnalisé en ajoutant les deux fichiers modifiés.
><img width="720" height="869" alt="image" src="https://github.com/user-attachments/assets/5c7cd1ac-d16f-4572-8967-7d9e61b72ecb" />

---

### Tâche 3 : Configurer la sécurité avec ASG et NSG
**Objectif :** Créer des groupes de sécurité applicatifs et réseau pour filtrer et sécuriser les flux de données.
1. **Création d'un Application Security Group (ASG) :**
    * Rechercher **Groupes de sécurité d'application** et cliquer sur **Créer**.
    * **Groupe de ressources :** `rg1`
    * **Nom :** `ASG-WEB`
    * **Région :** France Central
2. **Création d'un Network Security Group (NSG) :**
    * Rechercher **Groupes de sécurité réseau** et cliquer sur **Créer**.
    * **Nom :** `NSG-SECURE`
    * **Région :** France Central
<p align="center">
  <img src="https://github.com/user-attachments/assets/cedbf7fb-5111-461f-896a-4a35521ff2a2" width="45%" />
  &nbsp;&nbsp;&nbsp;&nbsp; <img src="https://github.com/user-attachments/assets/31e50aa3-c363-498f-a647-d74c9c39b523" width="45%" />
</p>
3. Une fois le NSG créé, aller dans l'onglet **Sous-réseaux** et cliquer sur **Associer** pour le lier au sous-réseau `ServicePartagé` du VNet `ServiceCentraux`.
><img width="1492" height="481" alt="image" src="https://github.com/user-attachments/assets/5ec015d3-1631-412c-90b2-34a08a6a3c71" />

4. **Configuration des règles entrantes (Inbound) :**
    * Dans le NSG, ajouter une règle autorisant le trafic provenant de l'ASG :
        * **Source :** Application security group (`ASG-WEB`)
        * **Plages de ports de destination :** `80, 443`
        * **Action :** Allow
        * **Nom :** `AutoriserASG`
><img width="1904" height="839" alt="image" src="https://github.com/user-attachments/assets/5ea175ab-573e-4049-9c6d-8686ddac31b7" />

5. **Configuration des règles sortantes (Outbound) :**
    * Ajouter une règle pour interdire l'accès à Internet :
        * **Destination :** Service Tag (`Internet`)
        * **Action :** Deny
        * **Priorité :** `4096`
        * **Nom :** `DenyInternetOutbound`
><img width="1894" height="855" alt="image" src="https://github.com/user-attachments/assets/16d27697-4c93-4c89-abcf-19182ab32586" />

---

### Tâche 4 : Configurer Azure DNS (Public et Privé)
**Objectif :** Assurer la résolution de noms interne et externe.

1. **Configuration d'une zone DNS publique :**
    * Rechercher **Zones DNS** dans le portail Azure et cliquer sur **Créer**.
    * **Groupe de ressources :** `rg1`
    * **Nom :** `pablo.com` 
    * **Région :** (La zone DNS est une ressource globale, mais le groupe de ressources reste localisé).
><img width="432" height="872" alt="image" src="https://github.com/user-attachments/assets/af2b7a6b-e17e-4427-823f-5ac10aaf8e96" />
2. **Ajout d'un enregistrement (Record set) :**
    * Dans la zone `pablo.com`, cliquer sur **+ Jeu d'enregistrements**.
    * **Nom :** `www`
    * **Type :** `A`
    * **Adresse IP :** `10.1.1.4`.
><img width="1903" height="874" alt="image" src="https://github.com/user-attachments/assets/e8c6a770-ec79-403e-a222-890d3d03e275" />
3. **Vérification de la résolution :**
    * Ouvrir un terminal et tester la résolution avec la commande :
    `nslookup www.pablo.com <Nom_du_serveur_DNS_Azure>`
><img width="553" height="123" alt="image" src="https://github.com/user-attachments/assets/acbc9c3b-07f4-4c6a-96d4-f42c8c39aae8" />
4. **Configuration d'une zone DNS privé :**
* Rechercher **Zones DNS privés** dans le portail Azure et cliquer sur **Créer**.
    * **Groupe de ressources :** `rg1`
    * **Nom :** `private.pablo.com`
><img width="603" height="867" alt="image" src="https://github.com/user-attachments/assets/629a4369-ce88-4553-be70-63b9f4f787d4" />
5. **Création d'un lien de réseau virtuel (Virtual Network Link) :**
    * Dans la zone DNS privée, aller dans la section **Liaisons de réseau virtuel**.
    * Cliquer sur **+ Ajouter** pour l'associer au réseau `ReseauProduction`.
    * **Nom de la liaison :** `lien_Production`
><img width="763" height="871" alt="image" src="https://github.com/user-attachments/assets/c04145ad-d666-42a5-8c92-8c826811d1ea" />

6. **Ajout d'enregistrements pour les machines virtuelles :**
    * Si l'enregistrement automatique n'est pas utilisé, ajouter manuellement un **Jeu d'enregistrements** (Record set).
    * **Nom :** `sensorvm`
    * **Type :** `A`
    * **Adresse IP :** `10.1.1.4` (IP de la machine interne).
><img width="1902" height="653" alt="image" src="https://github.com/user-attachments/assets/8c5b1c19-b6d8-4ac5-b3c0-44aa55c193e3" />
> Note : Contrairement à la zone publique, la zone DNS privée n'est accessible que depuis les réseaux virtuels explicitement liés. C'est un concept essentiel pour la sécurité et la communication interne des microservices.
---

##  Conclusion et points clés

* **Segmentation réseau :** L'utilisation de sous-réseaux permet d'isoler les ressources et d'appliquer des politiques de sécurité distinctes.
* **Sécurité multiniveau :** La combinaison des NSG (filtrage par port/IP) et des ASG (filtrage logique) renforce la sécurité.
* **Résolution de noms :** Les zones privées Azure DNS permettent une communication simplifiée entre les ressources internes sans exposition sur l'internet public.

---

##  Nettoyage des ressources
**Objectif :** Supprimer les ressources pour éviter les coûts inutiles.

* **CLI :** `az group delete --name rg1 --no-wait --yes`
><img width="522" height="43" alt="image" src="https://github.com/user-attachments/assets/7ebebb1d-dca4-4667-a209-42279de4abce" />
