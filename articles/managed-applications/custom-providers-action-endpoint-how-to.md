---
title: Ajout d’actions personnalisées à l’API REST Azure
description: Découvrez comment ajouter des actions personnalisées à l’API REST Azure. Cet article vous expliquera les conditions requises et les meilleures pratiques pour les points de terminaison qui souhaitent implémenter des actions personnalisées.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795300"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Ajout d’actions personnalisées à l’API REST Azure

Cet article passera par les conditions requises et les meilleures pratiques pour créer des points de terminaison de fournisseur de ressources personnalisées Azure qui implémentent les actions personnalisées. Si vous n’êtes pas familiarisé avec les fournisseurs de ressources personnalisées Azure, consultez [la vue d’ensemble des fournisseurs de ressources personnalisées](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Comment définir un point de terminaison d’action

Un **point de terminaison** est une URL qui pointe vers un service, qui implémente le contrat sous-jacent entre ce dernier et Azure. Le point de terminaison est défini dans le fournisseur de ressources personnalisées. Il peut s’agir de n’importe quelle URL accessible publiquement. L’exemple ci-dessous a une **action** appelée `myCustomAction` implémentée par `endpointURL`.

Exemple **ResourceProvider** :

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Création d’un point de terminaison d’action

Un **point de terminaison** qui implémente une **action** doit gérer la requête et la réponse pour la nouvelle API dans Azure. Lorsqu’un fournisseur de ressources personnalisées avec une **action** est créé, il génère un nouvel ensemble d’API dans Azure. Dans ce cas, l’action génère une nouvelle API d’action Azure pour les appels `POST` :

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Requête d’API Azure entrante :

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Cette requête est ensuite transférée au **point de terminaison** sous la forme suivante :

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

De même, la réponse du **point de terminaison** est ensuite transmise au client. La réponse du point de terminaison doit renvoyer :

- Un document d’objet JSON valide. Tous les tableaux et chaînes doivent être imbriqués sous un objet de niveau supérieur.
- L’en-tête `Content-Type` doit être défini sur « application/json; charset=utf-8 ».

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Réponse du fournisseur de ressources personnalisées Azure :

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Appel d’une action personnalisée

Il existe deux façons d’appeler une action personnalisée dans un fournisseur de ressources personnalisées :

- D’Azure CLI
- Modèles Azure Resource Manager

### <a name="azure-cli"></a>D’Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Paramètre | Obligatoire | Description
---|---|---
action | *Oui* | Le nom de l’action définie dans le **ResourceProvider**.
ids | *Oui* | L’ID de la ressource du **ResourceProvider**.
request-body | *non* | Le corps de la demande sera envoyé au **point de terminaison**.

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

> [!NOTE]
> La prise en charge des actions dans les modèles Azure Resource Manager est limitée. Afin que l’action soit appelée à l’intérieur d’un modèle, elle doit contenir le préfixe [`list`](../azure-resource-manager/resource-group-template-functions-resource.md#list) dans son nom.

Exemple **ResourceProvider** avec une action de liste :

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Exemple de modèle Azure Resource Manager :

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Paramètre | Obligatoire | Description
---|---|---
resourceIdentifier | *Oui* | L’ID de la ressource du **ResourceProvider**.
apiVersion | *Oui* | La version d’API du runtime des ressources. La version doit toujours être « 2018-09-01-preview ».
functionValues | *non* | Le corps de la demande sera envoyé au **point de terminaison**.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des fournisseurs de ressources personnalisées Azure](./custom-providers-overview.md)
- [Démarrage rapide : Créer un fournisseur de ressources personnalisées Azure et déployer des ressources personnalisées](./create-custom-provider.md)
- [Tutoriel : Créer des actions et des ressources personnalisées dans Azure](./tutorial-custom-providers-101.md)
- [Guide pratique pour ajouter des ressources personnalisées à l’API REST Azure](./custom-providers-resources-endpoint-how-to.md)
