---
title: Interroger des données avec l’API Azure Cosmos DB pour MongoDB
description: Découvrez comment interroger des données avec l’API Azure Cosmos DB pour MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 40385524e85f950fb32b69817fec27d842370736
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754756"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Interroger des données à l’aide de l’API Azure Cosmos DB pour MongoDB

[L’API Azure Cosmos DB pour MongoDB](mongodb-introduction.md) prend en charge les [requêtes MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Interrogation de données stockées dans votre base de données Cosmos à l’aide de l’interpréteur de commandes MongoDB

Vous pouvez commencer en utilisant les exemples dans ce document et regarder la vidéo [Interroger Azure Cosmos DB avec l’interpréteur de commandes MongoDB](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/).

## <a name="sample-document"></a>Exemple de document

L’exemple de document suivant est utilisé pour les requêtes de cet article.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a> Exemple de requête 1 

Étant donné l’exemple de document de famille ci-dessus, la requête suivante renvoie les documents où le champ id correspond à `WakefieldFamily`.

**Requête**
    
    db.families.find({ id: "WakefieldFamily"})

**Résultats**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>Exemple de requête 2 

La requête suivante renvoie tous les enfants de la famille. 

**Requête**
    
    db.families.find( { id: "WakefieldFamily" }, { children: true } )

**Résultats**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>Exemple de requête 3 

La requête suivante renvoie toutes les familles qui sont enregistrées. 

**Requête**
    
    db.families.find( { "isRegistered" : true })
**Résultats** Aucun document n’est renvoyé. 

## <a id="examplequery4"></a>Exemple de requête 4

La requête suivante renvoie toutes les familles qui ne sont pas enregistrées. 

**Requête**
    
    db.families.find( { "isRegistered" : false })
**Résultats**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>Exemple de requête 5

La requête suivante renvoie toutes les familles qui ne sont pas enregistrées dans l’état de New-York (NY). 

**Requête**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Résultats**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>Exemple de requête 6

La requête suivante renvoie toutes les familles dans lesquelles les enfants sont en 8e année.

**Requête**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Résultats**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>Exemple de requête 7

La requête suivante renvoie toutes les familles dont le nombre d’enfants est 3.

**Requête**
  
      db.Family.find( {children: { $size:3} } )

**Résultats**

Aucun résultat n’est renvoyé car il n’y a aucune famille avec plus de deux enfants. Cette requête ne permet d’obtenir un résultat que lorsque le paramètre est 2, auquel cas l’ensemble du document est renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Appris à interroger des données à l’aide de l’API Cosmos DB pour MongoDB

Vous pouvez maintenant poursuivre avec le didacticiel suivant montrant comment distribuer vos données globalement.

> [!div class="nextstepaction"]
> [Distribuer vos données globalement](tutorial-global-distribution-sql-api.md)

