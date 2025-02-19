---
title: État du scénario de migration de base de données | Microsoft Docs
description: Obtenez davantage d’informations sur l’état des scénarios de migration pris en charge par Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9ea070ea86a4d0d5e7101e82450235a59c936d2d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376408"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>État des scénarios de migration pris en charge par Azure Database Migration Service

Azure Database Migration Service est conçu pour prendre en charge différents scénarios de migration (paires source/cible), aussi bien pour le mode hors connexion (migration ponctuelle) que le mode en ligne (synchronisation continue). Azure Database Migration Service couvre de plus en plus de scénarios au fil du temps. De nouveaux scénarios sont régulièrement ajoutés. Cet article identifie les scénarios de migration qui sont actuellement pris en charge par Azure Database Migration Service et l’état de chaque scénario (préversion privée, préversion publique ou disposition générale).

## <a name="offline-versus-online-migrations"></a>Migrations hors connexion et en ligne

Avec Azure Database Migration Service, vous pouvez opter pour une migration hors connexion ou en ligne. Avec les migrations *hors connexion*, les temps d’arrêt de l’application commencent en même temps que la migration. Pour limiter le temps d’arrêt au temps nécessaire pour basculer vers le nouvel environnement une fois la migration terminée, utilisez une migration *en ligne*. Nous vous recommandons de tester une migration hors connexion pour déterminer si le temps d’arrêt est acceptable ; dans le cas contraire, privilégiez une migration en ligne.

## <a name="migration-scenario-status"></a>État du scénario de migration

L’état des scénarios de migration pris en charge par Azure Database Migration Service varie dans le temps. En règle générale, les scénarios sont d’abord publiés en **préversion privée**. Pour participer à une préversion privée, les clients doivent soumettre une candidature par le biais du [site DMS Preview](https://aka.ms/dms-preview). Au terme de la préversion privée, le scénario passe à l’état de **préversion publique**. Les utilisateurs d’Azure Database Migration Service peuvent essayer des scénarios de migration en préversion directement à partir de l’interface utilisateur. Aucune inscription n’est nécessaire.  Toutefois, il se peut que les scénarios de migration en préversion publique ne soient pas disponibles dans toutes les régions et subissent des modifications supplémentaires avant la version finale. Au terme de la préversion publique, le scénario passe à l’état de **disponibilité générale**. La disponibilité générale est l’état de la version finale : les fonctionnalités sont complètes et accessibles à tous les utilisateurs.

## <a name="migration-scenario-support"></a>Prise en charge du scénario de migration

Les tableaux suivants indiquent les scénarios de migration qui sont pris en charge lorsqu’Azure Database Migration Service est utilisé.

> [!NOTE]
> Si un scénario apparaît dans la liste ci-dessous, mais qu’il ne s’affiche pas dans l’interface utilisateur, veuillez envoyer un courrier pour [Poser des questions sur les migrations de base de données Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

> [!IMPORTANT]
> Pour voir tous les scénarios actuellement pris en charge par Azure Database Migration Service en préversion privée, consultez le [site DMS Preview](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Prise en charge de la migration hors connexion (migration ponctuelle)

Le tableau suivant présente la prise en charge d’Azure Database Migration Service pour les migrations hors connexion.

| Cible  | Source | Support | Statut |
| ------------- | ------------- |:-------------:|:-------------:|
| **Base de données Azure SQL** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Base de données Azure SQL MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Machine virtuelle Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Base de données Azure pour MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Base de données Azure pour PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Prise en charge de la migration en ligne (synchronisation continue)

Le tableau suivant présente la prise en charge d’Azure Database Migration Service pour les migrations en ligne.

| Cible  | Source | Support | Statut |
| ------------- | ------------- |:-------------:|:-------------:|
| **Base de données Azure SQL** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Base de données Azure SQL MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Préversion privée |
| **Machine virtuelle Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Base de données Azure pour MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Base de données Azure pour PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Préversion publique |

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation d’Azure Database Migration Service et de la mise à disponibilité régionale, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](dms-overview.md).
