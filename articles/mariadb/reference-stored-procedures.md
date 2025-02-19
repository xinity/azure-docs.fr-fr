---
title: Procédures stockées de gestion Azure Database for MariaDB
description: Découvrez les procédures stockées d’Azure Database pour MySQL qui vous aideront à configurer la réplication des donnée entrantes, à définir le fuseau horaire et à supprimer des requêtes.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 0a33edeac735502964427ddc3b05076fb9fac969
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973466"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Procédures stockées de gestion Azure Database for MariaDB

Des procédures stockées sont disponibles sur les serveurs Azure Database for MariaDB pour faciliter la gestion de votre serveur MariaDB. Cela comprend la gestion des connexions de votre serveur, des requêtes et de la configuration de la réplication des données entrantes.  

## <a name="data-in-replication-stored-procedures"></a>Procédures stockées de réplication des données entrantes

La réplication des données entrantes permet de synchroniser les données à partir d’un serveur MariaDB qui s’exécute en local, dans des machines virtuelles ou des services de base de données hébergés par d’autres fournisseurs cloud dans le service Azure Database for MariaDB.

Les procédures stockées suivantes sont utilisées pour définir ou supprimer la réplication des données entrantes entre un serveur maître et un réplica.

|**Nom de la procédure stockée**|**Paramètres d’entrée**|**Paramètres de sortie**|**Remarque sur l’utilisation**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Pour transférer des données en mode SSL, ajoutez le contexte du certificat d’autorité de certification dans le paramètre master_ssl_ca. </br><br>Pour transférer des données sans SSL, transmettez une chaîne vide dans le paramètre master_ssl_ca.|
|*mysql.az_replication _start*|N/A|N/A|Lance la réplication.|
|*mysql.az_replication _stop*|N/A|N/A|Arrête la réplication.|
|*mysql.az_replication _remove_master*|N/A|N/A|Supprime la relation de réplication entre le serveur maître et le réplica.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Ignore une erreur de réplication.|

Pour configurer la réplication des données entrantes entre un serveur maître et un réplica dans Azure Database for MariaDB, consultez [Guide pratique pour configurer la réplication des données entrantes](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Autres procédures stockées

Les procédures stockées suivantes sont disponibles dans Azure Database for MariaDB pour gérer votre serveur.

|**Nom de la procédure stockée**|**Paramètres d’entrée**|**Paramètres de sortie**|**Remarque sur l’utilisation**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/A|Équivaut à la commande [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Met fin à la connexion associée au processlist_id fourni après avoir mis fin à l’exécution de toute instruction exécutée actuellement par la connexion.|
|*mysql.az_kill_query*|processlist_id|N/A|Équivaut à la commande [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Met fin à l’instruction exécutée actuellement par la connexion. Laisse la connexion active.|
|*mysql.az_load_timezone*|N/A|N/A|Charge les tables de fuseau horaire pour permettre d’affecter des valeurs nommées au paramètre `time_zone` (par exemple « US/Pacific »).|

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment configurer la [réplication des données entrantes](howto-data-in-replication.md).
- Découvrez comment utiliser les [tables de fuseau horaire](howto-server-parameters.md#working-with-the-time-zone-parameter).