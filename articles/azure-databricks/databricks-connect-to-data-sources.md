---
title: 'Se connecter à plusieurs sources de données à partir d’Azure Databricks '
description: Découvrez comment vous connecter à Azure SQL Database, Azure Data Lake Store, Stockage Blob, Cosmos DB, Event Hubs et Azure SQL Data Warehouse à partir d’Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 9b44db3e8ffc02d211f7f97404f0cdd8d319fe03
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597491"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Se connecter à des sources de données à partir d’Azure Databricks

Cet article contient des liens vers toutes les sources de données différentes dans Azure qui peuvent être connectées à Azure Databricks. Suivez les exemples de ces liens pour extraire des données à partir des sources de données Azure (par exemple, Stockage Blob Azure, Azure Event Hubs, etc.) dans un cluster Azure Databricks, puis exécutez des travaux analytiques sur ces données. 

## <a name="prerequisites"></a>Prérequis

* Vous devez disposer d’un espace de travail Azure Databricks et d’un cluster Spark. Suivez les instructions contenues dans [Démarrage rapide : Exécuter une tâche Spark sur Azure Databricks à l’aide du portail Azure](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Sources de données pour Azure Databricks

La liste suivante indique les sources de données dans Azure que vous pouvez utiliser avec Azure Databricks. Pour obtenir la liste complète des sources de données que vous pouvez utiliser avec Azure Databricks, consultez [Data sources for Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (Sources de données d’Azure Databricks).

- [Base de données Azure SQL](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Cette rubrique fournit l’API DataFrame pour la connexion aux bases de données SQL à l’aide de JDBC, et indique comment contrôler le parallélisme des lectures via l’interface JDBC. Elle contient également des exemples détaillés d’utilisation de l’API Scala et des exemples Python et Spark SQL abrégés.
- [Azure Data Lake Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Ce lien contient des exemples d’utilisation du principal du service Azure Active Directory pour s’authentifier auprès d’Azure Data Lake Storage. Elle fournit également des instructions pour accéder aux données dans Azure Data Lake Storage à partir d’Azure Databricks.

- [Stockage Blob Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Cette rubrique contient des exemples indiquant comment accéder directement à Stockage Blob Azure à partir d’Azure Databricks à l’aide de la clé d’accès ou de la signature d’accès partagé d’un conteneur donné. Cette rubrique fournit également des informations sur la façon d’accéder à Stockage Blob Azure à partir d’Azure Databricks à l’aide de l’API RDD.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Cette rubrique contient des instructions sur l’utilisation du [connecteur Spark pour Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) à partir d’Azure Databricks pour accéder aux données d’Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Cette rubrique contient des instructions sur l’utilisation du [connecteur Spark pour Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark) à partir d’Azure Databricks pour accéder aux données d’Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Cette rubrique contient des instructions sur l’utilisation du connecteur Azure SQL Data Warehouse pour se connecter à partir d’Azure Databricks.
    

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sources à partir desquelles vous pouvez importer des données dans Azure Databricks, consultez [Spark Data Sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#) (Sources de données Spark)


