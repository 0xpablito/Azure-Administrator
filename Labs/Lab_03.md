# Lab 03 : Gérer les ressources Azure via les templates ARM et Bicep
## 📝 Description du Lab
Ce lab explore l'automatisation des déploiements via l'**Infrastructure as Code (IaC)**. L'objectif est d'apprendre à utiliser des templates **JSON (ARM)** et **Bicep** pour déployer des ressources de manière standardisée, rapide et reproductible sur Azure.

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

### Tâche 4 : Déployer un template avec le CLI
**Objectif :** Utiliser Bash depuis Cloud Shell pour déployer un template.

1. Ouvrir Cloud Shell et sélectionner Bash.
2. Vérifier que les fichiers template.json et parameters.json sont disponibles à l’aide de la commande `ls`
><img width="1032" height="140" alt="image" src="https://github.com/user-attachments/assets/9ceeb9fe-644a-417f-b6b4-0b7b8c93150d" />

3. Modifier le nom dans l’éditeur en remplaçant le nom du disque par `Disque4` dans le fichier template et parameters, puis enregistrer avec Ctrl+S.
><img width="1691" height="335" alt="image" src="https://github.com/user-attachments/assets/3fdc92bf-7b80-4c2a-ae24-0b90c9f95333" />

4. Vérifier que le message de déploiement affiche "provisioningState": "Succeeded", puis confirmer le résultat avec la commande `az disk list --resource-group rg1 --output table`.
><img width="828" height="100" alt="image" src="https://github.com/user-attachments/assets/8317de11-6312-41a7-ad98-b00627098692" />

### Tâche 5 : Déployer une ressource avec Azure Bicep
**Objectif :** Utiliser un fichier Bicep pour déployer une ressource Azure

1. Télécharger le fichier Bicep mis à disposition sur le Lab dédié [azuredeploydisk.bicep](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/tree/master/Allfiles/Labs/03).
2. Importer le fichier Bicep dans le Cloud Shell Bash.
3. Modifier le fichier azuredeploydisk.bicep depuis l'éditeur Azure avec les paramètres suivants :
 * Changer le nom ligne 2 pour `Disque5`
 * Changer le taille du disque ligne 7 pour 64 Go
 * Changer le nom SKU ligne 26 pour `StandardSSD_LRS`.
4. Enregistrer les changements puis déployer le template avec la commande `az deployment group create --resource-group rg1 --template-file azuredeploydisk.bicep`.
><img width="1775" height="873" alt="image" src="https://github.com/user-attachments/assets/53a4b854-dfd2-4458-8012-d8c67097a668" />

5. Vérifier le déploiement du nouveau disque avec la commande `az disk list --resource-group rg1 --output table`
><img width="790" height="118" alt="image" src="https://github.com/user-attachments/assets/ed0623a7-cb34-41f2-b925-604bd884ce12" />

##  Conclusion et points clés

Ce lab a permis de maîtriser les fondamentaux de l'**Infrastructure as Code (IaC)** sur Azure :

* **Standardisation via ARM :** Les templates JSON permettent de déployer des ressources de manière identique, éliminant les erreurs humaines liées à la configuration manuelle.
* **Séparation des responsabilités :** L'utilisation de fichiers de paramètres (`parameters.json`) distincts du modèle (`template.json`) permet de réutiliser le même code pour différents environnements en changeant simplement les valeurs.
* **Flexibilité des outils :** Le déploiement peut être piloté par l'interface graphique, **PowerShell** ou **Azure CLI**, offrant une grande liberté d'automatisation.
* **Avantage de Bicep :** Ce langage offre une syntaxe beaucoup plus lisible et concise que le JSON, facilitant le processus de création et la maintenance de l'infrastructure.

## Nettoyage des ressources

**Objectif :** Libérer les ressources et éviter la facturation inutile après la fin des tests.

* **Via PowerShell :** `Remove-AzResourceGroup -Name "rg1"`
><img width="530" height="57" alt="image" src="https://github.com/user-attachments/assets/76bddc4f-9dd8-405e-a992-73897eb8ecf2" />
