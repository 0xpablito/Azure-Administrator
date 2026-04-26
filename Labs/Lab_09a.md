# Lab 09a : Implémenter des Web Apps Azure

## 📝 Description du Lab
Ce lab explore les services PaaS d'Azure, spécifiquement **Azure App Service**. L'objectif est de déployer une application PHP depuis un dépôt GitHub externe, de gérer les cycles de mise à jour via des **slots de déploiement** (Staging vs Production) et de configurer l'**Autoscaling**.

## 🚀 Objectifs d'apprentissage
* Créer une Web App Azure sous Linux avec un plan App Service Premium.
* Configurer des **Deployment Slots** pour tester les versions avant mise en production.
* Automatiser le déploiement via un dépôt **Git externe**.
* Réaliser un **Swap** de slots sans interruption de service.
* Configurer et tester l'**Autoscaling automatique** avec des tests de charge.

### Tâche 1 : Créer et configurer une Web App
**Objectif :** Provisionner l'environnement d'hébergement.

1. **Paramètres de base :**
    * **Nom :** azlab | **Runtime Stack :** PHP 8.2 | **OS :** Linux.
    * **Plan App Service :** Premium V3 (P1V3), nécessaire pour supporter les slots et l'autoscaling.

><img width="725" height="867" alt="image" src="https://github.com/user-attachments/assets/b1f672eb-354c-4711-83f4-c173d27b3101" />

2. **Validation :** Une fois déployée, la Web App affiche la page par défaut d'Azure.

><img width="775" height="748" alt="image" src="https://github.com/user-attachments/assets/6584fafe-cf88-4178-8ef7-ad46e313898a" />

### Tâche 2 & 3 : Slots de déploiement et configuration Git
**Objectif :** Créer un environnement de pré-production (Staging).

1. **Création du Slot :** Ajout d'un slot nommé `staging`. Ce slot possède sa propre URL, distincte de la production.

><img width="1898" height="475" alt="image" src="https://github.com/user-attachments/assets/4997510f-4e8c-4945-afe6-a48ade8d664c" />

2. **Déploiement en staging :**
    * Configuration du **Deployment Center** sur le slot `staging`.
    * Source : **External Git**.
    * URL du dépôt : `https://github.com/Azure-Samples/php-docs-hello-world`.

><img width="1047" height="610" alt="image" src="https://github.com/user-attachments/assets/f307501b-f056-4d06-b177-082b9b516add" />

3. **Résultat :** Le slot de staging affiche maintenant "Hello World", tandis que le slot de production est toujours sur la page par défaut.

><img width="755" height="121" alt="image" src="https://github.com/user-attachments/assets/7b1ca335-ccba-40f9-9cb5-60eaf63c42fb" />

### Tâche 4 : Swap des Slots 
**Objectif :** Basculer le code testé en staging vers la production.

1. **Le Swap :** Exécution du **Swap** entre `staging` et `production`.
2. **Avantage :** Cette opération échange les adresses IP et les configurations. Il n'y a aucun temps d'arrêt pour les utilisateurs.

><img width="582" height="874" alt="image" src="https://github.com/user-attachments/assets/8aa55501-d982-49ac-a48d-bee1e8703393" />

3. **Vérification :** L'URL par défaut (production) affiche désormais "Hello World !".

><img width="740" height="117" alt="image" src="https://github.com/user-attachments/assets/46aa6d93-6f3b-4259-9f7b-d457bc6e9fcd" />

### Tâche 5 : Autoscaling 
**Objectif :** S'assurer que l'application supporte une augmentation du trafic.

1. **Configuration de l'Autoscale :**
    * Mode : **Automatic**.
    * Burst maximum : **2 instances**.

><img width="1178" height="779" alt="image" src="https://github.com/user-attachments/assets/65ae513c-9ee1-4fdb-943d-03192d40f68c" />

2. **Création du test de charge :**
    * Utilisation de l'outil de diagnostic pour créer un **Load Test**. 
    * Cette étape prépare l'environnement pour simuler un afflux d'utilisateurs sur la Web App.

><img width="742" height="532" alt="image" src="https://github.com/user-attachments/assets/93e812f9-9478-4590-8ca8-1315b6476c82" />

3. **Ajout d'une demande HTTP :**
    * Configuration d'une requête HTTP pointant vers l'URL de la ressource.

><img width="838" height="467" alt="image" src="https://github.com/user-attachments/assets/126e539d-f6e6-4eb1-a7c7-f2e4bf99ce6b" />

4. **Analyse des résultats du test :**
    * Une fois le test lancé, le tableau de bord affiche les métriques en temps réel : nombre d'utilisateurs virtuels, temps de réponse et nombre de requêtes par seconde.
    * Ces données permettent de confirmer que l'application reste stable sous pression.
  
><img width="1863" height="548" alt="image" src="https://github.com/user-attachments/assets/aaf44112-1a24-4b9f-9925-4aca4e9b4194" />

> **Note technique :** L'autoscaling automatique est une fonctionnalité puissante du plan Premium qui permet de ne pas se soucier de l'écriture de règles complexes (CPU > X%). Le Load Test vient valider que ce mécanisme se déclenche correctement.

##  Conclusion et points clés

* **Slots de déploiement :** Ils permettent de réduire les risques lors des mises à jour. Si le swap échoue, on peut faire un Rollback immédiat en refaisant un swap.
* **PaaS vs IaaS :** On ne gère pas de VMs, pas de mises à jour système, juste l'application et son plan de service.
* **Flexibilité :** L'autoscaling automatique permet de ne payer pour deux instances que lorsque c'est réellement nécessaire, optimisant ainsi les coûts.

---

##  Nettoyage des ressources

# Suppression du groupe de ressources du Lab 
`Remove-AzResourceGroup -Name "rg1"`
><img width="1896" height="874" alt="image" src="https://github.com/user-attachments/assets/cdd7a65d-e961-46d9-93b5-d4a6dbd883f0" />
