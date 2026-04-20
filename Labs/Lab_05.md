<img width="846" height="400" alt="image" src="https://github.com/user-attachments/assets/0a1f6332-3cc1-4bb6-a418-5cccd4694702" /># Lab 05 : Implémenter la connectivité entre sites (VNet Peering)

## 📝 Description du Lab
Ce lab se concentre sur la communication entre réseaux virtuels isolés. L'objectif est de mettre en place un **VNet Peering** pour permettre à deux réseaux distincts de communiquer via le backbone Microsoft, puis de manipuler le routage avec des **User Defined Routes (UDR)**.

## 🚀 Objectifs d'apprentissage
* Créer des machines virtuelles dans des réseaux virtuels séparés.
* Configurer et valider un appairage de réseaux virtuels (**VNet Peering**).
* Tester la connectivité inter-VNet via **Network Watcher** et PowerShell.
* Créer des routes personnalisées pour contrôler les flux de trafic.

---

## 🛠 Étapes réalisées

### Tâche 1 : Créer une première machine virtuelle avec son réseau
**Objectif :** Déployer une machine virtuelle ainsi qu'un réseasu virtuel

1. Création de la machine virtuelle **VMServices** dans un nouveau groupe de ressources `rg1` :
    * **Région :** France Central
    * **Image :** Windows Server 2025 Datacenter
    * **Taille :** Standard_D2s_v3
    * **Authentification :** localadmin / [Mot de passe généré avec gestionnaire de mot de passe]
    * **Ports entrants :** Aucun

2. Création du réseau virtuel **VMServices-vnet** :
    * **Espace d'adressage :** `10.0.0.0/16`
    * **Sous-réseau (Services) :** `10.0.0.0/24`

3. Dans l'onglet **Monitoring**, désactivation des diagnostics de démarrage (*Boot diagnostics*) pour optimiser les performances de déploiement.
><img width="564" height="864" alt="image" src="https://github.com/user-attachments/assets/9da20818-66e4-45d1-930e-e61c02999e9d" />

### Tâche 2 : Créer une seconde machine virtuelle avec son réseau
**Objectif :** Déployer une deuxième machine virtuelle ainsi qu'un réseasu virtuel différent poour la suite des étapes.

1. Création de la machine virtuelle **VMProduction** dans le groupe de ressources `rg1` :
    * **Région :** France Central
    * **Image :** Windows Server 2025 Datacenter
    * **Taille :** Standard_D2s_v3
    * **Authentification :** localadmin / [Mot de passe généré avec gestionnaire de mot de passe]
    * **Ports entrants :** Aucun

2. Création du réseau virtuel **VMProduction-vnet** :
    * **Espace d'adressage :** `172.16.0.0/16`
    * **Sous-réseau (Services) :** `172.16.0.0/24`
><img width="495" height="874" alt="image" src="https://github.com/user-attachments/assets/71a57aaf-e28f-4320-9c8f-d486091411cf" />

> **Note :** L'utilisation de plages IP distinctes (`10.0.x.x` vs `172.16.x.x`) est impérative pour permettre un peering ultérieur. Azure ne peut pas connecter deux réseaux dont les adresses se chevauchent.
> 
### Tâche 3 : Tester la connectivité entre les VM
**Objectif :** Vérifier que les réseaux sont bien isolés par défaut.

1. Utilisation de l'outil **Connection troubleshoot** de Network Watcher.
2. Test entre `VMServices` (Source) et `VMProduction` (Destination) sur le port **3389**.

><img width="897" height="831" alt="image" src="https://github.com/user-attachments/assets/9b456034-5c1e-4fdd-a861-716ddeb80096" />

3. **Résultat :** Le statut est **Unreachable**.

><img width="846" height="400" alt="image" src="https://github.com/user-attachments/assets/99854f3e-c6c4-427b-9ec0-a4626405456c" />

> Note : Ce résultat confirme que sans passerelle ou peering, le trafic entre deux réseaux virtuels Azure est impossible, garantissant une isolation totale des environnements.

### Tâche 4 : Configurer le peering entre les deux réseau virtuels
**Objectif :** Établir une connexion bidirectionnelle entre les deux réseaux virtuels.

1. Depuis le réseau `VMProduction-vnet`, ajouter un Peering avec les paramètres suivants :
    * **Noms des liens :** `ProductionVNET_to_ServicesVNET` et `ServicesVNET_to_ProductionVNET`.
    * **Réseau distant :** `VMServices-vnet`.
    * **Accès réseau :** Autoriser l'accès et le trafic transféré pour les deux liens.
> **Point Clé :** Le peering Azure est bidirectionnel. Lors de la création, Azure crée en réalité deux liens de peering (un pour chaque direction) pour assurer que la communication peut être initiée des deux côtés.

><img width="1790" height="619" alt="image" src="https://github.com/user-attachments/assets/b7879b00-5a21-447c-9e49-02ce8d273275" />

### Tâche 5 : Tester la connectivité entre les deux VM depuis Azure PowerShell
**Objectif :** Vérifier que le peering est effectif et permet aux VM de communiquer entre elles.

1. Depuis la VM **VMServices**, exécuter la commande `Test-NetConnection 172.16.0.4 -port 3389` afin de tester la connexion vers l'adresse IP privée de la VM **VMProduction**.
><img width="1766" height="584" alt="image" src="https://github.com/user-attachments/assets/6408b50c-97f2-42d3-b68a-2f46f51fa958" />

### Tâche 6 : Créer une route personnalisée
**Objectif :** Contrôler le flux réseau entre le sous-réseau périmétrique et le réseau interne en forçant le passage par une appliance virtuelle (NVA).

1. **Création du sous-réseau périmétrique :**
    * Dans le réseau virtuel **VMServices-vnet**, ajouter un nouveau sous-réseau nommé `perimetre` avec la plage d'adresses `10.0.1.0/24`.
><img width="1692" height="406" alt="image" src="https://github.com/user-attachments/assets/0e42ded5-5e66-4292-8773-2e831a515aa3" />

2. **Création de la table de routage :**
    * Rechercher **Tables de routage** dans le portail Azure et cliquer sur **Créer**.
    * **Nom :** `rt_Services`
    * **Propager les routes de la passerelle :** Non
    * **Groupe de ressources :** `rg1`

><img width="417" height="335" alt="image" src="https://github.com/user-attachments/assets/1e1f1775-d65b-478e-8ed3-b098ceba126b" />

3. **Configuration de la route personnalisée :**
    * Dans la ressource `rt_Services`, accéder à la section **Routes** et cliquer sur **Ajouter** :
        * **Nom de la route :** `PerimetreServices`
        * **Type de destination :** Adresses IP
        * **Adresses IP de destination :** `10.0.0.0/16` (le réseau interne)
        * **Type de saut suivant :** Appliance virtuelle (*Virtual appliance*)
        * **Adresse du prochain saut :** `10.0.1.7` (adresse de la future NVA)

><img width="1905" height="626" alt="image" src="https://github.com/user-attachments/assets/f9ebe7eb-056b-42ec-912d-16c7917ed713" />

4. **Association de la route :**
    * Dans la table de routage, aller dans **Sous-réseaux** et cliquer sur **Associer**.
    * Sélectionner le réseau `VMServices-vnet` et le sous-réseau `perimeter`.
><img width="1861" height="405" alt="image" src="https://github.com/user-attachments/assets/23bc6990-5758-4ffb-8345-2a3c5783c760" />

> **Note :** Cette manipulation permet de créer une "User Defined Route" (UDR). Elle force tout le trafic sortant du sous-réseau périmétrique (DMZ) à passer par une appliance de sécurité (NVA) avant d'atteindre les services centraux, garantissant ainsi un contrôle total sur les flux inter-réseaux.

## Conclusion et points clés

* **Connectivité inter-VNet :** Le **VNet Peering** est la solution la plus performante pour connecter des réseaux isolés, permettant une communication à faible latence via le backbone privé de Microsoft.
* **Maîtrise du routage (UDR) :** La création de routes personnalisées (**User Defined Routes**) est indispensable pour forcer le trafic à passer par des équipements de sécurité (NVA) plutôt que d'utiliser le routage par défaut.
* **Diagnostic réseau :** L'utilisation d'outils comme **Network Watcher** est indispensable pour valider les flux et dépanner les problèmes de connectivité dans des architectures hybrides ou segmentées.

##  Nettoyage des ressources
**Objectif :** Supprimer les ressources pour éviter les coûts inutiles.

Suppression manuelle du groupe de ressources **rg1** via le portail Azure pour stopper immédiatement la facturation.
><img width="1898" height="875" alt="image" src="https://github.com/user-attachments/assets/30da1b83-8f04-4482-8bc9-fc97dd33a5fe" />
