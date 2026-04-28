# Lab 09c : Implémenter Azure Container Apps (ACA)

## 📝 Description du Lab
Ce lab explore le déploiement d'applications conteneurisées via **Azure Container Apps**. Ce service permet d'exécuter des microservices et des applications conteneurisées sur une plateforme serverless bâtie sur Kubernetes, sans avoir à gérer l'infrastructure complexe des clusters (K8s).

## 🚀 Objectifs d'apprentissage
* Créer un **environnement** Container Apps (le conteneur logique pour les ressources).
* Déployer une application conteneurisée à partir d'une image de démarrage rapide.
* Tester l'accessibilité via l'URL d'application générée automatiquement.
* Comprendre la différence entre ACA et AKS (Kubernetes managé).

---

## 🛠 Étapes réalisées

### Tâche 1 : Créer et configurer une Azure Container App
**Objectif :** Mettre en place l'environnement et déployer le premier conteneur.

1. **Création manuelle de l'environnement :**
    * Contrairement à d'autres services, il est nécessaire de définir soi-même un **Container Apps Environment** (`monenv`).
    * Cet environnement sert de frontière logique : il regroupe les journaux (Log Analytics), les configurations réseau et les paramètres de sécurité partagés par tous les conteneurs qui y seront déployés.

2. **Déploiement de l'application :**
    * **Nom de l'app :** `apptest`.
    * **Image :** Utilisation de l'image Quickstart (*Simple hello world container*).
    * **Région :** France Central.

><img width="532" height="611" alt="image" src="https://github.com/user-attachments/assets/13b818ae-0438-46e4-a807-f5f99d6170d4" />

> **Note :** L'environnement Container Apps peut héberger plusieurs applications. Elles peuvent ainsi communiquer entre elles de manière sécurisée et isolée au sein de ce même environnement `monenv`.

---

### Tâche 2 : Tester et vérifier le déploiement
**Objectif :** Accéder à l'application via le point de terminaison public.

1. **Vérification de l'URL :**
    * Une fois le déploiement terminé, Azure fournit une **Application URL** unique.
    * Contrairement à ACI où l'on configure le DNS, ACA génère automatiquement une URL sécurisée (HTTPS).

2. **Test de navigation :**
    * En cliquant sur le lien, la page de confirmation *"Your Azure Container Apps app is live"* s'affiche.

><img width="987" height="746" alt="image" src="https://github.com/user-attachments/assets/ca217eb2-1995-4c30-849f-d26b911e3e1e" />


---

##  Conclusion et points clés

* **Abstraction de Kubernetes :** ACA utilise Kubernetes en arrière-plan, mais masque toute la complexité (pas de gestion de Nodes ou de Control Plane).
* **Évolutivité :** ACA est conçu pour le scaling dynamique (y compris le **scaling vers zéro** quand il n'y a pas de trafic, ce qui permet de ne rien payer).
* **Environnement partagé :** L'environnement créé permet de faire communiquer plusieurs "Container Apps" entre elles très facilement, idéal pour une architecture microservices.

---

##  Nettoyage des ressources

`Remove-AzResourceGroup -Name "rg9" -Force`
><img width="1895" height="847" alt="image" src="https://github.com/user-attachments/assets/45748e11-f819-4855-8e6b-7f5c7374913e" />
