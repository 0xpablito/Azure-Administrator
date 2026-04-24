# Lab 08 : Gérer les machines virtuelles Azure

## 📝 Description du Lab
Ce lab se concentre sur le déploiement et la gestion des machines virtuelles (VM). L'objectif est de comprendre comment assurer la haute disponibilité via les **Zones de Disponibilité**, comment modifier les ressources d'une VM existante (Scaling vertical) et comment automatiser la montée en charge avec les **Virtual Machine Scale Sets (VMSS)** (Scaling horizontal).

## 🚀 Objectifs d'apprentissage
* Déployer des VMs résilientes aux pannes de zone.
* Gérer le redimensionnement du calcul (SKU) et du stockage (Disques).
* Configurer un **VM Scale Set** avec équilibrage de charge.
* Implémenter des règles d'**Autoscale** basées sur des métriques de performance.
* Utiliser PowerShell et l'interface CLI pour l'automatisation.

---

## 🛠 Étapes réalisées

### Tâche 1 : Déployer des VMs résilientes aux zones
**Objectif :** Atteindre un niveau de disponibilité maximal en répartissant les ressources sur plusieurs centres de données physiques.

1. **Configuration Basics :**
    * **VMs :** `VM1` et `VM2`.
    * **Zones de disponibilité :** Sélection des Zones 1 et 2 (Central US).
    * **Image :** Windows Server 2025 Datacenter.

><img width="538" height="646" alt="image" src="https://github.com/user-attachments/assets/1fe2359d-ccea-401c-82c4-e6c1ba10661d" />


> **Note :** Note : Cette configuration déploiera deux machines virtuelles dans la région choisie, avec une machine dans chaque zone de disponibilité. Cela permet d’atteindre un SLA de disponibilité de 99,99 %, car au moins deux machines sont réparties sur au minimum deux zones distinctes. Même dans le cas où une seule machine virtuelle serait nécessaire, il est recommandé comme bonne pratique d’en déployer une seconde dans une autre zone afin d’assurer une meilleure résilience et continuité de service.

---

### Tâche 2 : Gestion du scaling (Vertical & Stockage)
**Objectif :** Ajuster les ressources d'une VM existante en fonction des besoins de performance.

1. **Scaling Vertical (Resize) :** Passage de la VM `VM1` vers un nouveau SKU (`D2ds_v4`). 
   * *Conséquence :* La VM redémarre pour appliquer le nouveau matériel virtuel.

><img width="1883" height="138" alt="image" src="https://github.com/user-attachments/assets/c4e7696f-8127-47e6-bfc2-6e138d762dff" />


2. **Modification du type de disque :**
   * Pour améliorer les performances, le disque a été **détaché** temporairement afin de modifier son type vers **Standard SSD**.


><img width="1877" height="190" alt="image" src="https://github.com/user-attachments/assets/0f6a0ce1-7bab-4453-b325-daff0215691f" />
  
3. **Ré-attachement à la VM :**
    * Une fois le disque mis à jour en SSD, il a été ré-attaché à `VM1`. 
    * Cette manipulation démontre qu'Azure permet de modifier dynamiquement les performances du stockage sans perdre de données.

><img width="835" height="203" alt="image" src="https://github.com/user-attachments/assets/996df3ca-1325-40c6-9b05-45d5fd3820c9" />


>Note : Pour modifier certaines propriétés d’un disque, comme le type de stockage, il est nécessaire de le détacher au préalable de la machine virtuelle. Cette opération le rend modifiable sans le supprimer, puis il peut être rattaché une fois les changements appliqués.

---

### Tâche 3 : Configuration d'un Virtual Machine Scale Set (VMSS)
**Objectif :** Créer une flotte de VMs identiques capables de s'adapter automatiquement à la charge.

1. Configuration de l'infrastructure réseau (VNet & Subnet)
Le VMSS nécessite un réseau virtuel robuste. Un VNet spécifique `vmss-vnet` a été créé avec une plage d'adressage dédiée pour isoler le trafic de la flotte.
* **VNet :** `vmss-vnet` (Espace d'adressage : `10.82.0.0/20`).
* **Sous-réseau :** `subnet0` (`10.82.0.0/24`).

<p align="center">
  <img src="https://github.com/user-attachments/assets/409ff1c6-b088-4619-9d33-bed2dab65822" width="48%" />
  <img src="https://github.com/user-attachments/assets/c0f212f7-1a62-48df-a760-4a567c92fb9a" width="48%" />
</p>

2. Sécurisation des flux (NSG)
Pour permettre l'accès aux applications web hébergées sur les instances du Scale Set, une règle de sécurité entrante a été ajoutée au Groupe de Sécurité Réseau (**NSG**).
* **Règle :** `allow-http` sur le port **80**.
* **Action :** Allow (Autoriser).

><img width="569" height="858" alt="image" src="https://github.com/user-attachments/assets/d4a463d9-12b9-465f-ace8-5090c551b77d" />

3. Équilibrage de charge (Load Balancer)
Afin de distribuer équitablement le trafic entrant entre toutes les instances de la flotte, un **Azure Load Balancer** (`vmss-lb`) a été intégré directement lors de la création du VMSS.

><img width="832" height="316" alt="image" src="https://github.com/user-attachments/assets/bf9b65d7-e754-4221-8a92-8fad38f15c5c" />

4. Paramètres de déploiement et Validation
Le VMSS est configuré en mode d'orchestration **Uniform**, réparti sur **3 Zones de disponibilité** (1, 2 et 3) pour une résilience maximale contre les pannes de datacenter.

><img width="628" height="662" alt="image" src="https://github.com/user-attachments/assets/ed5e0bdb-5498-4489-8ca2-e425fb54e6db" />


---

### Tâche 4 : Mise en œuvre de l'Autoscale (Scaling Horizontal)
**Objectif :** Définir des règles automatiques pour ajouter ou supprimer des instances.

1. Règle de "Scale Out" (Augmentation de la capacité)
Cette règle permet de répondre à un pic d'activité. Si la charge de travail augmente, le système ajoute automatiquement des instances pour maintenir les performances.
* **Métrique :** Pourcentage CPU (Host).
* **Condition :** Moyenne supérieure à **70%** pendant une durée de **10 minutes**.
* **Action :** Augmenter le nombre d'instances de **50%**.

><img width="570" height="862" alt="image" src="https://github.com/user-attachments/assets/54492c71-87c6-4e90-9054-bc3162d389cc" />

2. Règle de "Scale In" (Réduction de la capacité)
Pour optimiser les coûts, cette règle supprime les instances inutiles lorsque la demande diminue (par exemple, la nuit ou le week-end).
* **Condition :** Moyenne CPU inférieure à **30%** pendant **10 minutes**.
* **Action :** Diminuer le nombre d'instances de **50%**.

><img width="578" height="871" alt="image" src="https://github.com/user-attachments/assets/d68484d9-6896-42e9-8b8b-a8b37d09c7fd" />

3. Définition des limites d'instances
Afin de garder le contrôle sur la facturation et garantir une disponibilité minimale, des bornes de sécurité sont définies :
* **Minimum :** 2 instances (pour assurer la haute disponibilité en permanence).
* **Maximum :** 10 instances (pour plafonner les coûts, même en cas de charge extrême).
* **Default :** 2 instances

><img src="https://github.com/user-attachments/assets/d87d8788-0027-4805-9a74-bc973726a835" />

4. Surveillance du parc d'instances
Une fois les règles appliquées, l'onglet **Instances** permet de monitorer en temps réel le nombre de machines virtuelles actives et leur état de santé (Health state).

><img width="1372" height="283" alt="image" src="https://github.com/user-attachments/assets/07eee710-95e4-45d0-b951-41b479165103" />

---

### Tâche 5 : Automatisation (PowerShell)
**Objectif :** Utiliser les outils en ligne de commande pour la création rapide de ressources.

1. **Déploiement de la machine virtuelle avec Powershell :**
    * Utilisation de la commande `New-AzVm` dans le Cloud Shell pour créer une VM nommée `myPSVM`.
    * Cette méthode permet de définir toutes les options (Région, Zone, Taille, Image) en une seule ligne de commande.

><img width="407" height="256" alt="image" src="https://github.com/user-attachments/assets/e1b88d40-7a57-4a39-afcf-b0e389057db3" />

2. **Vérification du statut du déploiement :**
    * Utilisation de la commande `Get-AzVm` pour lister les ressources et confirmer que la machine est bien présente et fonctionnelle.

><img width="964" height="161" alt="image" src="https://github.com/user-attachments/assets/d8f38026-c91e-4fb5-918b-551a732935f5" />

3. **Arrêt et Libération (Deallocate) :**
    * Exécution de la commande `Stop-AzVm` pour arrêter la machine.
    * Une seconde vérification avec `Get-AzVm -Status` confirme l'état **VM deallocated**.

><img width="1007" height="428" alt="image" src="https://github.com/user-attachments/assets/179e25e5-397a-4668-91aa-7ce3f6413f12" />

---

##  Conclusion et points clés

* **SLA de disponibilité :** 99.9% pour une VM seule (Premium SSD), 99.95% en Availability Set, et **99.99%** en Availability Zones.
* **Vertical vs Horizontal :** * *Vertical :* Changer la taille d'une VM (nécessite un redémarrage).
    * *Horizontal :* Ajouter des VMs identiques via VMSS (sans interruption).
* **Autoscale :** Permet une optimisation des coûts en ne payant que pour les ressources nécessaires lors des pics de charge.

---

##  Nettoyage des ressources
`Remove-AzResourceGroup -Name "az104-rg8" -Force`
><img width="497" height="111" alt="image" src="https://github.com/user-attachments/assets/28aa8f7e-9d77-4317-a272-0932acad7092" />
