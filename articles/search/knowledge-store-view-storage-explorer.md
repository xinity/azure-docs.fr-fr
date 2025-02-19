---
title: Voir une base de connaissances avec l’Explorateur Stockage
titleSuffix: Azure Cognitive Search
description: Visualisez et analysez une base de connaissances Recherche cognitive Azure avec l’Explorateur Stockage du portail Azure.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: cfa85e61059e27cd39a9701a835a725e16e5bc0a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789971"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Voir une base de connaissances avec l’Explorateur Stockage

> [!Note]
> La base de connaissances est en préversion et ne doit pas être utilisée en production. L’[API REST Recherche cognitive Azure version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de .NET SDK pour l’instant.
>
Dans cet article, vous allez apprendre à connecter et explorer une base de connaissances à l’aide de l’Explorateur Stockage dans le portail Azure. Pour créer l’exemple de base de connaissances utilisé dans cette procédure pas à pas, consultez [Créer une base de connaissances dans le portail Azure](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Prérequis

+ Suivez les étapes décrites dans [Créer une base de connaissances dans le portail Azure](knowledge-store-create-portal.md) pour créer l’exemple de base de connaissances utilisé dans cette procédure pas à pas.

+ Vous aurez également besoin du nom du compte de stockage Azure utilisé pour créer la base de connaissances ainsi que de sa clé d’accès dans le portail Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Voir, modifier et interroger une base de connaissances dans l’Explorateur Stockage

1. Dans le portail Azure, ouvrez le compte de stockage que vous avez utilisé pour créer la base de connaissances.

1. Dans le volet de navigation de gauche du compte de stockage, cliquez sur **Explorateur Stockage**.

1. Développez la liste **TABLES** pour afficher la liste des projections de tables Azure créées durant l’exécution de l’Assistant **Importation des données** sur les exemples de données d’avis des clients concernant les hôtels.

Sélectionnez une table pour voir les données enrichies, notamment les phrases clés, les scores de sentiment, les données d’emplacement de latitude et de longitude, etc.

   ![Afficher des tables dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Afficher des tables dans l’Explorateur Stockage")

Pour changer le type de données d’une valeur de table ou pour changer des valeurs individuelles dans votre table, cliquez sur **Modifier**. Quand vous changez le type de données d’une colonne dans une ligne de table, il s’applique à toutes les lignes.

   ![Modifier une table dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Modifier une table dans l’Explorateur Stockage")

Pour exécuter des requêtes, cliquez sur **Requête** dans la barre de commandes, puis entrez vos conditions.  

   ![Interroger une table dans l’Explorateur Stockage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Interroger une table dans l’Explorateur Stockage")

## <a name="clean-up"></a>Nettoyer

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite.

## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à connecter cette base de connaissances à Power BI, consultez l’article suivant.

> [!div class="nextstepaction"]
> [Connexion avec Power BI](knowledge-store-connect-power-bi.md)

Pour apprendre à créer une base de connaissances à l’aide des API REST et de Postman, consultez l’article suivant.  

> [!div class="nextstepaction"]
> [Créer une base de connaissances avec REST](knowledge-store-howto.md)
