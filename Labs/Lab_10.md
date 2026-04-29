# Lab 10 : Mise en œuvre de la protection des données

## 📝 Description du Lab
Ce lab est dédié à la sécurisation des données et à la résilience des infrastructures. L'objectif est de mettre en place une stratégie de sauvegarde granulaire avec **Azure Backup** et une stratégie de reprise après sinistre (Disaster Recovery) avec **Azure Site Recovery**.

## 🚀 Objectifs d'apprentissage
* Déployer une infrastructure via des modèles ARM (JSON).
* Configurer un **Recovery Services Vault** (RSV) pour centraliser les sauvegardes.
* Définir des politiques de rétention et de sauvegarde (Backup Policies).
* Monitorer les jobs de sauvegarde via des comptes de stockage et des journaux de diagnostic.
* Configurer la **réplication inter-régionale** pour la haute résilience (Site Recovery).

---

## 🛠 Étapes réalisées

### Tâche 1 : Provisionnement de l'infrastructure par modèle
**Objectif :** Utiliser l'automatisation pour déployer rapidement une VM de test.

1. **Préparation :** Téléchargement des fichiers JSON (modèle et paramètres) depuis le dépôt officiel : [Modèles Lab 10](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/tree/master/Allfiles/Labs/10).
2. **Déploiement :** Utilisation de l'option "Deploy a custom template" pour provisionner la VM de test. La configuration nécessite l'importation des fichiers `az104-10-vms-edge-template` et `az104-10-vms-edge-parameters` ainsi que l'ajustement des paramètres d'authentification (utilisateur et mot de passe).

<p align="center">
  <img width="417" height="489" src="https://github.com/user-attachments/assets/fe9bee31-1a41-404e-81bc-45f65b0e0c35" />
</p>


---

### Tâche 2 : Configuration du Recovery Services Vault
**Objectif :** Créer le coffre-fort numérique qui stockera les points de restauration.

1. **Création du Vault :** Nommé `vault1`.

<p align="center">
  <img width="688" height="527" src="https://github.com/user-attachments/assets/b9d1a792-a533-49dc-9cce-cdac16a915ac" />
</p>

2. **Redondance du stockage :** Vérification de la configuration de réplication. Le choix du **Geo-redundant (GRS)** assure que les données sont copiées dans une région distante pour une protection maximale.

<p align="center">
  <img width="1417" height="453" src="https://github.com/user-attachments/assets/0fb5d3cd-4562-41bf-9e05-09f8120d842c" />
</p>

> **Note sur la redondance :** Le choix du **Geo-redundant (GRS)** permet de répliquer les sauvegardes dans une région Azure appairée. En cas de sinistre majeur rendant la région principale indisponible, vos données restent accessibles. Les autres options sont le **LRS** (local, moins cher mais moins sûr) et le **ZRS** (répartition sur plusieurs zones d'une même région).

3. **Sécurité (Soft Delete) :** Configuration de la suppression réversible. Ce paramètre garantit que les données de sauvegarde supprimées sont conservées pendant 14 jours, protégeant ainsi contre les erreurs de manipulation ou les cyberattaques.

<p align="center">
  <img width="1425" height="453" src="https://github.com/user-attachments/assets/bb1bf2be-d1a1-497b-83a3-2540e50e710d" />
</p>

> **Note sur le Soft Delete :** Cette sécurité conserve les données supprimées pendant **14 jours** pour contrer les suppressions accidentelles ou les ransomwares. Il est important de savoir que cette durée de rétention est **modifiable** dans les paramètres du Vault pour s'adapter à vos politiques de conformité.


---

### Tâche 3 : Configuration de la sauvegarde au niveau VM
**Objectif :** Planifier la protection automatique de la machine virtuelle.

1. **Politique de sauvegarde :**
    * **Fréquence :** Quotidienne à minuit.
    * **Rétention :** Conservation des snapshots d'instantanés pendant 2 jours pour permettre une restauration immédiate sans latence de transfert depuis le coffre.

<p align="center">
  <img width="825" height="850" src="https://github.com/user-attachments/assets/95a50dc4-43f6-4910-b61f-4ac18d15687e" />
</p>

2. **Ajout de la ressource :** Sélection et ajout de la machine virtuelle `az104-10-vm0` dans les éléments à protéger du vault.

<p align="center">
  <img width="1273" height="361" src="https://github.com/user-attachments/assets/31923860-0d31-452f-a970-2e2882738a92" />
</p>

3. **Exécution de la sauvegarde :** Déclenchement manuel via l'option **Backup Now**. 
    * On observe que le **Backup Pre-check** est au statut "Passed", ce qui valide l'état de l'agent Azure sur la VM et la connectivité avec le Vault.
    * Comme le statut initial est "Initial backup pending", l'option **Backup now** est utilisée pour forcer la création du premier point de récupération immédiatement.

<p align="center">
  <img width="1588" height="437" src="https://github.com/user-attachments/assets/91f605b5-76ca-4481-a19c-4fe952ec330a" />
</p>

---

### Tâche 4 : Monitoring de Azure Backup
**Objectif :** Assurer la traçabilité et l'alerte en cas d'échec.

1. **Infrastructure de monitoring :** Création d'un compte de stockage (`comptestockagetest10`) dédié à l'archivage des données de diagnostic.

<p align="center">
  <img width="686" height="808" src="https://github.com/user-attachments/assets/86680305-4542-4c33-949e-40f921194256" />
</p>

2. **Diagnostic Settings :** Configuration du Vault pour exporter les logs (Jobs, Alerts, Reporting) et les métriques vers ce compte de stockage. Cette méthode permet de conserver un historique long terme et de faciliter l'audit des jobs de sauvegarde.

<p align="center">
  <img width="1145" height="762" src="https://github.com/user-attachments/assets/f1cc968b-b4e2-429f-a358-d63e1571d3fe" />
</p>

3. **Suivi :** Vérification du statut final dans la section "Backup Jobs" pour confirmer le succès du transfert initial de la machine virtuelle.

<p align="center">
  <img width="1288" height="550" src="https://github.com/user-attachments/assets/60f3da5e-f8e8-4445-b3ee-0deebca2fc75" />
</p>

---

### Tâche 5 : Réplication et Disaster Recovery (Site Recovery)
**Objectif :** Préparer la bascule de la VM vers une autre région géographique.

1. **Création du second coffre-fort :** Provisionnement de `vault2` dans la région **North Europe**. Ce second Vault servira de cible pour la réplication des données provenant de la région source.

<p align="center">
  <img width="669" height="664" src="https://github.com/user-attachments/assets/95457fe5-098f-4a6d-957c-9d30c8884251" />
</p>

2. **Configuration du compte d'automatisation :** Création d'un **compte d'automatisation (Automation Account)** pour orchestrer les mises à jour et les tâches de maintenance sur la VM répliquée.

<p align="center">
  <img width="927" height="699" src="https://github.com/user-attachments/assets/32bbe5f8-e3bf-41c1-baba-27cf083614e8" />
</p>

3. **Activation de la réplication :** Configuration des paramètres cibles (réseau virtuel, stockage et cache) pour lier la VM vers la région **North Europe**.

<p align="center">
  <img width="1130" height="779" src="https://github.com/user-attachments/assets/aba2ab33-83a8-4e1a-a97b-e3533e1a08b9" />
</p>

4. **Vérification du statut de protection :** Une fois la synchronisation initiale terminée, la machine virtuelle apparaît avec l'état **Healthy** et le statut **Protected**. Elle est désormais prête pour un basculement (failover) en cas de panne de la région principale.

<p align="center">
  <img width="1892" height="483" src="https://github.com/user-attachments/assets/8844957d-685f-4107-b71f-edcf0b9c956d" />
</p>




---

##  Conclusion et points clés

* **Backup vs Site Recovery :** Le backup sert à récupérer des données passées (Rétention), le Site Recovery sert à maintenir la disponibilité du service (Réplication).
* **GRS (Geo-Redundancy) :** Essentiel pour la conformité, il assure que même si un datacenter est détruit, les données de sauvegarde existent ailleurs.
* **Orchestration :** Azure gère automatiquement la création des ressources cibles (disques, réseaux) dans la région de destination lors de la réplication.

---

##  Nettoyage des ressources

Le lab ayant nécessité la création de plusieurs groupes de ressources, j'ai utilisé PowerShell pour automatiser la suppression en masse afin de gagner du temps : : `$rgs = @("AzureBackupRG_francecentral_1", "NetworkWatcherRG", "rg10-1", "rg10-1-asr", "rg10-2", "Site-recovery-vault-RG")
$rgs | ForEach-Object { Remove-AzResourceGroup -Name $_ -Force -AsJob }`. 

><img width="1639" height="785" alt="image" src="https://github.com/user-attachments/assets/93513b9c-0e37-4e8f-b54b-46845264f11f" />

En suivant l'avancée avec la commande Get-Job, j'ai remarqué que la suppression du groupe rg10-1 était en statut Failed. Ce blocage est dû à deux sécurités natives d'Azure :

Verrou de ressource (Lock) : Azure Site Recovery applique automatiquement un verrou sur la VM répliquée pour empêcher sa suppression accidentelle. Il est impératif de le supprimer manuellement.

Données protégées dans le Vault : Un coffre-fort ne peut pas être supprimé s'il contient encore des points de récupération.

><img width="1315" height="321" alt="image" src="https://github.com/user-attachments/assets/6d8680cf-4e8f-4233-a4ce-df62403533cc" />

><img width="866" height="833" alt="image" src="https://github.com/user-attachments/assets/94d82aa0-0754-4754-8bac-8af34b60df17" />

Une fois le verrou levé et les données du coffre-fort effacées, la suppression globale des groupes de ressources a pu être finalisée avec succès.

>
