---
title: 'Démarrage rapide : Rechercher des images - API REST Recherche d’images Bing et Node.js'
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour envoyer des requêtes de recherche d’images à l’API REST Recherche d’images Bing à l’aide de JavaScript et recevoir des réponses JSON.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 737927aa725c117158ea867e007ecc0cedde50aa
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034645"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Démarrage rapide : Rechercher des images à l’aide de l’API REST Recherche d’images Bing et de Node.js

Utilisez ce guide de démarrage rapide pour commencer à envoyer des demandes de recherche à l’API Recherche d’images Bing. Cette application JavaScript envoie une requête de recherche à l’API et affiche l’URL de la première image dans les résultats. Bien que cette application soit écrite en JavaScript, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) avec une gestion des erreurs supplémentaire et des annotations.

## <a name="prerequisites"></a>Prérequis

* La dernière version de [Node.js](https://nodejs.org/en/download/).

* La [bibliothèque de requêtes JavaScript](https://github.com/request/request).  
  [!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis définissez la sévérité et les exigences HTTPS.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Créez des variables pour le point de terminaison d’API, le chemin de recherche d’API d’image, votre clé d’abonnement et le terme de recherche.
    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Construisez la requête de recherche et la demande.

1. Utilisez les variables de la dernière étape pour mettre en forme une URL de recherche pour la requête d’API. Notez que le terme de recherche doit être encodé sous forme d’URL avant d’être envoyé à l’API.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Utilisez la bibliothèque de requêtes pour envoyer votre demande à l’API. `response_handler` sera défini dans la section suivante.
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Gérer et analyser la réponse

1. Définissez une fonction nommée `response_handler` qui prend un appel HTTP, `response`, comme paramètre. Au sein de cette fonction, effectuez les étapes suivantes :

    1. Définissez une variable pour contenir le corps de la réponse JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Stockez le corps de la réponse quand l’indicateur **data** est appelé.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. Quand un indicateur **end** est signalé, obtenez le premier résultat de la réponse JSON. Imprimez l’URL de la première image, ainsi que le nombre total d’images retournées.

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="example-json-response"></a>Exemple de réponse JSON

Les réponses de l’API Recherche d’images Bing sont retournées au format JSON. Cet exemple de réponse a été tronqué pour afficher un résultat unique.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que la Recherche d’images Bing ?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Essayez une démonstration interactive en ligne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [Détail des prix](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) des API Recherche Bing. 
* [Obtenir une clé d’accès Cognitive Services gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentation Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
