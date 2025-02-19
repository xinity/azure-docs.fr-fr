---
title: 'Didacticiel : se connecter à Azure Analysis Services avec Power BI Desktop | Microsoft Docs'
author: minewiskan
description: Découvrez comment vous connecter à Azure Analysis Services avec Power BI Desktop.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: f2123b6d8b11c89735e65cb0f48d93841f49328d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294488"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Tutoriel : Se connecter avec Power BI Desktop

Dans ce didacticiel, vous utilisez Power BI Desktop pour vous connecter à l’exemple de base de données de modèle adventureworks sur votre serveur. Les tâches que vous effectuez simulent une connexion utilisateur classique au modèle et la création d’un rapport à partir des données du modèle.

> [!div class="checklist"]
> * Obtenir le nom de votre serveur à partir du portail
> * Se connecter à l’aide de Power BI Desktop
> * Créer un rapport de base

## <a name="prerequisites"></a>Prérequis

- [Ajoutez l’exemple de base de données de modèle adventureworks](../analysis-services-create-sample-model.md) à votre serveur.
- Disposez d’autorisations [*lecture*](../analysis-services-server-admins.md) pour l’exemple de base de données de modèle adventureworks.
- [Installez la dernière version de Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Dans ce tutoriel, vous vous connectez au portail pour obtenir le nom du serveur uniquement. En règle générale, les utilisateurs peuvent obtenir le nom du serveur auprès de l’administrateur du serveur.

Connectez-vous au [portail](https://portal.azure.com/).

## <a name="get-server-name"></a>Obtenir le nom du serveur
Pour pouvoir vous connecter à votre serveur à partir de Power BI Desktop, vous avez besoin du nom du serveur. Vous pouvez obtenir le nom du serveur à partir du portail.

Dans **Portail Azure** > Serveur > **Présentation** > **Nom du serveur**, copiez le nom du serveur.
   
   ![Obtenir le nom du serveur dans Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Se connecter à Power BI Desktop

1. Dans Power BI Desktop, cliquez sur **Obtenir les données** > **Azure** > **Base de données Azure Analysis Services**.

   ![Se connecter pour obtenir des données](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. Dans **Serveur**, collez le nom du serveur puis, dans **Base de données**, entrez **adventureworks** et cliquez sur **OK**.

   ![Spécifier le nom du serveur et la base de données de modèle](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. À l’invite, entrez vos informations d’identification. Le compte que vous entrez doit disposer au minimum d’autorisations de lecture pour la base de données de modèle adventureworks.

    Le modèle adventureworks s’ouvre dans Power BI Desktop avec un rapport vierge dans l’affichage Rapport. La liste **Champs** affiche tous les objets du modèle qui ne sont pas cachés. L’état de la connexion se trouve dans le coin inférieur droit.

4. Dans **VISUALISATIONS**, sélectionnez **Graphique à barres groupées**, cliquez sur **Format** (icône en forme de rouleau de peinture), puis activez **Étiquettes de données**. 

   ![Visualisations](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. Dans la table **FIELDS** > **Internet Sales**, sélectionnez les mesures **Internet Sales Total** et **Margin**. Dans la table **Catégorie de produit**, sélectionnez **Nom de catégorie de produit**.

   ![Terminer le rapport](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Prenez quelques minutes pour étudier l’exemple de modèle adventureworks en créant différentes visualisations et en découpant des données et métriques. Lorsque vous êtes satisfait de votre rapport, veillez à enregistrer.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’en avez plus besoin, n’enregistrez pas votre rapport ou supprimez le fichier si vous n’avez enregistré.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à utiliser Power BI Desktop pour vous connecter à un modèle de données sur un serveur et à créer un rapport de base. Si vous n’êtes pas familiarisé avec la création d’un modèle de données, consultez le [didacticiel de modélisation de données tabulaires Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) disponible dans la documentation relative à SQL Server Analysis Services.