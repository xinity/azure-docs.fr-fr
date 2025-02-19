---
title: Copier des données depuis une base de données Teradata Vantage avec Azure Data Factory | Microsoft Docs
description: Découvrez le connecteur Teradata du service Data Factory, qui vous permet de copier des données Teradata Vantage vers des magasins de données pris en charge par Data Factory en tant que récepteurs.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: e538c8b00bddc8a2fa35b158c1e76f9033b73a56
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089186"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Copier des données depuis une base de données Teradata Vantage à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Version actuelle](connector-teradata.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Teradata Vantage. Il s’appuie sur la [vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Teradata est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de Teradata Vantage vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Teradata prend en charge :

- Teradata **versions 14.10, 15.0, 15.10, 16.0, 16.10 et 16.20**.
- Copie des données avec l’authentification **De base** ou **Windows**.
- Copie en parallèle à partir d’une source Teradata. Pour en savoir plus, voir [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata).

> [!NOTE]
>
> Après la publication de la version 3.18 du runtime d’intégration auto-hébergé, Azure Data Factory a mis à niveau le connecteur Teradata. Les charges de travail existantes qui utilisent le connecteur Teradata précédent sont toujours prises en charge. Cependant, il est préférable d’utiliser le nouveau connecteur pour les nouvelles charges de travail. Notez que le nouveau chemin d’accès requiert un ensemble différent de service lié, de jeu de données et de source de copie. Pour plus de détails de configuration, consultez les sections correspondantes ci-après.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Le runtime d’intégration fournit un pilote Teradata intégré, depuis la version 3.18. Vous n’avez pas besoin d’installer manuellement un pilote. Le pilote requiert l’installation du package Redistribuable Visual C++ pour Visual Studio 2012 Update 4 sur l’ordinateur du runtime d’intégration auto-hébergé. S’il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

Pour une version du runtime d’intégration auto-hébergé antérieure à 3.18, vous devez installer le [fournisseur de données .NET pour Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) version 14 ou ultérieure sur la machine exécutant le runtime d’intégration. 

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur Teradata.

## <a name="linked-service-properties"></a>Propriétés du service lié

Le service lié Teradata prend en charge les propriétés suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **Teradata**. | OUI |
| connectionString | Spécifie les informations requises pour se connecter à l’instance de Teradata. Consultez les exemples suivants.<br/>Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez la section [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| username | Spécifiez le nom d’utilisateur associé à la connexion à Teradata. S’applique lors de l’utilisation de l’authentification Windows. | Non |
| password | Spécifiez un mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Vous pouvez également choisir de [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). <br>S’applique lors de l’utilisation de l’authentification Windows ou du référencement du mot de passe dans Key Vault pour l’authentification de base. | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |OUI |

**Exemple : utilisation de l’authentification de base**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : utilisation d’une authentification Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> La charge utile suivante est toujours prise en charge. Cependant, nous vous invitons à utiliser la nouvelle charge utile dorénavant.

**Charge utile précédente :**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Cette section fournit une liste des propriétés prises en charge par le jeu de données Teradata. Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md).

Pour copier des données à partir de Teradata, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur `TeradataTable`. | OUI |
| database | Nom de l’instance Teradata. | Non (si « query » dans la source de l’activité est spécifié) |
| table | Nom de la table dans l’instance Teradata. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple :**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> Le jeu de données de type `RelationalTable` est toujours pris en charge. Toutefois, nous vous recommandons d’utiliser le nouveau jeu de données.

**Charge utile précédente :**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section fournit une liste des propriétés prises en charge par la source Teradata. Pour obtenir la liste complète des sections et propriétés disponibles pour la définition des activités, consultez [Pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata en tant que source

>[!TIP]
>Pour charger efficacement des données à partir de Teradata à l'aide du partitionnement des données, consultez la section [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata).

Pour copier des données à partir de Teradata, les propriétés prises en charge dans la section **source** de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source de l’activité de copie doit être définie sur `TeradataSource`. | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple `"SELECT * FROM MyTable"`.<br>Lorsque vous activez la charge partitionnée, vous devez utiliser les paramètres de partition intégrés correspondants dans votre requête. Pour consulter des exemples, voir [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non (si la table du jeu de données est spécifiée) |
| partitionOptions | Spécifie les options de partitionnement des données utilisées pour charger des données à partir de Teradata. <br>Les valeurs autorisées sont les suivantes : **Aucun** (par défaut), **Hachage** et **DynamicRange**.<br>Lorsqu’une option de partition est activée (autrement dit, pas `None`), le degré de parallélisme pour charger simultanément des données à partir de Teradata est contrôlé par le paramètre [`parallelCopies`](copy-activity-performance.md#parallel-copy) de l’activité de copie. | Non |
| partitionSettings | Spécifiez le groupe de paramètres pour le partitionnement des données. <br>S’applique lorsque l’option de partitionnement n’est pas `None`. | Non |
| partitionColumnName | Spécifiez le nom de la colonne source qu’utilisera le partitionnement par plages de valeurs ou hachage pour la copie en parallèle. S’il n’est pas spécifié, l'index primaire de la table sera automatiquement détecté et utilisé en tant que colonne de partition. <br>S’applique lorsque l’option de partitionnement est `Hash` ou `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfHashPartitionCondition` ou `?AdfRangePartitionColumnName` dans la clause WHERE. Consultez l’exemple de la section [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non |
| partitionUpperBound | Valeur maximale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque de l’option de partition est `DynamicRange`. Si vous avez recours à une requête pour récupérer des données sources, utilisez `?AdfRangePartitionUpbound` dans la clause WHERE. Pour consulter un exemple, voir [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non |
| partitionLowerBound | Valeur minimale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionLowbound` dans la clause WHERE. Pour consulter un exemple, voir [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non |

> [!NOTE]
>
> La source de copie de type `RelationalSource` est toujours prise en charge, mais ne gère pas la nouvelle charge en parallèle intégrée à partir de Teradata (options de partition). Toutefois, nous vous recommandons d’utiliser le nouveau jeu de données.

**Exemple : copie de données à l’aide de la requête de base sans partition**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Copie en parallèle à partir de Teradata

Le connecteur Teradata de Data Factory propose un partitionnement de données intégré pour copier des données à partir de Teradata, en parallèle. Vous trouverez des options de partitionnement de données dans la table **Source** de l’activité de copie.

![Capture d’écran représentant les options de partition](./media/connector-teradata/connector-teradata-partition-options.png)

Lorsque vous activez la copie partitionnée, Data Factory exécute des requêtes en parallèle sur votre source Teradata, afin de charger des données par partitions. Le degré de parallélisme est contrôlé via le paramètre [`parallelCopies`](copy-activity-performance.md#parallel-copy) sur l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur quatre, Data Factory génère et exécute simultanément quatre requêtes basées l'option de partition et les paramètres que vous avez spécifiés, chacune récupérant des données à partir de Teradata.

Il vous est recommandé d’activer la copie en parallèle avec partitionnement des données notamment lorsque vous chargez une grande quantité de données à partir de Teradata. Voici quelques suggestions de configurations pour différents scénarios. Lors de la copie de données dans un magasin de données basé sur des fichiers, il est recommandé de les écrire dans un dossier sous la forme de plusieurs fichiers (spécifiez uniquement le nom du dossier). Les performances seront meilleures qu’avec l’écriture dans un seul fichier.

| Scénario                                                     | Paramètres suggérés                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Chargement complet à partir d’une table volumineuse.                                   | **Option de partition** : Hachage. <br><br/>Lors de l’exécution, Data Factory détecte automatiquement la colonne PK, y applique un hachage et copie les données par partitions. |
| Chargez une grande quantité de données à l’aide d’une requête personnalisée.                 | **Option de partition** : Hachage.<br>**Requête** : `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour appliquer la partition par hachage. Si cette colonne n’est pas spécifiée, Data Factory détecte automatiquement la colonne PK de la table que vous avez spécifiée dans le jeu de données Teradata.<br><br>Lors de l’exécution, Data Factory remplace `?AdfHashPartitionCondition` par la logique de partition de hachage et l’envoie à Teradata. |
| Chargez une grande quantité de données à l’aide d’une requête personnalisée, qui dispose d’une colonne d’entiers avec valeur uniformément distribuée pour le partitionnement par plages de valeurs. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Requête**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données. Vous pouvez procéder au partitionnement par rapport à la colonne avec le type de données entier.<br>**Limite supérieure de partition** et **limite inférieure de partition** : Indiquez si vous souhaitez filtrer le contenu par rapport à la colonne de partition pour récupérer uniquement les données entre les plages inférieure et supérieure.<br><br>Lors de l’exécution, Data Factory remplace `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` et `?AdfRangePartitionLowbound` par le nom réel de la colonne et les plages de valeurs de chaque partition, et les envoie à Teradata. <br>Par exemple, si l’ID de la colonne de partition « ID » est défini sur une limite inférieure de 1 et une limite supérieure de 80, avec une copie en parallèle définie sur 4, Data Factory récupère les données via 4 partitions. Les ID sont inclus entre [1,20], [21, 40], [41, 60] et [61, 80], respectivement. |

**Exemple : requête avec partition par hachage**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exemple : requête avec partition dynamique par spécification de plages de valeurs**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mappage de type de données pour Teradata

Lorsque vous copiez des données à partir de Teradata, les mappages suivants s’appliquent. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données Teradata | Type de données intermédiaires d’Azure Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Chaîne |
| Clob |Chaîne |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Integer |Int32 |
| Interval Day |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Day To Hour |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Day To Minute |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Day To Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Hour |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Hour To Minute |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Hour To Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Minute |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Minute To Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Month |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Year |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Year To Month |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Number |Double |
| Période (date) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (heure) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (heure avec fuseau horaire) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (timestamp) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (timestamp avec fuseau horaire) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| SmallInt |Int16 |
| Temps |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |Chaîne |
| VarGraphic |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Xml |Non pris en charge. Appliquer un cast explicite dans la requête source. |


## <a name="lookup-activity-properties"></a>Propriétés de l’activité de recherche

Pour en savoir plus sur les propriétés, consultez [Activité de recherche](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
