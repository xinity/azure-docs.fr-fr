---
title: Configurer les paramètres de service dans Azure Database pour MariaDB
description: Cet article explique comment configurer les paramètres de service dans Azure Database pour MariaDB à l’aide de l’utilitaire en ligne de commande Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 4e0bf45f1c67a5e07d6ed632f6560d094b673c0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61040035"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personnaliser les paramètres de configuration du serveur à l’aide d’Azure CLI
Vous pouvez répertorier, afficher et mettre à jour les paramètres de configuration d’un serveur Azure Database pour MariaDB à l’aide d’Azure CLI, l’utilitaire en ligne de commande Azure. Un sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Un serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- L’utilitaire en ligne de commande [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Répertorier les paramètres de configuration de serveur pour Azure Database pour MariaDB
Pour répertorier tous les paramètres modifiables sur un serveur, ainsi que leurs valeurs, exécutez la commande [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list).

Vous pouvez répertorier les paramètres de configuration du serveur **mydemoserver.mariadb.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Pour obtenir la définition de chacun des paramètres répertoriés, consultez la section de référence MariaDB dans [Server System Variables](https://mariadb.com/kb/en/library/server-system-variables/) (Variables système du serveur).

## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur
Pour afficher les détails d’un paramètre de configuration particulier pour un serveur, exécutez la commande [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show).

Cet exemple affiche les détails du paramètre de configuration de serveur **slow\_query\_log** pour le serveur **mydemoserver.mariadb.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modifier une valeur de paramètre de configuration de serveur
Vous pouvez également modifier la valeur d’un paramètre de configuration de serveur, ce qui a pour effet de mettre à jour la valeur de configuration sous-jacente du moteur du serveur MariaDB. Pour mettre à jour la configuration, exécutez la commande [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set). 

Pour mettre à jour le paramètre de configuration de serveur **slow\_query\_log** pour le serveur **mydemoserver.mariadb.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Si vous souhaitez réinitialiser la valeur d’un paramètre de configuration, omettez le paramètre `--value` facultatif. Le service applique alors la valeur par défaut. Pour l’exemple ci-dessus, on aurait ce qui suit :
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Ce code réinitialise la configuration **slow\_query\_log** à la valeur par défaut **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Utilisation avec le paramètre de fuseau horaire

### <a name="populating-the-time-zone-tables"></a>Remplissage des tables de fuseaux horaires

Les tables de fuseaux horaires sur votre serveur peuvent être remplies en appelant la procédure stockée `az_load_timezone` à partir d’un outil tel que la ligne de commande MariaDB ou MariaDB Workbench.

> [!NOTE]
> Si vous exécutez la commande `az_load_timezone` à partir de MariaDB Workbench, vous devrez peut-être d’abord désactiver le mode de mise à jour sécurisée à l’aide de `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Pour afficher les valeurs de fuseau horaire disponibles, exécutez la commande suivante :

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Définition du fuseau horaire global

Le fuseau horaire global peut être défini à l’aide de la commande [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set).

La commande suivante met à jour le paramètre de configuration de serveur **time\_zone** du serveur **mydemoserver.mariadb.database.azure.com** dans le groupe de ressources **myresourcegroup** sur **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Définition du fuseau horaire de la session

Le fuseau horaire de la session peut être défini en exécutant la commande `SET time_zone` à partir d’un outil tel que la ligne de commande MariaDB ou MariaDB Workbench. L’exemple ci-dessous définit le fuseau horaire **US/Pacific**.  

```sql
SET time_zone = 'US/Pacific';
```

Reportez-vous à la documentation de MariaDB relative aux [fonction de date et heure](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Étapes suivantes

- Guide pratique pour configurer des [paramètres de serveur dans le portail Azure](howto-server-parameters.md)
