#  Azure Container Instances (ACI) - Serveur Minecraft

Après avoir suivi le cursus Microsoft Learn et complété les labs GitHub de la certification **Microsoft Azure Administrator (AZ-104)**, j'ai souhaité appliquer mes compétences sur un cas d'usage concret et personnel : l'hébergement d'un serveur de jeu Minecraft pour une session de quelques heures.

Ce dépôt a pour but de documenter et de partager ma démarche, mes choix d'architecture ainsi que les étapes de déploiement de ce projet.

---

## Pourquoi le Cloud Azure pour ce projet ?

L'hébergement traditionnel impose souvent la location d'un serveur dédié au mois. L'utilisation d'Azure permet d’éviter une location mensuelle grâce à deux avantages principaux du cloud :
1. **La tarification à l'usage (Pay-As-You-Go)** : Le serveur n'est facturé que lorsqu'il tourne, ce qui est idéal pour un besoin éphémère (une soirée ou un week-end) et permet de maintenir un coût proche de zéro le reste du temps.
2. **La flexibilité des ressources (Scalabilité)** : Possibilité de configurer une machine sur-mesure (CPU/RAM) adaptée exactement au nombre de joueurs, sans gaspillage.

---

##  Objectifs Techniques & Concepts Validés

Ce cas pratique permet de valider plusieurs compétences clés du programme AZ-104 :

*   **Architecture Serverless (CaaS)** : Utilisation d'**Azure Container Instances (ACI)** pour déployer l'application instantanément, sans avoir à créer, sécuriser ni maintenir une machine virtuelle classique (Azure VM).
*   **Conteneurisation & Docker** : Manipulation d'images Docker publiques (`itzg/minecraft-server`) issues de Docker Hub et configuration avancée via l'injection de variables d'environnement.
*   **Optimisation FinOps** : Dimensionnement fin des ressources (Compute/Memory) pour coller au plus juste des besoins de performance tout en maîtrisant les coûts de facturation Azure.
*   **Approche Multi-Interface** : Maîtrise et comparaison des méthodes de déploiement via l'interface graphique (**Portail Azure**) et par automatisation (**Azure CLI** / Cloud Shell).

##  Étape 1 : Le Déploiement Initial via le Portail Azure (GUI)

Pour la première session, l'utilisation de l'interface graphique permet de bien visualiser la structure des ressources et de comprendre l'organisation des paramètres demandés par Azure.

### 1. Configuration
*   **Groupe de ressources** : Création d'un groupe dédié nommé `rg1` pour isoler le projet et pouvoir tout supprimer d'un coup à la fin de la soirée.
*   **Détails de l'instance** :
    *   Nom du conteneur : `serveurdejeu`
    *   Région : `West Europe` (choisie pour minimiser la latence/ping des joueurs).
*   **Source de l'image** : Sélection de *Autre registre* (Other registry) en mode *Public*.
* **Image** : `itzg/minecraft-server`
    >  **Provenance** : C'est une image publique très populaire issue de **Docker Hub**.
    > 
    >  **Rôle** : C'est un package "tout-en-un" prêt à l'emploi. Au lieu de louer un serveur vide et de devoir installer Linux, Java, puis les fichiers du jeu à la main, cette image contient déjà tout le nécessaire : un système **Linux** ultra-léger, l'environnement **Java** optimisé, et un script qui lit nos variables Azure pour configurer automatiquement le serveur dès le démarrage.
* **Taille (Size)** : Configuration sur-mesure avec 4 vCPUs et 8 Go de RAM pour encaisser la charge liée à la génération des cartes de plusieurs joueurs en simultané.
><img width="790" height="784" alt="image" src="https://github.com/user-attachments/assets/ded8d8eb-d158-4b0c-800a-f5d8c17ce4e3" />


### 2. Configuration Réseau (Networking)
*   **Type de réseau** : *Public*.
*   **Ports** : Suppression du port 80 (HTTP) proposé par défaut. Ouverture du port 25565 en protocole TCP, qui est le port par défaut utilisé par les clients Minecraft pour joindre un serveur. Ce choix est indiqué dans la documentation de l’image Docker, qui précise que ce port doit être exposé pour le fonctionnement du serveur.
><img width="814" height="546" alt="image" src="https://github.com/user-attachments/assets/f6372b35-b7ad-43ff-9366-0f5759e68abc" />

### 3. Paramètres Avancés & Variables d'Environnement (Advanced)
C'est ici que l'on injecte la configuration propre au jeu. Le conteneur va lire ces clés/valeurs au démarrage pour paramétrer le serveur automatiquement :

Détails des variables:
1. EULA | `TRUE` |, Accepte les termes de la licence Minecraft.
2. VERSION | `26.1.2` | Bloque la version spécifique du jeu.
3. HARDCORE | `true` | Active le mode mort définitive.
4. DIFFICULTY | `hard` | Difficulté maximale.
5. ONLINE_MODE | `FALSE` | Permet la connexion des comptes "cracks".
6. TYPE | `FABRIC` | Installe le chargeur de mods Fabric.
7. MODRINTH_PROJECTS | `fabric-api,sharedhealth` | Télécharge et installe automatiquement l'API et le mod de vie partagée.

><img width="838" height="680" alt="image" src="https://github.com/user-attachments/assets/76b05eed-c1d9-43db-aba1-8f0c0c6904be" />

Une fois la validation Azure terminée, le déploiement prend moins de trois minutes. L'adresse IP publique est alors générée et s'affiche sur la page de vue d'ensemble. Il suffit ensuite de l'utiliser pour se connecter au serveur directement depuis le jeu. Comme l'illustre la capture d'écran, l'exécution de la commande netstat -n -p tcp | findstr 25565 confirme qu'une connexion active a bien été établie avec le serveur.

><img width="1802" height="740" alt="image" src="https://github.com/user-attachments/assets/ac40a395-1f8d-4202-b889-994d0bb4b41f" />

Le Portail Azure permet de suivre en quasi-temps réel la consommation de la ressource (CPU et Mémoire) directement depuis l'onglet de supervision du conteneur.

><img width="1591" height="412" alt="image" src="https://github.com/user-attachments/assets/ed6ff78e-fd84-43d0-8f5a-e4399f3e9b2c" />

**Constat après session :**
Après analyse des graphiques durant une session de jeu active, il s'avère que le dimensionnement initial (**4 vCPUs / 8 Go de RAM**) était légèrement surdimensionné par rapport aux besoins réels de l'application pour notre nombre de joueurs.

**Plan d'action pour les prochaines sessions :**
* **Ajustement des ressources** : Réduire la configuration à **2 vCPUs** et **4 ou 6 Go de RAM** lors des prochaines soirées pour vérifier si les performances restent fluides (sans rollbacks).
* **Impact budgétaire** : Diviser par deux les ressources allouées permet de diviser le coût horaire par deux, poussant la démarche d'optimisation au maximum.

##  Fin de session : Gestion des coûts et Nettoyage

Une fois ma session terminée, j'ai le choix entre deux options directement depuis l'interface pour couper les coûts :

1. **Pour une simple pause** : Je clique sur le bouton **Stop** en haut de la page du conteneur. Cela libère les processeurs et la RAM pour stopper la facturation du calcul (Compute), tout en gardant le serveur prêt à redémarrer pour la prochaine fois.
2. **Pour une fin définitive** : Je supprime directement le groupe de ressources contenant l'instance. Cela détruit tout d'un coup et arrête définitivement tous les coûts associés au projet.

><img width="1885" height="819" alt="image" src="https://github.com/user-attachments/assets/ae01c8ce-5db4-4bd5-87ad-f20508859a11" />

##  Étape 2 : Automatisation via Azure CLI

Après avoir configurer le serveur une première fois depuis le portail qui permet de voir tout les parametres proposés, je sais maintenant de quoi j'ai besoin pour mon serveur. Il est alors temps d'automatiser ces étapes pour déployer le serveur en quelques commandes pour rendre cela beaucoup plus rapide pour les prochaines fois.

### 1. Création du Groupe de Ressources
Avant de lancer le conteneur, je crée le groupe de ressources qui va isoler mon projet de la même manière que sur le portail :
```bash
`az group create --name rg1 --location westeurope`
```
### 2. Déploiement de l'instance de conteneur (ACI)
Pour condenser tous les clics du portail (définition des 2 vCPUs, des 4 Go de RAM, ouverture du port 25565 et injection des configurations de jeu), j'exécute la commande brute suivante.

```bash
az container create \
  --resource-group rg1 \
  --name serveurdejeu-cli \
  --image itzg/minecraft-server \
  --cpu 2 \
  --memory 4 \
  --os-type Linux \
  --ip-address Public \
  --ports 25565 \
  --protocol TCP \
  --environment-variables \
    EULA=TRUE \
    VERSION=26.1.2 \
    HARDCORE=true \
    DIFFICULTY=hard \
    ONLINE_MODE=FALSE \
    TYPE=FABRIC \
    MODRINTH_PROJECTS=fabric-api,sharedhealth`
```
### 3. Récupération de l'IP publique du serveur
Une fois le déploiement lancé, je peux récupérer l'adresse IP publique du serveur en ligne de commande pour la partager aux joueurs, sans avoir besoin d'ouvrir l'interface d'Azure :

><img width="305" height="136" alt="image" src="https://github.com/user-attachments/assets/73766938-7678-41be-993c-37aa0b5a9305" />

##  Fin de session : Gestion des coûts et Nettoyage (CLI)

Pour faire une simple pause : J'arrête l'instance pour libérer les ressources de calcul et stopper la facturation du Compute :
`az container stop --resource-group rg1 --name serveurdejeu-cli`

Pour une fin définitive : Je supprime le groupe de ressources. L'argument --yes supprime la confirmation manuelle et --no-wait me rend la main immédiatement dans le terminal pendant qu'Azure nettoie tout en arrière-plan :

><img width="735" height="58" alt="image" src="https://github.com/user-attachments/assets/0c765fbb-09f0-49ef-bd89-3f0ecca8a124" />


