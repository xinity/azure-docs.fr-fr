---
title: Créer des compteurs de performances pour suivre les performances du gestionnaire de cartes de partitions
description: Classe ShardMapManager et compteurs de performances pour le routage dépendant des données
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568678"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Créer des compteurs de performances pour suivre les performances du gestionnaire de cartes de partitions

Les compteurs de performances sont utilisés pour suivre les performances des opérations de [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md). Ces compteurs sont accessibles dans l’Analyseur de performances, sous la catégorie « Base de données élastique : Gestion des partitions ».

Vous pouvez recueillir les performances d’un [gestionnaire de cartes de partitions](sql-database-elastic-scale-shard-map-management.md), en particulier lorsque vous utilisez un [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md). Les compteurs sont créés à l’aide des méthodes de la classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Pour vous procurer la version la plus récente :** accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Vous pouvez également consulter l’article [Mettre à niveau une application pour utiliser la dernière version de la bibliothèque cliente de bases de données élastiques](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Prérequis

* Pour créer la catégorie et les compteurs de performances, l’utilisateur doit être membre du groupe **Administrateurs** local groupe sur l’ordinateur qui héberge l’application.  
* Pour créer une instance de compteur de performances et mettre à jour les compteurs, l’utilisateur doit être membre du groupe **Administrateurs** ou du groupe **Utilisateurs de l’Analyseur de performances**.

## <a name="create-performance-category-and-counters"></a>Création de catégories et de compteurs de performances

Pour créer les compteurs, appelez la méthode CreatePerformanceCategoryAndCounters de la [classe ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Seul un administrateur peut exécuter la méthode :

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Vous pouvez également utiliser [ce](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script PowerShell pour exécuter la méthode.
La méthode crée les compteurs de performances suivants :  

* **Mappages mise en cache** : nombre de mappages mis en cache pour la carte de partitions.
* **Opérations DDR/s** : taux d’opérations de routage dépendant des données pour la carte de partitions. Ce compteur est mis à jour lorsqu’un appel à [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) permet d’établir la connexion à la partition de destination.
* **Nb de correspondances dans le cache lors de la recherche de mappages** : taux de réussite des opérations de recherche de mappages dans le cache pour la carte de partitions.
* **Nb d’échecs de correspondance dans le cache lors de la recherche de mappages** : taux d’échec des opérations de recherche de mappages dans le cache pour la carte de partitions.
* **Mappages ajoutés ou mis à jour dans le cache/s** : taux d’ajout ou de mise à jour de mappages dans le cache pour la carte de partitions.
* **Mappages supprimés du cache/s** : taux de suppression de mappages dans le cache pour la carte de partitions.

Les compteurs de performances sont créés pour chaque carte de partitions mises en cache par processus.  

## <a name="notes"></a>Notes

Les événements suivants déclenchent la création des compteurs de performances :  

* Initialisation de [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) avec [chargement hâtif](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), si l’objet ShardMapManager contient des cartes de partitions, avec les méthodes [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) et [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager).
* Recherche réussie d’une carte de partitions (à l’aide de [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Création réussie de la carte de partitions à l’aide de CreateShardMap().

Les compteurs de performance seront mis à jour par toutes les opérations du cache sur la carte de partitions et sur les mappages. Suppression réussie de la carte de partitions à l'aide de DeleteShardMap(), entraînant la suppression de l'instance des compteurs de performances.  

## <a name="best-practices"></a>Bonnes pratiques

* Il est recommandé de créer la catégorie et les compteurs de performances une fois seulement avant la création de l’objet ShardMapManager. Chaque exécution de la commande CreatePerformanceCategoryAndCounters() efface les compteurs précédents (perte de données signalée par toutes les instances) et en crée de nouveaux.  
* Des instances de compteurs de performances sont créées pour chaque processus. Toute panne de l’application ou suppression d’une carte de partitions dans le cache entraîne la suppression des instances de compteurs de performances.  

### <a name="see-also"></a>Vous pouvez également consulter l’article

[Vue d’ensemble des fonctionnalités de base de données élastique](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
