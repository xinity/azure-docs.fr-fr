---
title: Entité prédéfinie currency - LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des informations sur l’entité prédéfinie currency dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 61be3225f22aca821f8c26522ab37eab0c82bc26
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677717"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entité prédéfinie currency pour une application LUIS
L’entité prédéfinie currency détecte la devise dans de nombreuses dénominations et de nombreux pays/régions, indépendamment de la culture de l’application LUIS. Étant donné que cette entité est déjà formée, vous n’avez pas besoin d’ajouter d’exemples d’énoncés contenant des devises aux intentions de l’application. L’entité currency est prise en charge dans de [nombreuses cultures](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Types d’entités currency
L’entité currency est gérée à partir du dépôt GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Résolution pour l’entité currency

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

L’exemple suivant montre la résolution de l’entité **builtin.currency**.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)

Le code JSON suivant a le paramètre `verbose` défini sur `false` :

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

Le code JSON suivant a le paramètre `verbose` défini sur `true` :

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```


* * * 

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

En savoir plus sur les entités [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md) [email](luis-reference-prebuilt-email.md). 
