---
title: Vidage sur incident et restauration dans Azure Database pour PostgreSQL (serveur unique)
description: Explique comment extraire une base de données PostgreSQL dans un fichier de sauvegarde et comment restaurer des données à partir d’un fichier créé par la commande pg_dump dans Azure Database pour PostgreSQL (serveur unique).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 55e802aa1f7bdf0d67d1a9c3f020d255afdc8130
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261900"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migration de votre base de données PostgreSQL par vidage et restauration
Vous pouvez utiliser la commande [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) pour extraire une base de données PostgreSQL vers un fichier de vidage, et la commande [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) pour restaurer la base de données PostgreSQL à partir d’un fichier d’archive créé par pg_dump.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md) avec des règles de pare-feu autorisant l’accès et la base de données sous-jacente.
- Utilitaires de ligne de commande [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) installés

Suivez les étapes ci-dessous pour vider et restaurer votre base de données PostgreSQL :

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Création d’un fichier de vidage à l’aide de pg_dump qui contient les données à charger
Pour sauvegarder une base de données PostgreSQL existante en local ou sur une machine virtuelle, exécutez la commande suivante :
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Par exemple, si vous avez un serveur local contenant une base de données appelée **testdb**
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pg_restore"></a>Restauration des données dans la base de données cible pour PostrgeSQL à l’aide de pg_restore
Après avoir créé la base de données cible, vous pouvez utiliser la commande pg_restore et le paramètre -d, --dbname pour restaurer les données dans la base de données cible à partir du fichier de vidage.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
L’ajout du paramètre --no-owner contraint tous les objets créés au cours de la restauration à appartenir à l’utilisateur désigné par --username. Pour plus d’informations, consultez la documentation PostgreSQL officielle sur [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Si votre serveur PostgreSQL nécessite des connexions SSL (qui sont activées par défaut sur les serveurs Azure Database pour PostgreSQL), définissez une variable d’environnement `PGSSLMODE=require` pour que l’outil pg_restore se connecte avec SSL. Sans connexion SSL, l’erreur suivante peut s’afficher : `FATAL:  SSL connection is required. Please specify SSL options and retry.`.
>
> Sur la ligne de commande Windows, exécutez la commande `SET PGSSLMODE=require` avant d’exécuter la commande pg_restore. Dans Linux ou Bash, exécutez la commande `export PGSSLMODE=require` avant d’exécuter la commande pg_restore.
>

Dans cet exemple, restaurez les données à partir du fichier de vidage **testdb.dump** dans la base de données **mypgsqldb** sur le serveur cible **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optimisation du processus de migration

Une façon de migrer votre base de données PostgreSQL existante vers le service Azure Database pour PostgreSQL consiste à sauvegarder la base de données sur la source et à la restaurer dans Azure. Pour réduire le temps nécessaire pour effectuer la migration, envisagez d’utiliser les paramètres suivants avec les commandes de sauvegarde et de restauration.

> [!NOTE]
> Pour obtenir des informations détaillées sur la syntaxe, consultez les articles [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) et [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Pour la sauvegarde
- Effectuez la sauvegarde avec le commutateur -Fc pour pouvoir effectuer la restauration en parallèle afin de l’accélérer. Par exemple :

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Pour la restauration
- Nous vous suggérons de déplacer le fichier de sauvegarde vers une machine virtuelle Azure dans la même région que le serveur Azure Database pour PostgreSQL vers lequel vous effectuez la migration, et d’effectuer l’opération pg_restore à partir de cette machine virtuelle pour réduire la latence du réseau. Nous vous recommandons également de créer la machine virtuelle en activant l’[accélération réseau](../virtual-network/create-vm-accelerated-networking-powershell.md).

- Cela doit être déjà fait par défaut, mais ouvrez le fichier de vidage pour vérifier que les instructions de création d’index figurent après l’insertion des données. Si tel n’est pas le cas, placez les instructions de création d’index après que les données ont été insérées.

- Restaurez avec les commutateurs -Fc et -j *#* pour mettre en parallèle la restauration. *#* est le nombre de cœurs présents sur le serveur cible. Vous pouvez également essayer avec *#* défini sur le double du nombre de cœurs du serveur cible pour voir l’impact. Par exemple :

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Vous pouvez également modifier le fichier de vidage en ajoutant la commande *set synchronous_commit = off;* au début, et la commande *set synchronous_commit = on;* à la fin. Ne pas l’activer à la fin, avant que les applications modifient les données, peut entraîner une perte de données par la suite.

- Sur le serveur cible Azure Database pour PostgreSQL, procédez comme suit avant la restauration :
    - Désactivez le suivi des performances des requêtes, car ces statistiques ne sont pas nécessaires pendant la migration. Pour ce faire, définissez pg_stat_statements.track, pg_qs.query_capture_mode et pgms_wait_sampling.query_capture_mode sur NONE.

    - Utilisez une référence SKU à haute capacité de calcul et à mémoire élevée (32 vCore à mémoire optimisée, par exemple) pour accélérer la migration. Vous pourrez facilement revenir à votre référence SKU préférée à l'issue de la restauration. Plus la référence SKU est élevée, plus le parallélisme atteint peut être important si vous augmentez le paramètre `-j` correspondant dans la commande pg_restore. 

    - Un nombre élevé d'IOPS sur le serveur cible peut améliorer les performances de restauration. Vous pouvez configurer un nombre d'IOPS plus élevé en augmentant la taille de stockage du serveur. Ce paramètre n'est pas réversible. À vous de déterminer si un nombre plus élevé d'IOPS serait bénéfique à votre charge de travail réelle à l'avenir.

Pensez à tester et valider ces commandes dans un environnement de test avant de les utiliser en production.

## <a name="next-steps"></a>Étapes suivantes
- Pour migrer une base de données PostgreSQL par exportation et importation, consultez l’article [Migrer votre base de données PostgreSQL par exportation et importation](howto-migrate-using-export-and-import.md).
- Pour plus d’informations sur la migration de bases de données vers Azure Database pour PostgreSQL, consultez le [Guide de migration des bases de données](https://aka.ms/datamigration).
