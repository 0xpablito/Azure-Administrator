# Lab 09b : Implémenter Azure Container Instances (ACI)

## 📝 Description du Lab
Ce lab porte sur le déploiement rapide d'applications conteneurisées à l'aide d'**Azure Container Instances**. L'objectif est de comprendre comment exécuter des conteneurs Docker de manière isolée et sans serveur (serverless), tout en les rendant accessibles via un nom de domaine (FQDN).

## 🚀 Objectifs d'apprentissage
* Déployer un conteneur à partir d'une image Docker (MCR - Microsoft Container Registry).
* Configurer l'accès réseau public via un **DNS Name Label**.
* Vérifier le bon fonctionnement via le navigateur et consulter les **Logs** du conteneur.

---

## 🛠 Étapes réalisées

### Tâche 1 : Déployer une instance de conteneur
**Objectif :** Lancer une application web isolée à partir d'une image pré-existante.

1. **Configuration Basics :**
    * **Nom du conteneur :** `c1`.
    * **Source de l'image :** Quickstart images (`aci-helloworld`).
    * **Région :** France Central.
2. **Configuration Réseau :**
    * Attribution d'un **DNS name label** unique (`labpablo`)

><img width="562" height="635" alt="image" src="https://github.com/user-attachments/assets/85de0610-36ff-4349-af0e-d61190de44af" />

---

### Tâche 2 : Tester et vérifier le déploiement
**Objectif :** Valider l'accessibilité de l'application et inspecter son activité.

1. **Test de connectivité :**
    * Une fois le statut à **Running**, récupération du FQDN.

><img width="1605" height="383" alt="image" src="https://github.com/user-attachments/assets/0243f8ce-6cf9-4060-af7e-cec3732a0715" />

2. **Test de connectivité :**
    * Navigation vers l'URL dans un navigateur : la page "Welcome to Azure Container Instance" s'affiche correctement.
  
><img width="994" height="418" alt="image" src="https://github.com/user-attachments/assets/f2c4f48c-e6f2-4cec-8bd2-08d5055b2b76" />


2. **Inspection des Logs :**
    * Consultation de la section **Containers > Logs** dans le portail Azure.
    * Les entrées affichent les requêtes **HTTP GET** générées lors de l'actualisation de la page. Cela confirme que le conteneur reçoit et traite bien le trafic.

><img width="1900" height="658" alt="image" src="https://github.com/user-attachments/assets/5dd5ebad-8512-40ef-b69b-cb7d3c11641e" />


---

##  Conclusion et points clés

* **Simplicité :** ACI est la méthode la plus rapide pour lancer un conteneur sur Azure. Il n'y a aucune infrastructure à gérer.
* **Usage :** Idéal pour des tâches éphémères, des scripts d'automatisation ou des tests rapides.
* **Accessibilité :** Le DNS Name Label permet d'exposer facilement un service sur Internet sans configurer manuellement de Load Balancer ou d'IP publique complexe.

---

## Nettoyage des ressources

`Remove-AzResourceGroup -Name rg2 -Force`

><img width="459" height="50" alt="image" src="https://github.com/user-attachments/assets/ce01bb58-0b08-4c29-af27-20a7952ca06a" />
