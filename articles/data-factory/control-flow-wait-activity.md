---
title: Activité Wait dans Azure Data Factory | Microsoft Docs
description: L’activité Wait suspend l’exécution du pipeline pendant la période spécifiée.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 48a722979e61209a855dd1fec22fcdcc756ae1ce
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142419"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Exécuter l’activité Wait dans Azure Data Factory
Quand vous utilisez une activité Wait dans un pipeline, celui-ci attend pendant la période spécifiée avant de poursuivre l’exécution des activités suivantes. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
Nom | Nom de l’activité `Wait`. | Chaîne | OUI
Type | Doit être défini sur **Wait**. | Chaîne | OUI
waitTimeInSeconds | Nombre de secondes pendant lesquelles le pipeline attend avant de poursuivre le traitement. | Integer | OUI

## <a name="example"></a>Exemples

> [!NOTE]
> Cette section fournit des définitions JSON et des exemples de commandes PowerShell pour exécuter le pipeline. Pour une procédure pas à pas avec des instructions détaillées permettant de créer un pipeline Data Factory en utilisant des définitions JSON et Azure PowerShell, consultez [Didacticiel : créer une fabrique de données avec Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline avec l’activité Wait
Dans cet exemple, le pipeline a deux activités : **Jusqu’à** et **Attendre**. L’activité Wait est configurée pour attendre pendant une seconde. Le pipeline exécute l’activité Web dans une boucle avec une attente d’une seconde entre chaque exécution. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
