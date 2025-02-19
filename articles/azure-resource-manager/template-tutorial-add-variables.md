---
title: Tutoriel - Ajouter une variable au modèle Azure Resource Manager
description: Ajoutez des variables à votre modèle Azure Resource Manager pour simplifier la syntaxe.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fb577c0c0f00da3735da98fc2e94f3610c9bd80d
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001467"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>Didacticiel : Ajouter des variables à votre modèle Resource Manager

Dans ce tutoriel, vous apprenez à ajouter une variable à votre modèle. Les variables simplifient vos modèles en vous permettant d’écrire une expression une fois et de la réutiliser dans tout le modèle. Ce tutoriel dure environ **7 minutes**.

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les fonctions](template-tutorial-add-functions.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Outils Resource Manager et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez les [outils de modèle](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examiner votre modèle

À la fin du tutoriel précédent, votre modèle présentait le code JSON suivant :

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

Le paramètre pour le nom de compte de stockage est difficile à utiliser, car vous devez fournir un nom unique. Si vous avez suivi les tutoriels précédents de cette série, vous êtes probablement fatigué de chercher un nom unique. Pour résoudre ce problème, ajoutez une variable qui élabore un nom unique pour le compte de stockage.

## <a name="use-variable"></a>Utiliser une variable

L’exemple suivant met en évidence les modifications à apporter pour ajouter une variable à votre modèle qui crée un nom de compte de stockage unique. Copiez l’intégralité du fichier et remplacez votre modèle par son contenu.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

Notez qu’elle comprend une variable nommée **uniqueStorageName**. Cette variable utilise quatre fonctions pour composer une valeur de chaîne.

Vous êtes déjà familiarisé avec la fonction [parameters](resource-group-template-functions-deployment.md#parameters), nous ne l’examinerons donc pas.

Vous êtes également familiarisé avec la fonction [resourceGroup](resource-group-template-functions-resource.md#resourcegroup). Dans ce cas, vous obtenez la propriété **id** au lieu de la propriété **location**, comme illustré dans le tutoriel précédent. La propriété **id** retourne l’identificateur complet du groupe de ressources, notamment l’ID d’abonnement et le nom du groupe de ressources.

La fonction [uniqueString](resource-group-template-functions-string.md#uniquestring) crée une valeur de hachage de 13 caractères. La valeur retournée est déterminée par les paramètres que vous transmettez. Pour ce tutoriel, vous utilisez l’ID de groupe de ressources en tant qu’entrée pour la valeur de hachage. Cela signifie que vous pouvez déployer ce modèle dans différents groupes de ressources et obtenir une valeur de chaîne unique différente. Par contre, vous recevez la même valeur si vous déployez sur le même groupe de ressources.

La fonction [concat](resource-group-template-functions-string.md#concat) prend des valeurs et les combine ensemble. Pour cette variable, elle prend la chaîne à partir du paramètre et la chaîne à partir de la fonction uniqueString, puis les combine en une seule chaîne.

Le paramètre **storagePrefix** vous permet de transmettre un préfixe qui vous aide à identifier les comptes de stockage. Vous pouvez créer votre propre convention de nommage ; dans un longue liste de ressources, celle-ci facilite l’identification des comptes de stockage après le déploiement.

Enfin, remarquez que le nom de stockage est maintenant défini sur la variable, et non sur un paramètre.

## <a name="deploy-the-template"></a>Déployer le modèle

Déployons le modèle. Le déploiement de ce modèle est plus facile que les modèles précédents, car vous fournissez uniquement le préfixe pour le nom de stockage.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](template-tutorial-create-first-template.md#create-resource-group). L’exemple suppose que vous avez défini la variable **templateFile** sur le chemin du fichier de modèle, comme indiqué dans le [premier tutoriel](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Vous voyez qu’une ressource de compte de stockage a été déployée. Le nom du compte de stockage est composé de **store** et d’une chaîne de caractères aléatoires.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une variable qui crée un nom unique pour un compte de stockage. Dans le tutoriel suivant, vous allez retourner une valeur à partir du compte de stockage déployé.

> [!div class="nextstepaction"]
> [Ajouter des sorties](template-tutorial-add-outputs.md)
