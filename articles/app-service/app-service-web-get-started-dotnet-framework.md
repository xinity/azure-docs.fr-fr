---
title: Créer une application web C# ASP.NET Framework - Azure App Service | Microsoft Docs
description: Découvrez comment exécuter des applications web dans Azure App Service en déployant l’application web C# ASP.NET par défaut.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 10/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 258e547c58016cb449c74b058d02f2a2e4d7d683
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792787"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Créer une application web ASP.NET Framework dans Azure

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.

Ce guide de démarrage rapide montre comment déployer votre première application web ASP.NET sur Azure App Service. Une fois que vous avez terminé, vous disposez d’un plan App Service. Vous disposez également d’une application App Service et d’une application web déployée.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, installez <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> avec la charge de travail **Développement web et ASP.NET**.

Si vous avez déjà installé Visual Studio 2019 :

- Installez les dernières mises à jour dans Visual Studio en sélectionnant **Aide** > **Rechercher les mises à jour**.
- Ajoutez la charge de travail en sélectionnant **Outils** > **Obtenir des outils et des fonctionnalités**.

## Créer une application web ASP.NET <a name="create-and-publish-the-web-app"></a>

Créez une application web ASP.NET en effectuant les étapes suivantes :

1. Ouvrez Visual Studio, puis sélectionnez **Créer un projet**.

2. Dans **Créer un projet**, recherchez et choisissez **Application web ASP.NET (.NET Framework)** , puis sélectionnez **Suivant**.

3. Dans **Configurer votre nouveau projet**, nommez l’application _myFirstAzureWebApp_, puis sélectionnez **Créer**.

   ![Configurer votre projet d’application web](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Vous pouvez déployer n’importe quel type d’application web ASP.NET dans Azure. Pour ce guide de démarrage rapide, choisissez le modèle **MVC**.

5. Vérifiez que l’option d’authentification a la valeur **Aucune authentification**. Sélectionnez **Create** (Créer).

   ![Créer une application web ASP.NET](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Démarrer sans débogage** pour exécuter l’application web localement.

   ![Exécuter l’application localement](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Publier votre application web <a name="launch-the-publish-wizard"></a>

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **myFirstAzureWebApp**, puis sélectionnez **Publier**.

1. Choisissez **App Service**, puis remplacez **Créer un profil** par **Publier**.

   ![Publier à partir de la page de présentation du projet](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. Dans **Créer un App Service**, vos options varient si vous êtes déjà connecté à Azure et si vous avez un compte Visual Studio lié à un compte Azure. Sélectionnez **Ajouter un compte** ou **Connexion** pour vous connecter à votre abonnement Azure. Si vous êtes déjà connecté, sélectionnez le compte souhaité.

   > [!NOTE]
   > Si vous êtes déjà connecté, ne sélectionnez pas encore **Créer**.
   >
   >

   ![Connexion à Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Pour **Groupe de ressources**, sélectionnez **Nouveau**.

1. Dans **Nouveau nom du groupe de ressources**, entrez *myResourceGroup*, puis sélectionnez **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Pour **Plan d’hébergement**, sélectionnez **Nouveau**.

1. Dans la boîte de dialogue **Configurer le plan d’hébergement**, entrez les valeurs du tableau suivant, puis sélectionnez **OK**.

   | Paramètre | Valeur suggérée | Description |
   |-|-|-|
   |Plan App Service| myAppServicePlan | Nom du plan App Service. |
   | Location | Europe Ouest | Centre de données dans lequel l’application web est hébergée. |
   | Size | Gratuit | Le [niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) détermine les fonctionnalités d’hébergement. |

   ![Créer un plan App Service](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Dans **Nom**, entrez un nom d’application unique qui inclut uniquement les caractères valides `a-z`, `A-Z`, `0-9` et `-`. Vous pouvez accepter le nom unique généré automatiquement. L’URL de l’application web est `http://<app_name>.azurewebsites.net`, où `<app_name>` est le nom de votre application.

2. Sélectionnez **Créer** pour commencer à créer les ressources Azure.

   ![Configurer le nom de l’application](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Une fois que l’Assistant a terminé, il publie l’application web ASP.NET dans Azure, puis lance l’application dans le navigateur par défaut.

![Application web ASP.NET publiée dans Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Le nom d’application spécifié dans la page **Créer un App Service** est utilisé en tant que préfixe d’URL au format `http://<app_name>.azurewebsites.net`.

**Félicitations !** Votre application web ASP.NET s’exécute en temps réel dans Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Mise à jour de l’application et redéploiement

1. Dans l’**Explorateur de solutions**, sous votre projet, ouvrez **Vues** > **Accueil** > **Index.cshtml**.

1. Recherchez la balise HTML `<div class="jumbotron">` vers le début, puis remplacez la totalité de l’élément par le code suivant :

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Pour effectuer un redéploiement dans Azure, cliquez avec le bouton droit sur le projet **myFirstAzureWebApp** dans **l’Explorateur de solutions**, puis sélectionnez **Publier**. Sélectionnez ensuite **Publier**.

Une fois la publication terminée, Visual Studio lance un navigateur en accédant à l’URL de l’application web.

![Application web ASP.NET mise à jour dans Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Gérer l’application Azure

1. Accédez au <a href="https://portal.azure.com" target="_blank">Portail Azure</a> pour gérer l’application web.

2. Dans le menu de gauche, sélectionnez **App Services**, puis sélectionnez le nom de votre application Azure.

   ![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez effectuer des tâches de gestion de base, par exemple parcourir, arrêter, démarrer, redémarrer et supprimer.

   ![Vue d’ensemble d’App Service dans le portail Azure](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Le menu de gauche fournit différentes pages vous permettant de configurer votre application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [ASP.NET avec SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
