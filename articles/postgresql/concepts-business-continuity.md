---
title: Vue d’ensemble de la continuité d’activité avec Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit la continuité d’activité (limite de restauration dans le temps, interruption de centre de données, géo-restauration) quand vous utilisez Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 3623611bcd22486d90651c6e8b6880c6de1de0c5
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950096"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Vue d’ensemble de la continuité d’activité avec Azure Database pour PostgreSQL - Serveur unique

Cette vue d’ensemble décrit les fonctionnalités de continuité d’activité et de reprise après sinistre que fournit Azure Database pour PostgreSQL. Découvrez les options de reprise à la suite d’événements d’interruption susceptibles d’entraîner une perte de données ou une indisponibilité de votre base de données et de votre application. Connaissez la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités que vous pouvez utiliser pour garantir la continuité d’activité

Azure Database pour PostgreSQL propose des fonctionnalités de continuité d’activité, notamment des sauvegardes automatisées et la possibilité pour les utilisateurs de lancer une géorestauration. Chacune de ces fonctionnalités présente des caractéristiques spécifiques concernant le temps de récupération estimé (ERT) et le risque de perte de données. Une fois que vous avez compris ces options, vous pouvez choisir celles qui vous conviennent et les utiliser ensemble dans différents scénarios. Au moment d’élaborer votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption, c’est-à-dire votre objectif de délai de récupération (RTO). Vous devez aussi comprendre la quantité maximale des récentes mises à jour de données (intervalle) que l’application peut accepter de perdre lors de la reprise après l’événement d’interruption, c’est-à-dire votre objectif de point de récupération (RPO).

Le tableau suivant compare l’ERT et le RPO pour les fonctionnalités disponibles :

| **Fonctionnalité** | **De base** | **Usage général** | **Mémoire optimisée** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Limite de restauration dans le temps à partir de la sauvegarde | N’importe quel point de restauration dans la période de rétention | N’importe quel point de restauration dans la période de rétention | N’importe quel point de restauration dans la période de rétention |
| Géo-restauration à partir de sauvegardes répliquées géographiquement | Non pris en charge | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Il n’est **pas** possible de restaurer des serveurs supprimés. Si vous supprimez le serveur, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. Utilisez le [verrouillage des ressources Azure](../azure-resource-manager/resource-group-lock-resources.md) pour éviter la suppression accidentelle de votre serveur.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Récupérer un serveur après une erreur d’utilisateur ou d’application

Vous pouvez vous servir des sauvegardes du service pour récupérer un serveur à la suite de plusieurs événements d’interruption. Un utilisateur peut supprimer par inadvertance des données, une table importante, voire une base de données tout entière. Une application peut accidentellement remplacer des données correctes par des données incorrectes en raison d’une défaillance d’application, etc.

Vous pouvez procéder à une **restauration à un point dans le temps** afin de créer une copie de votre serveur à un point dans le temps valide connu. Ce moment donné doit se situer dans la période de rétention de sauvegarde que vous avez configurée pour votre serveur. Une fois les données restaurées sur le nouveau serveur, vous pouvez remplacer le serveur d’origine par le serveur nouvellement restauré ou copier les données nécessaires du serveur restauré sur le serveur d’origine.

## <a name="recover-from-an-azure-data-center-outage"></a>Récupérer après une panne du centre de données Azure

Bien que le fait soit rare, un centre de données Azure peut subir une panne. En cas de panne, il s’ensuit une interruption d’activité dont la durée peut se limiter à quelques minutes, mais aussi se compter en heures.

Vous pouvez attendre que votre serveur redevienne disponible une fois la panne réparée au niveau du centre de données. Cette solution vaut pour les applications qui peuvent s’accommoder d’un certain temps d’indisponibilité du serveur, par exemple dans un environnement de développement. Quand un centre de données connaît une panne, vous ne savez pas combien de temps cela peut durer. Cette solution n’est donc valable que si vous n’avez pas besoin du serveur pendant un certain temps.

## <a name="geo-restore"></a>Géo-restauration

La fonctionnalité de géorestauration permet de restaurer le serveur à l’aide de sauvegardes géoredondantes. Les sauvegardes sont hébergées dans la [région appairée](../best-practices-availability-paired-regions.md) de votre serveur. Vous pouvez effectuer une restauration à partir de ces sauvegardes dans n’importe quelle autre région. La géorestauration crée un serveur avec les données issues des sauvegardes. Pour plus d’informations sur la géorestauration, consultez cet [article sur les concepts de sauvegarde et de restauration](concepts-backup.md).

> [!IMPORTANT]
> La géorestauration n’est possible que si vous avez provisionné le serveur avec le stockage de sauvegardes géoredondantes. Si vous souhaitez basculer des sauvegardes redondantes localement aux sauvegardes géoredondantes pour un serveur existant, vous devez effectuer une copie de sauvegarde de votre serveur existant en utilisant pg_dump et la restaurer vers un serveur nouvellement créé configuré avec des sauvegardes géoredondantes.

## <a name="cross-region-read-replicas"></a>Réplicas en lecture inter-régions
Vous pouvez utiliser des réplicas en lecture inter-régions pour améliorer la planification de la continuité d’activité et de la reprise d’activité. Les réplicas en lecture sont mis à jour de manière asynchrone à l’aide de la technologie de réplication physique de PostgreSQL. Pour plus d’informations sur les réplicas en lecture, les régions disponibles et le basculement, consultez cet [article sur les concepts relatifs aux réplicas en lecture](concepts-read-replicas.md). 

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les sauvegardes automatisées, consultez [Sauvegardes dans Azure Database pour PostgreSQL](concepts-backup.md). 
- Découvrez comment effectuer une restauration à l’aide du [portail Azure](howto-restore-server-portal.md) ou de l’interface [Azure CLI](howto-restore-server-cli.md).
- Apprenez-en davantage sur les [réplicas en lecture dans Azure Database pour PostgreSQL](concepts-read-replicas.md).