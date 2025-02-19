---
title: Tutoriel - Exporter le modèle Azure Resource Manager à partir du portail Azure
description: Découvrez comment utiliser un modèle exporté pour procéder au développement de votre modèle.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6e4f246cac0ecc1ab5942e522595f59c3625db8f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243210"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Didacticiel : Utiliser un modèle exporté depuis le portail Azure

Dans cette série de tutoriels, vous avez créé un modèle pour déployer un compte de stockage Azure. Dans les deux tutoriels suivants, vous allez ajouter un *plan App Service* et un *site web*. Au lieu de créer des modèles à partir de zéro, vous apprenez à exporter des modèles depuis le portail Azure, et à utiliser les exemples de modèles à partir des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/). Vous personnalisez ces modèles par rapport à votre utilisation. Ce tutoriel met l’accent sur l’exportation de modèles et sur la personnalisation du résultat pour votre modèle. Il faut compter environ **14 minutes** pour effectuer ce tutoriel.

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les sorties](template-tutorial-add-outputs.md), mais il n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Outils Resource Manager et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez les [outils de modèle](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examiner votre modèle

À la fin du tutoriel précédent, votre modèle présentait le code JSON suivant :

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Ce modèle fonctionne bien pour le déploiement des comptes de stockage, mais vous souhaitez peut-être y ajouter d’autres ressources. Vous pouvez exporter un modèle à partir d’une ressource existante pour obtenir rapidement le code JSON de cette ressource.

## <a name="create-app-service-plan"></a>Créer un plan App Service

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**.
1. Dans **Rechercher sur la Place de marché**, entrez **Plan App Service**, puis sélectionnez **Plan App Service**.  Ne sélectionnez pas **Plan App Service (classique)** .
1. Sélectionnez **Create** (Créer).
1. Entrez :

    - **Abonnement** : sélectionnez votre abonnement Azure.
    - **Groupe de ressources** : Sélectionnez **Créer**, puis précisez un nom. Indiquez un autre nom de groupe de ressources que celui que vous avez utilisé dans cette série de tutoriels.
    - **Nom** : entrez un nom pour le plan App Service.
    - **Système d’exploitation** : sélectionnez **Linux**.
    - **Région** : sélectionnez une zone géographique Azure. Par exemple, **USA Centre**.
    - **Niveau tarifaire** : afin de réduire les coûts, changez la référence (SKU) pour **De base B1** (sous Dev/Test).

    ![Modèle Resource Manager - Exporter le modèle du portail](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Sélectionnez **Examiner et créer**.
1. Sélectionnez **Create** (Créer). Quelques instants sont nécessaires pour créer la ressource.

## <a name="export-the-template"></a>Exporter le modèle

1. Sélectionnez **Accéder à la ressource**.

    ![Accéder à la ressource](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Sélectionnez **Exporter le modèle**.

    ![Modèle Resource Manager - Exporter le modèle](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   La fonction d’exportation de modèle prend l’état actuel d’une ressource et génère un modèle pour le déployer. L’exportation d’un modèle peut être un moyen pratique d’obtenir rapidement le code JSON dont vous avez besoin pour déployer une ressource.

1. Copiez la définition de **Microsoft.Web/serverfarms** et la définition de paramètre dans votre modèle.

    ![Modèle Resource Manager - Exporter le modèle - Modèle exporté](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> En règle générale, le modèle exporté est plus détaillé que ce que vous pourriez souhaiter lors de la création d’un modèle. Par exemple, l’objet SKU du modèle exporté possède cinq propriétés. Ce modèle fonctionne, mais vous pouvez simplement utiliser la propriété **name**. Vous pouvez commencer avec le modèle exporté, puis le modifier à votre convenance pour répondre à vos besoins.

## <a name="revise-the-existing-template"></a>Corriger le modèle existant

Le modèle exporté vous donne la majeure partie du code JSON dont vous avez besoin, mais vous devez le personnaliser pour votre modèle. Portez une attention particulière aux différences existant dans les paramètres et les variable,s entre votre modèle et le modèle exporté. Bien entendu, le processus d’exportation ignore les paramètres et les variables que vous avez déjà définis dans votre modèle.

L’exemple suivant met en évidence les ajouts à opérer dans votre modèle. Il contient le code exporté, ainsi que quelques modifications. Tout d’abord, il modifie le nom du paramètre pour qu’il corresponde à votre convention de nommage. Ensuite, il utilise votre paramètre location pour l’emplacement du plan App Service. Enfin, il supprime **name** à l’intérieur de l’objet **properties**, car cette valeur est redondante avec la propriété **name** au niveau de la ressource.

Copiez l’intégralité du fichier et remplacez votre modèle par son contenu.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-the-template"></a>Déployer le modèle

Utilisez Azure CLI ou Azure PowerShell pour déployer un modèle.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](template-tutorial-create-first-template.md#create-resource-group). L’exemple suppose que vous avez défini la variable **templateFile** sur le chemin du fichier de modèle, comme indiqué dans le [premier tutoriel](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Le groupe de ressources contient un compte de stockage et un plan App Service.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à exporter un modèle à partir du portail Azure, et à utiliser le modèle exporté pour le développement de votre modèle. Vous pouvez également utiliser les modèles de démarrage rapide Azure pour simplifier le développement de modèles.

> [!div class="nextstepaction"]
> [Utiliser les modèles de démarrage rapide Azure](template-tutorial-quickstart-template.md)
