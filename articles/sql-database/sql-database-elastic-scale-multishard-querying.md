---
title: Interroger des bases de données Azure SQL partitionnées | Microsoft Docs
description: Exécutez des requêtes en utilisant la bibliothèque cliente des bases de données élastiques.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 471af9e1bc699ccaa8bc930ab930d6d40bbdc984
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568366"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Requête sur plusieurs partitions à l’aide d’outils de base de données élastique

## <a name="overview"></a>Vue d'ensemble

Avec les [outils des bases de données élastiques](sql-database-elastic-scale-introduction.md), vous pouvez créer des solutions de base de données partitionnée. **Requête sur plusieurs partitions** est utilisée pour des tâches telles que la collecte de données / la création de rapports qui nécessitent l'exécution d'une requête qui s'étend sur plusieurs partitions. (Comparez cela au [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)qui effectue tout le travail sur une partition unique.)

1. Obtenez un **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) ou **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) à l’aide de la méthode **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), ou **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)). Consultez les rubriques [Construction d’un objet ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) et [Obtenir un objet RangeShardMap ou ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Créez un objet **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Créez **MultiShardStatement ou MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Définissez la **propriété CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) sur une commande T-SQL.
5. Exécutez la commande en appelant la méthode **ExecuteQueryAsync ou ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Affichez les résultats à l’aide de la classe **MultiShardResultSet ou MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Exemples

Le code suivant illustre l'utilisation de la requête sur plusieurs partitions à l'aide d'une **ShardMap** donnée nommée *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

La principale différence est la construction de connexions à plusieurs partitions. Lorsque **SqlConnection** fonctionne sur une base de données individuelle, l’objet **MultiShardConnection** utilise une ***collection de partitions*** comme entrée. Remplissez la collection de partitions à partir d'une carte de partitions. La requête est ensuite exécutée sur la collection de partitions à l'aide de la sémantique **UNION ALL** pour assembler un seul résultat global. Le nom de la partition d'où provient la ligne peut éventuellement être ajouté à la sortie à l'aide de la propriété sur la commande **ExecutionOptions** .

Notez l'appel à **myShardMap.GetShards()** . Cette méthode récupère toutes les partitions de la carte de partitions et offre un moyen facile d’exécuter une requête sur toutes les bases de données concernées. La collection de partitions pour une requête sur plusieurs partitions peut être affinée davantage en effectuant une requête LINQ sur la collection retournée par l'appel à **myShardMap.GetShards()** . En combinaison avec la stratégie de résultats partiels, la capacité actuelle de l'interrogation de plusieurs partitions a été conçue pour fonctionner correctement avec des dizaines, voire des centaines, de partitions.

Une limitation de l'interrogation de plusieurs partitions est actuellement le manque de validation des partitions et shardlets interrogés. Tandis que le routage dépendant des données vérifie qu'une partition donnée fait partie de la carte de partitions au moment de l'interrogation, les requêtes sur plusieurs partitions n'effectuent pas cette vérification. De ce fait, les requêtes sur plusieurs partitions peuvent s’exécuter sur des bases de données qui ont été supprimées de la carte de partitions.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Requêtes sur plusieurs partitions et opérations de fractionnement et de fusion

Les requêtes sur plusieurs partitions ne vérifient pas si les shardlets de la base de données interrogée participent à des opérations de fractionnement et de fusion en cours. (Consultez [Mise à l’échelle utilisant l’outil de fractionnement et de fusion de bases de données élastiques](sql-database-elastic-scale-overview-split-and-merge.md).) Cela peut entraîner des incohérences avec des lignes du même shardlet qui s’affichent pour plusieurs bases de données dans la même requête sur plusieurs partitions. Tenez compte de ces limitations et envisagez de purger les opérations de fractionnement et de fusion en cours et les modifications apportées à la carte de partitions lors de l’exécution de requêtes sur plusieurs partitions.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]