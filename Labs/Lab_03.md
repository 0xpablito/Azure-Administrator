# Lab 02b : Gérer les ressources Azure via les templates ARM et Bicep
## 📝 Description du Lab
Ce lab se concentre sur l'automatisation des déploiements de ressources. L'objectif est d'utiliser des templates **JSON (ARM)** et **Bicep** pour déployer des ressources de manière rapide et sans erreur humaine.

## 🚀 Objectifs d'apprentissage
* Créer et exporter un template Azure Resource Manager (ARM).
* Modifier et redéployer un template pour créer de nouvelles ressources.
* Déployer des templates via **Azure PowerShell** et **Azure CLI**.
* Utiliser **Azure Bicep** pour simplifier la déclaration d'infrastructure.


##  Étapes réalisées

### Tâche 1 : Créer et exporter un template ARM
**Objectif :** Créer une ressource manuellement pour ensuite récupérer son template JSON.

1. Créer un disque managé dans Azure avec les paramètres suivants :
><img width="625" height="858" alt="image" src="https://github.com/user-attachments/assets/a91b7ef5-873d-4b50-80e6-f1d4b5be5958" />

2. Une fois la ressource créée, accéder à la section **Automation** du disque managé, puis sélectionner **Export template**.
3. Télécharger le fichier ``Template`` ainsi que le fichier ``Parameters``.
><img width="1568" height="548" alt="image" src="https://github.com/user-attachments/assets/a4e3fb9a-e64d-4e3e-b5ae-cb68025c3615" />
> Note : Le fichier Template définit la structure et les ressources disponibles, tandis que le fichier Parameters contient les valeurs spécifiques choisies pour le déploiement.

### Tâche 2 : Modifier un template Azure Resource Manager et le redéployer
**Objectif :** Utiliser le template téléchargé lors de l'étape précédente pour le modifier et le redéployer afin de créer une nouvelle ressource.

1. Rechercher **Deploy a custom template** et choisir **Build your own template in the editor**.
2. Importer le fichier `template.json`, puis renommer `disk_Disque1_name` en `disk_name` aux deux occurrences. Puis remplacer `Disque1` en `Disque2` et sauvegarder les changements.
3. Sélectionner `Edit parameters`, importer le fichier `parameters.json`renommer `disks_Disque1_name` en `disk_name` et sauvegarder les changements.
><img width="1764" height="524" alt="image" src="https://github.com/user-attachments/assets/979b4125-22b2-4f49-bf5a-14114c287b75" />
4. Configurer les derniers paramètres (Abonnement, Groupe de ressources, Région) et lancer le déploiement de la ressource avec les templates modifiés.
><img width="727" height="869" alt="image" src="https://github.com/user-attachments/assets/8e394fb1-ebe3-4dfc-92be-bda9463a6986" />

### Tâche 3 : Configurer Cloud Shell et déployer un template avec Powershell
**Objectif :** Utiliser l'interface en ligne de commande pour automatiser le déploiement sans passer par l'interface graphique du portail.

1. Ouvrir le Cloud Shell et choisir Powershell
2. Depuis `Manage files` choisir `Upload` et importer les .json template et parameters
><img width="1820" height="292" alt="image" src="https://github.com/user-attachments/assets/26983996-f61b-43eb-bcdd-14b9cf6743c9" />

3. À l'aide de l'éditeur, modifier un paramètre du template (par exemple, changer la valeur du nom du disque par `Disque3`), puis lancer le déploiement avec la commande suivante :
``New-AzResourceGroupDeployment -ResourceGroupName rg1 -TemplateFile template.json -TemplateParameterFile parameters.json``
><img width="1772" height="864" alt="image" src="https://github.com/user-attachments/assets/4c120204-f123-45cb-8768-8d8eeea8e397" />

4. Vérification du déploiement du disque grâce à la commande `Get-AzDisk`
><img width="1822" height="748" alt="image" src="https://github.com/user-attachments/assets/2240e8fb-7041-47bc-8e9e-b6fbf6769c05" />

