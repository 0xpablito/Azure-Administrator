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

6. Test de la policy en créant un compte de stockage dans le groupe de ressources ``rg1``
><img width="869" height="860" alt="image" src="https://github.com/user-attachments/assets/1cdf75af-6b96-4118-a32a-6d4dd120f8e4" />

> Note : En cliquant sur "Policy Details", il est possible de consulter plus d’informations sur l’erreur ainsi que sur la policy qui bloque la création du compte de stockage.


### Tâche 3 : Forcer l’utilisation des tags avec une Policy
**Objectif :** Faire en sorte que les ressources enfants héritent automatiquement du tag Cost Center du groupe de ressources parent s'il est manquant, en utilisant une tâche de remédiation.

1. Accès à la section Assignments de Azure Policy et suppression de l'affectation précédente (Require a tag...) pour éviter les conflits.
2. Sélection de la policy built-in : Inherit a tag from the resource group if missing.
3. Configuration du Scope : Définition sur le groupe de ressources rg1.
4. Configuration des paramètres :
* Assignment name : Hériter du tag Cost Center et de sa valeur 000 du groupe de ressources.
* Tag Name : Cost Center.
5. Dans l'onglet Remediation, activation de l'option Create a remediation task, puis Review + Create.

><img width="789" height="862" alt="image" src="https://github.com/user-attachments/assets/bd296d93-4449-4ed3-aa1b-73281b3a7350" />

#### Test de la politique et vérification :

1. Ajout d'un nouveau compte de stockage dans le groupe ``rg1`` sans ajouter le tag.
><img width="1122" height="454" alt="image" src="https://github.com/user-attachments/assets/d2ffcdd0-45d6-4042-9fe2-91774d085e11" />
>Résultat : La validation passe avec succès car la politique ne bloque pas la création, elle la modifie. Le tag Cost Center avec la valeur 000 a été automatiquement appliqué à la ressource par Azure Policy.

### Tâche 4 : Sécuriser les ressources avec des Verrous (Locks)
**Objectif :** Empêcher la suppression ou la modification accidentelle de ressources critiques.

1. Ajout d'un verrou depuis l'onglet ``Lock`` du groupe de ressources ``rg1`` en mode ``Delete``
2. Test du verrou en tentant de supprimer le groupe de ressources
><img width="1330" height="255" alt="image" src="https://github.com/user-attachments/assets/9daf0977-34ba-4877-b4e3-d3dda2e31f27" />
>Résultat : Lors de la suppression du groupe de ressources une erreur s'affiche redirigant vers le Lock appliqué.

>Note : Deux types de verrous sont proposés : "Read-only", empêchant la modification, et "Delete", empêchant la suppression tout en laissant la modification possible.

##  Nettoyage des ressources 
**Objectif :** Libérer les ressources et éviter des coûts inutiles en supprimant les configurations de test.
1. **Suppression du verrou** : Suppression manuelle du verrou `rg-lock` dans la section Locks pour débloquer la suppression du groupe.
2. **Suppression du groupe de ressources** : 
   * **Via PowerShell :** ``Remove-AzResourceGroup -Name rg1``
><img width="411" height="29" alt="image" src="https://github.com/user-attachments/assets/f0df9e01-1861-43b7-93c0-31f39c01b259" />

