---
title: Guide pratique pour parcourir les résultats de la recherche - API Recherche Bing
titleSuffix: Azure Cognitive Services
description: Découvrez comment parcourir les résultats de la recherche à partir des API Recherche Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: aahi
ms.openlocfilehash: 9fc05ab42c75bac1f8e192dd4fe20bb142881479
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176906"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Guide pratique pour parcourir les résultats à partir des API Recherche Bing

Quand vous envoyez un appel aux API Recherche web, personnalisée, d’images, d’actualités ou de vidéos Bing, Bing retourne une partie du nombre total de résultats susceptibles d’être pertinents pour la requête. Pour obtenir une estimation du nombre total de résultats disponibles, accédez au champ `totalEstimatedMatches` de l’objet de la réponse. 

Par exemple : 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Navigation dans les résultats de la recherche

Pour parcourir les résultats disponibles, utilisez les paramètres de requête `count` et `offset` au moment d’envoyer votre requête.  

> [!NOTE]
>
> * La navigation avec les API Vidéo, Image et Actualités Bing s’applique uniquement aux recherches globales de vidéos (`/video/search`), d’actualités (`/news/search`) et d’images (`/image/search`). La navigation dans des catégories et des rubriques de tendances n’est pas prise en charge.  
> * Le champ `TotalEstimatedMatches` est une estimation du nombre total de résultats de recherche pour la requête actuelle. Lorsque vous définissez les paramètres `count` et `offset`, cette estimation peut changer.

| Paramètre | Description                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Spécifie le nombre de résultats à renvoyer dans la réponse. Notez que la valeur par défaut de `count` et le nombre maximal de résultats que vous pouvez demander varient en fonction de l’API. Vous pouvez trouver ces valeurs dans la documentation de référence sous [Étapes suivantes](#next-steps). |
| `offset`  | Spécifie le nombre de résultats à ignorer. Le paramètre `offset` est basé sur zéro et doit être inférieur à (`totalEstimatedMatches` - `count`).                                           |

Par exemple, si vous souhaitez afficher 15 résultats par page, vous devez définir `count` sur 15 et `offset` sur 0 pour obtenir la première page de résultats. Pour chaque appel d’API suivant, vous devez incrémenter le paramètre `offset` de 15. L’exemple suivant illustre une requête de 15 pages Web avec un paramètre offset de défini sur 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Si vous utilisez la valeur `count` par défaut, vous devez uniquement spécifier le paramètre de requête `offset` dans vos appels d’API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> L’API Recherche web Bing retourne des résultats de recherche qui peuvent inclure des pages web, des images, des vidéos et des actualités. Quand vous parcourez les résultats de la recherche à partir de l’API Recherche web Bing, vous parcourez uniquement [WebPages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) et non d’autres types de réponses tels que des images ou des actualités. Les résultats de la recherche dans les objets `WebPage` peuvent inclure des résultats qui s’affichent également dans d’autres types de réponses.
>
> Si vous utilisez le paramètre de requête `responseFilter` sans spécifier de valeurs de filtre, n’utilisez pas les paramètres `count` et `offset`. 

## <a name="next-steps"></a>Étapes suivantes

* [Présentation des API Recherche web Bing](bing-api-comparison.md)
* [Référence pour l’API Recherche Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Informations de référence sur l’API Recherche personnalisée Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Informations de référence sur l’API Recherche d’actualités Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Informations de référence sur l’API Recherche de vidéos Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Informations de référence sur l’API Recherche d’images Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)