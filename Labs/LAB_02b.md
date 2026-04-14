# Lab 02b : Gérer la gouvernance depuis Azure Policy

## 📝 Description du Lab
Ce lab se concentre sur la mise en place de la gouvernance dans Microsoft Entra ID et dans l’environnement Azure. L’objectif est d’améliorer la gestion des ressources en appliquant des règles de conformité, en structurant l’organisation et en assurant un meilleur suivi des ressources via les tags et les politiques.

## 🚀 Objectifs d'apprentissage
* Mettre en place une gouvernance des ressources Azure.
* Utiliser les tags pour améliorer le suivi et le reporting.
* Appliquer des policies pour imposer des règles sur les ressources.
* Sécuriser les ressources à l’aide de verrous.


##  Étapes réalisées

### Tâche 1 : Appliquer un tags depuis Azure
**Objectif :** Ajouter des métadonnées aux ressources afin de faciliter leur gestion, leur suivi et leur organisation.

1. Navigation vers **Ressource Groups**, puis création d’un groupe de ressources nommé rg1.
2. Ajout d’un tag avec les valeurs suivantes :
* Name : Cost Center
* Value : 000
><img width="541" height="868" alt="image" src="https://github.com/user-attachments/assets/ec893284-7b10-4d25-a028-2ea88ffe4518" />





### Tâche 2 : Forcer l’utilisation des tags avec une Policy
**Objectif :** Garantir que toutes les ressources respectent des règles de gouvernance en imposant des tags obligatoires.

1. Accès au service Policy dans le portail Azure
2. Dans Authoring > Definitions, sélection de la policy ``Require a tag and its value on resources``.
3. Définition du scope sur l’abonnement et le groupe de ressources rg1
4. Configuration des paramètres de base :
* Nom : Exiger le tag Cost Center et sa valeur sur les ressources
* Description : Exiger le tag Cost Center et sa valeur sur toutes les ressources du groupe de ressources
* Policy enforcement : Enabled
Configuration des paramètres :
* Tag Name : Cost Center
* Tag Value : 000
5. Validation avec Review + Create puis création de la policy
><img width="855" height="784" alt="image" src="https://github.com/user-attachments/assets/041f4d25-e3ba-42dc-bb91-a42460d4a130" />
