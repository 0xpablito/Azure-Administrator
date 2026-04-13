# Lab 02 : Gérer les abonnements et le contrôle d’accès basé sur les rôles (RBAC)

## 📝 Description du Lab
Ce lab se concentre sur la gestion des identités dans **Microsoft Entra ID**. L'objectif est de sécuriser l'accès aux ressources en gérant les utilisateurs, les groupes et les licences.

## 🚀 Objectifs d'apprentissage
* Créer et configurer un groupe de management.
* Créer un role personnalisé RBAC
* Examiner et attribuer un rôle Azure built-in
* Surveiller les attributions de rôles via le journal d’activité.


##  Étapes réalisées

### Tâche 1 : Création d'un groupe de management
**Objectif :** Permet d'organiser plusieurs abonnements et d'appliquer des politiques de sécurité ou des contrôles d'accès de manière centralisée.

1. Navigation vers **Ressource Manager**.
3. Création d'un groupe de management.
<img width="1693" height="904" alt="image" src="https://github.com/user-attachments/assets/73034858-f48c-440b-9a68-fd5b1de5a93a" />




### Tâche 2 : Ajouter un rôle Aure "Built-in"
**Objectif :** Déléguer des tâches spécifiques à une équipe (ici le Helpdesk) sans leur donner les pleins pouvoirs (principe du moindre privilège).

1. Navigation vers le Contrôle d'accès (IAM) du groupe d'administration "management1", puis dans l'onglet Rôles.
2. Attribution du rôle "Virtual Machine Contributor" au groupe "Helpdesk".
<img width="1214" height="867" alt="image" src="https://github.com/user-attachments/assets/4bf2a0c8-27fc-4274-96e8-d14713e2f368" />

>**Note :** Ce rôle permet la gestion des VM sans donner accès au système d'exploitation, ni au réseau virtuel ou au stockage auxquels elles sont connectées.
>**Note (Best Practice) :** Privilégier l'attribution de rôles à des groupes plutôt qu'à des individus.


### Tâche 3 : Créer un rôle RBAC personnalisé
**Objectif :** Répondre à des besoins spécifiques lorsque les rôles intégrés sont soit trop permissifs, soit insuffisants pour des besoins précis.

1. Ajout d’un rôle personnalisé en reprenant la base d’un rôle existant.
2. Ajout d'une règle de restriction pour exclure la capacité d’enregistrer des Resource Providers Azure. Cette action est retirée via une règle NotAction, ce qui empêche le rôle d’activer ou de modifier les services disponibles dans l’abonnement.
<img width="609" height="865" alt="image" src="https://github.com/user-attachments/assets/9d3001bb-7d97-4ddb-adf7-22cdf1c7d6bf" />


>**Note :** Les rôles personnalisés permettent de définir précisément les autorisations en ajoutant ou en retirant des actions. Ils peuvent être créés à partir de zéro, d’un rôle intégré Azure, ou en modifiant un fichier JSON existant.

### Tâche 4 : Surveiller les attributions de rôles via le journal d'activité
**Objectif :** Assurer la traçabilité en identifiant qui a modifié des accès, quand et sur quelle ressource.

1. Accéder au groupe d'administration **management1**.
2. Sélectionner le menu **Journal d'activité** (Activity log) dans le panneau de gauche.
3. Filtrer les événements pour isoler les opérations liées aux attributions de rôles (ex: "Create role assignment").

<img width="1465" height="405" alt="image" src="https://github.com/user-attachments/assets/6c876160-cd8b-4da8-85e3-cb970b405382" />

## Conclusion et points clés

Ce lab a permis de mettre en pratique la gouvernance Azure à travers plusieurs concepts fondamentaux :

* **Organisation logique :** Les groupes d'administration servent à structurer les abonnements de manière hiérarchique pour une gestion centralisée.
* **Contrôle d'accès (RBAC) :** L'utilisation de rôles intégrés (*Built-in*) permet de déléguer rapidement des permissions standard.
* **Personnalisation avancée :** La création de rôles personnalisés via des définitions **JSON** (incluant `Actions` et `NotActions`) offre une précision totale pour respecter le principe du moindre privilège.
* **Audit et Traçabilité :** Le **Journal d'activité** est l'outil indispensable pour surveiller qui attribue quels rôles, garantissant ainsi un suivi complet.

##  Nettoyage des ressources 
**Objectif :** Libérer les ressources et éviter des coûts inutiles en supprimant les configurations de test.

* **Via PowerShell :** 
  ``Remove-AzManagementGroup -GroupName "management1"``
  <img width="601" height="52" alt="image" src="https://github.com/user-attachments/assets/ace9d803-9053-413f-bc75-b200357394a9" />

