# ☁️ Microsoft Azure Administrator (AZ-104) 

Ce dépôt contient ma documentation personnelle, mes configurations et mes notes d'apprentissage pour la préparation de la certification **AZ-104 : Microsoft Azure Administrator**. 

L'objectif est de documenter la mise en pratique des concepts théoriques via les exercices officiels du programme Microsoft.

---

## 📊 Suivi de progression

| Lab | Titre | Statut | Documentation |
| :--- | :--- | :---: | :---: |
| **01** | Manage Microsoft Entra ID Identities | 🟢 Terminé | [Consulter](./Labs/LAB_01.md) |
| **02a** | Manage Subscriptions and RBAC | 🟢 Terminé | [Consulter](./Labs/LAB_02a.md) |
| **02b** | Manage Governance via Azure Policy | ⚪ À faire | [Consulter](./Labs/LAB_02b.md) |
| **03** | Manage Resources by Using ARM Templates | ⚪ À faire | [Consulter](./Labs/LAB_03b.md) |
| **04** | Implement Virtual Networking | ⚪ À faire | [Consulter](./Labs/LAB_04.md) |
| **05** | Implement Intersite Connectivity | ⚪ À faire | [Consulter](./Labs/LAB_05.md) |
| **06** | Implement Network Traffic Management | ⚪ À faire | [Consulter](./Labs/LAB_06.md) |
| **07** | Manage Azure Storage | ⚪ À faire | [Consulter](./Labs/LAB_07.md) |
| **08** | Manage Virtual Machines | ⚪ À faire | [Consulter](./Labs/LAB_08.md) |
| **09a** | Implement Web Apps | ⚪ À faire | [Consulter](./Labs/LAB_09.md) |
| **09b** | Implement Azure Container Instances | ⚪ À faire | [Consulter](./Labs/LAB_09b.md) |
| **09c** | Implement Azure Container Apps | ⚪ À faire | [Consulter](./Labs/LAB_09c.md) |
| **10** | Implement Data Protection | ⚪ À faire | [Consulter](./Labs/LAB_10.md) |
| **11** | Implement Monitoring | ⚪ À faire | [Consulter](./Labs/LAB_11.md) |

---

## 🛠 Outils utilisés
* **Portail Azure :** Administration et configuration graphique.
* **Azure CLI & PowerShell :** Scripting et gestion en ligne de commande.
* **Infrastructure as Code (IaC) :** Utilisation de templates ARM et Bicep.
* **Git & GitHub :** Suivi de version et documentation.

---

## 💰 Gestion des coûts
Pour ce projet, j'utilise une souscription **Pay-As-You-Go**. Afin d'optimiser les coûts, je respecte les principes suivants :
1. **Isolation :** Utilisation d'un Groupe de Ressources dédié par lab.
2. **Automatisation :** Configuration systématique de l'**Auto-shutdown** sur les VMs.
3. **Nettoyage :** Suppression immédiate de toutes les ressources à la fin de chaque session via Azure CLI.
