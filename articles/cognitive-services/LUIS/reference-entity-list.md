---
title: Type d’entité de liste - LUIS
titleSuffix: Azure Cognitive Services
description: Les entités de liste représentent un ensemble fixe, fermé de mots associés, ainsi que leurs synonymes. LUIS ne détecte pas les valeurs supplémentaires pour les entités de liste. Utilisez la fonctionnalité Recommander pour trouver des suggestions de nouveaux mots à partir de la liste actuelle.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1757faf8ab2be0b62956b6939ee068929f9275a4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695250"
---
# <a name="list-entity"></a>Entité de liste 

Les entités de liste représentent un ensemble fixe, fermé de mots associés, ainsi que leurs synonymes. LUIS ne détecte pas les valeurs supplémentaires pour les entités de liste. Utilisez la fonctionnalité **Recommander** pour trouver des suggestions de nouveaux mots à partir de la liste actuelle. S’il existe plusieurs entités de liste avec la même valeur, chaque entité est retournée dans la requête du point de terminaison. 

Une entité de liste n’est pas issue de l’apprentissage automatique. Il s’agit d’une correspondance de texte à l’exact. LUIS marque comme entité chaque correspondance avec un élément d’une liste dans la réponse. 

**L’entité convient bien lorsque les données de texte :**

* Sont un ensemble connu.
* Ne changent pas souvent. Si vous avez besoin de modifier la liste souvent ou si vous souhaitez que la liste se développe automatiquement, une entité simple améliorée avec une liste d’expressions est un meilleur choix. 
* L’ensemble ne dépasse pas les [limites](luis-boundaries.md) maximum de LUIS pour ce type d’entité.
* Le texte de l’énoncé est une correspondance exacte avec un synonyme ou le nom canonique. LUIS n’utilise pas la liste au-delà des correspondances de texte exactes. Une simple entité de liste ne suffit pas pour résoudre la correspondance approximative, la recherche de radicaux, de pluriels et d’autres variantes. Pour gérer les variantes, envisagez d’utiliser un [modèle](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) avec la syntaxe de texte facultative.

![entité de liste](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Exemple JSON

Supposons que l’application comporte une liste nommée `Cities`, permettant des variations de noms de ville : ville aéroportuaire (Sea-tac), indicatif d’aéroport (SEA), code postal (98101) et indicatif téléphonique régional (206).

|Élément de liste|Synonymes de l’élément|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Dans l’énoncé précédent, le mot `paris` est mappé à l’élément paris au sein de l’entité de liste `Cities`. Celle-ci est mise en correspondance avec le nom normalisé de l’élément ainsi que ses synonymes.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Réponse de point de terminaison de prédiction V2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Réponse de point de terminaison de prédiction V3](#tab/V3)


Il s’agit du JSON si `verbose=false` est défini dans la chaîne de requête :

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Il s’agit du JSON si `verbose=true` est défini dans la chaîne de requête :

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Objet de données|Nom de l’entité|Valeur|
|--|--|--|
|Entité de liste|`Cities`|`paris`|


## <a name="next-steps"></a>Étapes suivantes

Dans ce [didacticiel](luis-quickstart-intent-and-list-entity.md), vous allez apprendre à utiliser une **entité de liste** pour extraire des correspondances exactes de texte à partir d’une liste d’éléments connus. 
