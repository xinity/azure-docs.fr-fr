---
title: Copier des données à partir d’une table SAP à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données à partir d’une table SAP vers des magasins de données récepteurs pris en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 87f97d674b1dd4334ac0ca07648baa1e7cc6607a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089554"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copier des données d’une table SAP à l’aide d’Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’une table SAP. Pour plus d’informations, consultez l’article [Vue d’ensemble d’activité de copie](copy-activity-overview.md).

>[!TIP]
>Pour en savoir plus sur la prise en charge générale de l'intégration de données SAP par ADF, consultez le [livre blanc Intégration de données SAP à l'aide d'Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) qui contient une présentation détaillée, une comparaison et des conseils.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur de table SAP est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données à partir d’une table SAP vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur de table SAP prend en charge les opérations suivantes :

- Copie de données à partir d’une table SAP dans :

  - SAP ERP Central Component (SAP ECC) 7.01 ou version ultérieure (dans une pile de package de support SAP récente publiée après 2015).
  - SAP Business Warehouse (SAP BW) version 7.01 ou ultérieure (dans une pile SAP Support Package Stack postérieure à 2015).
  - SAP S/4HANA.
  - Autres produits de SAP Business Suite version 7.01 ou ultérieure (dans une pile SAP Support Package Stack postérieure à 2015).

- Copie de données à partir d'une table transparente SAP, d'une table mise en pool, d'une table clusterisée et d'une vue.
- Copie de données à l’aide de l’authentification de base ou de Secure Network Communications (SNC), si SNC est configuré.
- Connexion à un serveur d’applications SAP ou un serveur de messagerie SAP.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce connecteur de table SAP, vous devez effectuer les opérations suivantes :

- Configurez un runtime d’intégration auto-hébergé (version 3.17 ou ultérieure). Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md).

- Téléchargez la version 64 bits de [SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) à partir du site web de SAP et installez-la sur la machine dotée du runtime d’intégration auto-hébergé. Lors de l'installation, veillez à sélectionner l’option **Installer les assemblys dans le GAC** dans la fenêtre des **étapes de configuration facultatives**.

  ![Installer SAP Connector pour .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- L’utilisateur SAP utilisé dans le connecteur de table SAP Data Factory doit disposer des autorisations suivantes :

  - Autorisation d'utiliser des destinations RFC (Remote Function Call).
  - Autorisations relatives à l’activité Exécuter de l’objet d’autorisation S_SDSAUTH.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques au connecteur de table SAP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP BW Open Hub sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `type` doit être définie sur `SapTable`. | OUI |
| `server` | Nom du serveur sur lequel réside l’instance SAP.<br/>Permet de se connecter à un serveur d'applications SAP. | Non |
| `systemNumber` | Numéro du système SAP.<br/>Permet de se connecter à un serveur d'applications SAP.<br/>Valeur autorisée : Nombre décimal à deux chiffres représenté sous forme de chaîne. | Non |
| `messageServer` | Nom d’hôte du serveur de messagerie SAP.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `messageServerService` | Nom de service ou numéro de port du serveur de messagerie.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `systemId` | ID du système SAP où se trouve la table.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `logonGroup` | Groupe de connexion du système SAP.<br/>Permet de se connecter à un serveur de messagerie SAP. | Non |
| `clientId` | ID du client dans le système SAP.<br/>Valeur autorisée : Nombre décimal à trois chiffres représenté sous forme de chaîne. | OUI |
| `language` | Langue utilisée par le système SAP.<br/>La valeur par défaut est `EN`.| Non |
| `userName` | Nom de l’utilisateur ayant accès au serveur SAP. | OUI |
| `password` | Mot de passe de l’utilisateur. Marquez ce champ avec le type `SecureString` afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| `sncMode` | Indicateur d’activation SNC permettant d’accéder au serveur SAP où se trouve la table.<br/>Applicable si vous voulez utiliser SNC pour vous connecter au serveur SAP.<br/>Valeurs autorisées : `0` (off, valeur par défaut) ou `1` (on). | Non |
| `sncMyName` | Nom SNC de l’indicateur permettant d’accéder au serveur SAP où se trouve la table.<br/>S’applique si `sncMode` est défini sur On. | Non |
| `sncPartnerName` | Nom SNC du partenaire de communication permettant d’accéder au serveur SAP où se trouve la table.<br/>S’applique si `sncMode` est défini sur On. | Non |
| `sncLibraryPath` | Bibliothèque du produit de sécurité externe permettant d’accéder au serveur SAP où se trouve la table.<br/>S’applique si `sncMode` est défini sur On. | Non |
| `sncQop` | Niveau SNC Quality of Protection à appliquer.<br/>S’applique si `sncMode` est défini sur On. <br/>Les valeurs autorisées sont `1` (Authentification), `2` (Intégrité), `3` (Confidentialité), `8` (Par défaut), `9` (Maximum). | Non |
| `connectVia` | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Un runtime d’intégration auto-hébergé est nécessaire, comme indiqué précédemment dans [Prérequis](#prerequisites). |OUI |

**Exemple 1 : Se connecter à un serveur d'applications SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemple 2 : Se connecter à un serveur de messagerie SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Exemple 3 : Se connecter via SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés permettant de définir des jeux de données, consultez [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données de la table SAP.

Pour copier des données depuis et vers le service lié SAP BW Open Hub, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `type` doit être définie sur `SapTableResource`. | OUI |
| `tableName` | Nom de la table SAP à partir de laquelle copier des données. | OUI |

### <a name="example"></a>Exemples

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source de la table SAP.

### <a name="sap-table-as-source"></a>Table SAP en tant que source

Pour copier des données à partir d’une table SAP, les propriétés suivantes sont prises en charge :

| Propriété                         | Description                                                  | Obligatoire |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | La propriété `type` doit être définie sur `SapTableSource`.         | OUI      |
| `rowCount`                         | Nombre de lignes à récupérer.                              | Non       |
| `rfcTableFields`                   | Champs (colonnes) à copier à partir de la table SAP. Par exemple : `column0, column1`. | Non       |
| `rfcTableOptions`                  | Options permettant de filtrer les lignes d’une table SAP. Par exemple : `COLUMN0 EQ 'SOMEVALUE'`. Voir également la table des opérateurs de requête SAP plus loin dans cet article. | Non       |
| `customRfcReadTableFunctionModule` | Module de fonction RFC personnalisé qui peut être utilisé pour lire des données à partir de la table SAP.<br>Vous pouvez utiliser un module de fonction RFC personnalisé pour définir la façon dont les données sont récupérées à partir de votre système SAP et retournées à Data Factory. Le module de fonction personnalisé doit avoir une interface implémentée (importation, exportation, tables) similaire à `/SAPDS/RFC_READ_TABLE2`, l'interface par défaut utilisée par Data Factory. | Non       |
| `partitionOption`                  | Mécanisme de partition permettant d’effectuer une lecture à partir d’une table SAP. Les options prises en charge sont les suivantes : <ul><li>`None`</li><li>`PartitionOnInt` (entier normal ou valeurs entières avec remplissage à l’aide de zéros à gauche ; par exemple, `0000012345`)</li><li>`PartitionOnCalendarYear` (4 chiffres au format « AAAA »)</li><li>`PartitionOnCalendarMonth` (6 chiffres au format « AAAAMM »)</li><li>`PartitionOnCalendarDate` (8 chiffres au format « AAAAMMJJ »)</li></ul> | Non       |
| `partitionColumnName`              | Nom de la colonne utilisée pour partitionner les données.                | Non       |
| `partitionUpperBound`              | Valeur maximale de la colonne spécifiée dans `partitionColumnName` qui sera utilisée pour poursuivre le partitionnement. | Non       |
| `partitionLowerBound`              | Valeur minimale de la colonne spécifiée dans `partitionColumnName` qui sera utilisée pour poursuivre le partitionnement. | Non       |
| `maxPartitionsNumber`              | Nombre maximal de partitions à utiliser pour diviser les données.     | Non       |

>[!TIP]
>Si votre table SAP contient un grand volume de données, par exemple plusieurs milliards de lignes, utilisez `partitionOption` et `partitionSetting` pour fractionner les données en partitions plus petites. Dans ce cas, les données sont lues par partition et chaque partition de données est extraite de votre serveur SAP via un seul appel RFC.<br/>
<br/>
>Si nous prenons l’exemple de `partitionOption` avec la valeur `partitionOnInt`, le nombre de lignes dans chaque partition est calculé à l’aide de cette formule : (nombre total de lignes comprises entre `partitionUpperBound` et `partitionLowerBound`)/`maxPartitionsNumber`.<br/>
<br/>
>Pour charger des partitions de données en parallèle afin d’accélérer la copie, le degré parallèle est contrôlé par le paramètre [`parallelCopies`](copy-activity-performance.md#parallel-copy) de l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur quatre, Data Factory génère et exécute simultanément quatre requêtes basées l’option de partition et les paramètres que vous avez spécifiés, chacune récupérant des données à partir de votre table SAP. Nous vous recommandons vivement de faire de `maxPartitionsNumber` un multiple de la valeur de la propriété `parallelCopies`. Lors de la copie de données dans un magasin de données basé sur des fichiers, il est également recommandé de les écrire dans un dossier sous la forme de plusieurs fichiers (spécifiez uniquement le nom du dossier). Les performances seront meilleures qu'avec l'écriture dans un seul fichier.

Dans `rfcTableOptions`, vous pouvez utiliser les opérateurs de requête SAP courants suivants pour filtrer les lignes :

| Operator | Description |
| :------- | :------- |
| `EQ` | Égal à |
| `NE` | Non égal à |
| `LT` | Inférieur à |
| `LE` | Inférieur ou égal à |
| `GT` | Supérieur à |
| `GE` | Supérieur ou égal à |
| `LIKE` | Comme dans `LIKE 'Emma%'` |

### <a name="example"></a>Exemples

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mappages de type de données pour une table SAP

Lors de la copie de données à partir d’une table SAP, les mappages suivants sont utilisés à partir des types de données de table SAP en types de données intermédiaires Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type SAP ABAP | Type de données intermédiaires d’Azure Data Factory |
|:--- |:--- |
| `C` (Chaîne) | `String` |
| `I` (Entier) | `Int32` |
| `F` (Flottant) | `Double` |
| `D` (Date) | `String` |
| `T` (Heure) | `String` |
| `P` P (« packed » DCB, Devise, Décimal, Qté) | `Decimal` |
| `N` (Numérique) | `String` |
| `X` (Données binaires et brutes) | `String` |

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie d’Azure Data Factory, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
