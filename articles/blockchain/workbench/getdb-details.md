---
title: Obtenir des détails sur la base de données Azure Blockchain Workbench Preview
description: Découvrez comment obtenir des informations sur la base de données Azure Blockchain Workbench Preview et le serveur de base de données.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f4a4eaab9a03aeed27e29eb645b6e22a028b243b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845076"
---
# <a name="get-information-about-your-azure-blockchain-workbench-preview-database"></a>Obtenir des informations sur votre base de données Azure Blockchain Workbench Preview

Cet article explique comment obtenir des informations détaillées sur votre base de données Azure Blockchain Workbench Preview.

## <a name="overview"></a>Vue d'ensemble

Pour plus d’informations sur les applications, les flux de travail et l’exécution des contrats intelligent, utilisez les vues de base de données dans la base de données SQL Blockchain Workbench. Les développeurs peuvent utiliser ces informations avec certains outils comme Microsoft Excel, Power BI, Visual Studio et SQL Server Management Studio.

Avant de se connecter à la base de données, un développeur a besoin de plusieurs choses :

* Un accès client externe autorisé dans le pare-feu de base de données. Cet article sur la configuration d’un pare-feu de base de données explique comment autoriser l’accès.
* Le nom du serveur de base de données et le nom de la base de données.

## <a name="connect-to-the-blockchain-workbench-database"></a>Se connecter à la base de données Blockchain Workbench

Pour se connecter à la base de données :

1. Connectez-vous au portail Azure avec un compte détenant des autorisations de **propriétaire** pour les ressources Azure Blockchain Workbench.
2. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**.
3. Choisissez le nom du groupe de ressources pour votre déploiement Blockchain Workbench.
4. Sélectionnez **Type** pour trier la liste des ressources, puis votre **serveur SQL**. La liste triée dans la capture d’écran suivante montre les deux bases de données SQL : « master » et l’autre qui utilise « lhgn » en tant que **préfixe ressource**.

   ![Liste des ressources Blockchain Workbench triées](./media/getdb-details/sorted-workbench-resource-list.png)

5. Pour afficher des informations détaillées sur la base de données Blockchain Workbench, sélectionnez le lien pour la base de données avec le **préfixe ressource** que vous avez indiqué au moment du déploiement de Blockchain Workbench.

   ![Détails de la base de données](./media/getdb-details/workbench-db-details.png)

Le nom du serveur de base de données et le nom de la base de données vous permettent de vous connecter à la base de données Blockchain Workbench à l’aide de votre outil de développement ou de création de rapports.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vues de la base de données dans Azure Blockchain Workbench](database-views.md)