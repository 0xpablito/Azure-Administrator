# Lab 01 : Manage Microsoft Entra ID Identities

## 📝 Description du Lab
Ce lab se concentre sur la gestion des identités dans **Microsoft Entra ID**. L'objectif est de sécuriser l'accès aux ressources en gérant les utilisateurs, les groupes et les licences.

## 🚀 Objectifs d'apprentissage
* Créer et configurer des utilisateurs Microsoft Entra ID.
* Créer des groupes et gérer l'appartenance des membres.

## 🛠️ Architecture du Lab
Le scénario simule la mise en place de l'organisation pour la société "Contoso".
- **Utilisateurs créés :** `Utilisateur1`, `Pablo Fernandez`, etc.
- **Groupes :** `Administrateurs IT` (Assigné).

##  Étapes réalisées

### Tâche 1 : Création des utilisateurs
1. Connexion au portail Azure.
2. Navigation vers **Microsoft Entra ID**.
3. Création de deux utilisateurs avec des profils spécifiques (interne et externe).

<table>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/4204b30d-87bc-4c9a-a278-be95cc6f4360" alt="Création utilisateur interne" width="100%"/>
      <br><em>Utilisateur Interne</em>
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/1854431b-cfa7-4de6-8a4b-8bbb29cd8282" alt="Création utilisateur externe" width="100%"/>
      <br><em>Utilisateur Externe (Invité)</em>
    </td>
  </tr>
</table>



### Tâche 2 : Création de groupe
Créer des groupes permet de mieux gérer les accès et simplifie l’administration.

> **Note sur les types d'adhésion :**
> * **Groupes Statiques (Assigned) :** Le mode par défaut (licence gratuite). L'administrateur doit ajouter ou retirer manuellement chaque membre.
> * **Groupes Dynamiques (Dynamic User/Device) :** Nécessite une licence **Entra ID P1 ou P2**. Les membres sont ajoutés automatiquement selon des règles (ex: département, ville, etc.).

**Actions réalisées :**
Création d'un groupe de sécurité avec l'option **"Assigned"** (statique), définition du propriétaire et ajout des membres.

<img width="1271" height="875" alt="image" src="https://github.com/user-attachments/assets/2a8bb3e1-0c45-4f30-ad4c-8572dac505a2" />





**Vérification des utilisateurs et du groupe :**
J'ai pu constater l'ajout de mes comptes utilisateurs lors de l'ajout dans le groupe. Je vérifie maintenant la présence du groupe, de ses membres et de son propriétaire en me rendant sur la page du groupe créé (**Administrateurs IT**).

<img width="1697" height="745" alt="image" src="https://github.com/user-attachments/assets/53d68e29-764f-4cf7-8fae-9b4b9325230f" />
