---
title: Résoudre les problèmes liés aux déploiements Resource Manager | Microsoft Docs
description: Découvrez comment superviser et dépanner les déploiements de modèle Azure Resource Manager. Affiche les journaux d’activité et l’historique des déploiements.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4ad32ed83d731a26b6bb72fca230d00d5465c45a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390213"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager

Découvrez comment corriger les erreurs de déploiement des modèles Resource Manager. Au cours de ce tutoriel, vous allez configurer deux erreurs dans un modèle, puis apprendre à utiliser les journaux d’activité et l’historique de déploiement pour résoudre les problèmes.

Il existe deux types d’erreurs liées au déploiement d’un modèle :

- Les **erreurs de validation** sont liées à des scénarios pouvant être identifiés avant le déploiement. Elles incluent notamment les erreurs de syntaxe dans votre modèle ou le déploiement de ressources entraînant un dépassement des quotas d’abonnement. 
- Les **erreurs de déploiement** sont liées aux événements se produisant lors du déploiement. Elles incluent la tentative d’accès à une ressource qui est déployée en parallèle.

Les deux types d’erreurs retournent un code d’erreur qui vous permet de résoudre les problèmes liés au déploiement. Les deux types d’erreurs apparaissent dans le journal d’activité. Toutefois, les erreurs de validation n’apparaissent pas dans l’historique de votre déploiement, car le déploiement n’a jamais démarré.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un modèle problématique
> * Résoudre les erreurs de validation
> * Résoudre les erreurs de déploiement
> * Supprimer des ressources

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- [Visual Studio Code](https://code.visualstudio.com/) avec l’[extension Outils Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="create-a-problematic-template"></a>Créer un modèle problématique

Ouvrez un modèle nommé [Créer un compte de stockage standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/) depuis les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/), puis configurez deux problèmes.

1. À partir de Visual Studio Code, sélectionnez **Fichier**>**Ouvrir un fichier**.
2. Collez l’URL suivante dans **Nom de fichier** :

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Sélectionnez **Ouvrir** pour ouvrir le fichier.
4. Remplacez la ligne **apiVersion** par la ligne suivante :

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** est un nom d’élément non valide. Il s’agit d’une erreur de validation.
    - La version de l’API doit être « 2018-07-01 ».  Il s’agit d’une erreur de déploiement.

5. Sélectionnez **Fichier**>**Enregistrer sous** pour enregistrer le fichier sous le nom **azuredeploy.json** sur votre ordinateur local.

## <a name="troubleshoot-the-validation-error"></a>Résoudre l’erreur de validation

Reportez-vous à la section [Déployer le modèle](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) pour déployer le modèle.

Vous devez obtenir une erreur similaire à la suivante depuis l’interpréteur de commandes :

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Le message d’erreur indique que le problème est lié à **apiVersion1**.

Utilisez Visual Studio Code pour remédier au problème en remplaçant **apiVersion1** par **apiVersion**, puis enregistrez le modèle.

## <a name="troubleshoot-the-deployment-error"></a>Résoudre l’erreur de déploiement

Reportez-vous à la section [Déployer le modèle](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) pour déployer le modèle.

Vous devez obtenir une erreur similaire à la suivante depuis l’interpréteur de commandes :

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Vous trouverez l’erreur de déploiement à partir du portail Azure à l’aide de la procédure suivante :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le groupe de ressources en sélectionnant **Groupes de ressources**, puis le nom du groupe de ressources. Vous devez voir **1 Échec** sous **Déploiement**.

    ![Tutoriel sur la résolution des problèmes liés à Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Sélectionnez **Détails de l’erreur**.

    ![Tutoriel sur la résolution des problèmes liés à Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Le message d’erreur est identique à celui mentionné précédemment :

    ![Tutoriel sur la résolution des problèmes liés à Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Vous pouvez également rechercher l’erreur dans les journaux d’activité :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Surveiller** > **Journal d’activité**.
3. Utilisez les filtres pour rechercher le journal.

    ![Tutoriel sur la résolution des problèmes liés à Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Utilisez Visual Studio Code pour corriger le problème, puis redéployez le modèle.

Pour obtenir la liste des erreurs courantes, consultez [Résoudre les erreurs courantes de déploiement Azure avec Azure Resource Manager](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.  Vous devriez voir six ressources au total dans le groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à résoudre les erreurs de déploiement des modèles Resource Manager.  Pour plus d’informations, consultez [Résoudre les erreurs courantes de déploiement Azure avec Azure Resource Manager](./resource-manager-common-deployment-errors.md).
