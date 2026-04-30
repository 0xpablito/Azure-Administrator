# Lab 11 : Implémenter la surveillance (Monitoring)

## 📝 Description du Lab
Ce lab explore les capacités d'**Azure Monitor** pour assurer la visibilité sur l'état de santé de l'infrastructure. On y apprend à automatiser la détection d'incidents (alertes) et à analyser les données de performance via les journaux (Log Analytics).

## 🚀 Objectifs d'apprentissage
* Déployer une infrastructure de test via un modèle ARM.
* Créer une **Alerte** basée sur des événements du journal d'activité.
* Configurer un **Action Group** pour notifier les administrateurs.
* Gérer les périodes de maintenance via des **Alert Processing Rules**.
* Interroger les données de performance avec des requêtes **KQL**.

---

## 🛠 Étapes réalisées

### Tâche 1 : Provisionnement et configuration de VM Insights
**Objectif :** Déployer la VM de test et activer la collecte de données détaillées.

1. **Déploiement de l'infrastructure :** 
Utilisation du modèle ARM [az104-11-vm-template.json](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/blob/master/Allfiles/Labs/11/az104-11-vm-template.json) pour déployer rapidement une machine virtuelle (`az104-vm0`) ainsi qu'un réseau virtuel (VNet) associé.

<p align="center">
  <img width="719" height="795" src="https://github.com/user-attachments/assets/fe7ab280-6898-4938-aa48-cdab3dc6b8a3" />
</p>

2. **Activation de VM Insights :** 
Une fois la VM déployée, l'option **VM Insights** a été activée sur la ressource `az104-vm0`. Cette étape est cruciale car elle installe l'agent de surveillance nécessaire pour remonter les métriques de santé et de performance vers Azure Monitor.

<p align="center">
  <img width="1268" height="224" src="https://github.com/user-attachments/assets/b2f61f7a-1cdf-46e5-ad56-4dd2068ed88e" />
</p>


---

### Tâche 2 & 3 : Création de l'alerte et du groupe d'action
**Objectif :** Configurer une alerte pour notifier l'équipe IT dès qu'une ressource critique (VM) est supprimée.

1. **Sélection du Signal :** Surveillance du signal `Delete Virtual Machine` au niveau de la souscription afin de détecter toute suppression administrative.

<p align="center">
  <img width="942" height="213" src="https://github.com/user-attachments/assets/733817da-95b2-4951-b752-9ab121b13d1a" />
</p>

2. **Configuration du Groupe d'Action (`AlerteOps`) :** Définition du canal de notification par e-mail.

<p align="center">
  <img width="1885" height="526" src="https://github.com/user-attachments/assets/9d0e8fbb-0ee7-43f3-a9c4-281fb61ed103" />
</p>

3. **Résumé de la Règle :** La règle est configurée avec une sévérité **4 - Verbose** et liée au groupe d'action pour une réactivité maximale.

<p align="center">
  <img width="945" height="768" src="https://github.com/user-attachments/assets/2b7d0bfd-f2b0-49ee-9753-ce689e62e340" />
</p>

---

### Tâche 4 : Test de l'alerte
**Objectif :** Vérifier l'efficacité du système de notification en conditions réelles.

1. **Action de suppression :** Suppression forcée de la VM `az104-11-vm0`.

<p align="center">
  <img width="847" height="866" src="https://github.com/user-attachments/assets/7e750a49-ccc6-4a0d-932d-ab777a5108af" />
</p>

2. **Validation dans le Journal d'activité :** On confirme que l'événement de suppression a bien été intercepté par Azure.

<p align="center">
  <img width="1893" height="389" src="https://github.com/user-attachments/assets/ce1a42ad-8bd4-485e-a7fc-2d6c8611075d" />
</p>


---

### Tâche 5 : Règle de traitement des alertes (Maintenance)
**Objectif :** Gérer les alertes pendant les interventions prévues (Alert Processing Rules).

* **Configuration :** Utilisation de l'option `Suppress notifications`.
* **Planification :** Définition d'une fenêtre de maintenance (ex: 22h00 - 07h00). Les alertes restent visibles dans le portail mais n'envoient plus d'e-mails.

<p align="center">
  <img width="870" height="720" src="https://github.com/user-attachments/assets/f4bc09c3-f418-43d2-802c-be1ea1f16ca1" />
</p>


---

### Tâche 6 : Analyse de données avec KQL
**Objectif :** Explorer et interroger les métriques brutes via le Kusto Query Language (KQL).

Le langage KQL permet d'interroger la base de données de logs.

`InsightsMetrics
 | where TimeGenerated > ago(1h)
 | where Name == "UtilizationPercentage"
 | summarize avg(Val) by bin(TimeGenerated, 5m), Computer
 | render timechart`

><img width="1889" height="815" alt="image" src="https://github.com/user-attachments/assets/a19c43e0-4168-4c27-a159-7ed764b3400d" />
