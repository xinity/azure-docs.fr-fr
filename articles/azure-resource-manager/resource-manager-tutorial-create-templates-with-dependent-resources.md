---
title: Modèle Azure Resource Manager avec des ressources dépendantes
description: Découvrez comment créer votre premier modèle Azure Resource Manager avec plusieurs ressources, et comment le déployer dans le portail Azure
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0eb4b29ffb8ae0269dbc7efd7e9ef6b720188bce
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533507"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Didacticiel : Créer des modèles Azure Resource Manager avec des ressources dépendantes

Découvrez comment créer un modèle Azure Resource Manager pour déployer plusieurs ressources et configurer l’ordre de déploiement. Après avoir créé le modèle, vous déployez le modèle à l’aide du Cloud Shell dans le portail Azure.

Dans ce didacticiel, vous créez un compte de stockage, une machine virtuelle, un réseau virtuel et d’autres ressources dépendantes. Certaines ressources ne peuvent pas être déployées avant qu’une autre ressource n’existe. Par exemple, vous ne pouvez pas créer la machine virtuelle avant que son compte de stockage et son interface réseau n’existent. Vous définissez cette relation en rendant une seule ressource dépendante des autres ressources. Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle. Pour plus d’informations, consultez [Définir l’ordre de déploiement des ressources dans les modèles Azure Resource Manager](./resource-group-define-dependencies.md).

![schéma de l’ordre de déploiement des ressources dépendant du modèle Azure Resource Manager](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Ouvrir un modèle de démarrage rapide
> * Explorer le modèle
> * Déployer le modèle

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* [Visual Studio Code](https://code.visualstudio.com/) avec l’extension Outils Resource Manager.  Consultez [Installer l’extension ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Voici un exemple pour générer un mot de passe :

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault a été conçu pour protéger les clés et autres secrets de chiffrement. Pour plus d’informations, consultez [Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./resource-manager-tutorial-use-key-vault.md). Nous vous recommandons également de mettre à jour votre mot de passe tous les trois mois.

## <a name="open-a-quickstart-template"></a>Ouvrir un modèle de démarrage rapide

Modèles de démarrage rapide Azure est un référentiel pour les modèles Resource Manager. Au lieu de créer un modèle à partir de zéro, vous pouvez chercher un exemple de modèle et le personnaliser. Le modèle utilisé dans ce didacticiel se nomme [Déployer une machine virtuelle Windows simple](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer une copie du fichier sur votre ordinateur local avec le nom **azuredeploy.json**.

## <a name="explore-the-template"></a>Explorer le modèle

Lorsque vous explorez le modèle dans cette section, essayez de répondre aux questions suivantes :

* Combien de ressources Azure sont-elles définies dans ce modèle ?
* L’une des ressources est un compte de stockage Azure.  La définition est-elle semblable à celle utilisée dans le dernier didacticiel ?
* Trouvez-vous les références de modèle pour les ressources définies dans ce modèle ?
* Trouvez-vous les dépendances des ressources ?

1. À partir de Visual Studio Code, réduisez les éléments jusqu'à ce que vous voyiez uniquement les éléments de premier niveau et les éléments de second niveau à l’intérieur des **ressources** :

    ![Modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Il existe cinq ressources définies par le modèle :

   * `Microsoft.Storage/storageAccounts`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Consultez la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Il est préférable de comprendre quelques notions basiques du modèle avant de le personnaliser.

2. Développez la première ressource. Il s’agit d’un compte de stockage. Comparez la définition de ressource à la [référence de modèle](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Définition du compte de stockage des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Développez la deuxième ressource. Le type de ressource, est `Microsoft.Network/publicIPAddresses`. Comparez la définition de ressource à la [référence de modèle](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Définition de l’adresse IP publique des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Développez la quatrième ressource. Le type de ressource, est `Microsoft.Network/networkInterfaces` :  

    ![Élément dependson des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    L’élément dependsOn vous permet de définir une ressource comme une dépendance sur une ou plusieurs ressources. La ressource dépend de deux autres ressources :

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Développez la cinquième ressource. Cette ressource est une machine virtuelle. Elle dépend de deux autres ressources :

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Le diagramme suivant illustre les ressources et les informations de dépendance pour ce modèle :

![Diagramme de dépendance des modèles Azure Resource Manager Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

En spécifiant les dépendances, Resource Manager déploie efficacement la solution. Il déploie le compte de stockage, l’adresse IP publique et le réseau virtuel en parallèle car ils n’ont aucune dépendance. Après que l’adresse IP publique et le réseau virtuel sont déployés, l’interface réseau est créée. Lorsque toutes les autres ressources sont déployées, Resource Manager déploie la machine virtuelle.

## <a name="deploy-the-template"></a>Déployer le modèle

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il existe de nombreuses méthodes pour déployer des modèles.  Dans ce didacticiel, vous utilisez Cloud Shell dans le portail Azure.

1. Connectez-vous à [Cloud Shell](https://shell.azure.com). 
2. Sélectionnez **PowerShell** à partir de l’angle supérieur gauche de Cloud Shell, puis sélectionnez **Confirmer**.  Vous utiliserez PowerShell dans ce didacticiel.
3. Sélectionnez **Charger le fichier** à partir de Cloud Shell :

    ![Fichier de chargement du Cloud Shell du portail Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Sélectionnez le modèle que vous avez enregistré précédemment dans ce didacticiel. Le nom par défaut est **azuredeploy.json**.  Si vous avez un fichier portant le même nom de fichier, l’ancien fichier est remplacé sans notification.

    Vous pouvez éventuellement utiliser la commande **ls $HOME** et la commande **cat $HOME/azuredeploy.json** pour vérifier que les fichiers sont bien chargés. 

5. Dans Cloud Shell, exécutez les commandes PowerShell suivantes. Pour une sécurité optimale, utilisez un mot de passe généré pour le compte administrateur de la machine virtuelle. Consultez les [Conditions préalables](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    ```

8. Exécutez la commande PowerShell suivante pour lister les machines virtuelles nouvellement créées :

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Le nom de la machine virtuelle est codé en dur en tant que **SimpleWinVM** à l’intérieur du modèle.

9. Établir une connexion RDP vers la machine virtuelle pour vérifier la machine virtuelle a été créée avec succès.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez développé et déployé un modèle pour créer une machine virtuelle, un réseau virtuel et les ressources dépendantes. Pour savoir comment déployer des ressources Azure en fonction des conditions, consultez :

> [!div class="nextstepaction"]
> [Utiliser des conditions](./resource-manager-tutorial-use-conditions.md)
