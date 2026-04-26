# ☁️ Microsoft Azure Administrator (AZ-104) 

Ce dépôt ne se limite pas à une simple exécution des Labs étape par étape. L’objectif est de réellement comprendre les concepts manipulés, plutôt que de suivre mécaniquement un guide “clic par clic”.

Chaque exercice est donc documenté avec mes propres explications, des ajustements et du contexte supplémentaire pour mieux assimiler les notions clés. Cette approche me permet de construire une compréhension solide et réutilisable des services Azure.

Ce travail s’inscrit directement dans ma préparation à la certification AZ-104 : Microsoft Azure Administrator, avec une volonté de maîtriser les sujets en profondeur plutôt que de viser uniquement la réussite de l’examen.

---

## 📊 Suivi de progression

| Lab | Titre | Statut | Documentation |
| :--- | :--- | :---: | :---: |
| **01** | Gérer les identités Microsoft Entra ID | 🟢 Terminé | [Consulter](./Labs/Lab_01.md) |
| **02a** | Gérer les abonnements et le RBAC | 🟢 Terminé | [Consulter](./Labs/Lab_02a.md) |
| **02b** | Gérer la gouvernance via Azure Policy | 🟢 Terminé | [Consulter](./Labs/Lab_02b.md) |
| **03** | Gérer les ressources à l’aide de templates ARM | 🟢 Terminé | [Consulter](./Labs/Lab_03.md) |
| **04** | Mettre en place un réseau virtuel | 🟢 Terminé | [Consulter](./Labs/Lab_04.md) |
| **05** | Mettre en place la connectivité intersites | 🟢 Terminé | [Consulter](./Labs/Lab_05.md) |
| **06** | Mettre en place la gestion du trafic réseau | 🟢 Terminé | [Consulter](./Labs/Lab_06.md) |
| **07** | Gérer le stockage Azure | 🟢 Terminé | [Consulter](./Labs/Lab_07.md) |
| **08** | Gérer les machines virtuelles | 🟢 Terminé | [Consulter](./Labs/Lab_08.md) |
| **09a** | Mettre en place des applications web | 🟢 Terminé | [Consulter](./Labs/Lab_09a.md) |
| **09b** | Mettre en place Azure Container Instances | 🟢 Terminé | [Consulter](./Labs/Lab_09b.md) |
| **09c** | Déploiement d’Azure Container Apps | ⚪ À faire | [Consulter](./Labs/Lab_09c.md) |
| **10** | Mettre en place la protection des données | ⚪ À faire | [Consulter](./Labs/Lab_10.md) |
| **11** | Supervision et monitoring | ⚪ À faire | [Consulter](./Labs/Lab_11.md) |

---

## 🛠 Outils utilisés
* **Portail Azure :** Administration et configuration graphique.
* **Azure CLI & PowerShell :** Scripting et gestion en ligne de commande.
* **Infrastructure as Code (IaC) :** Utilisation de templates ARM et Bicep.
* **Git & GitHub :** Suivi de version et documentation.

---

## 💰 Gestion des coûts
Pour ce projet, j'utilise une souscription **Pay-As-You-Go**. Afin d'optimiser les coûts, je respecte les principes suivants :
1. **Isolation :** Utilisation d'un Groupe de Ressources dédié par Lab.
2. **Automatisation :** Configuration systématique de l'**Auto-shutdown** sur les VMs.
3. **Nettoyage :** Suppression immédiate de toutes les ressources à la fin de chaque session via Azure CLI.
