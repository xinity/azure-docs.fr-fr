---
title: Se connecter à Facebook – Azure Logic Apps
description: Gérer votre chronologie et la page avec les API REST de Facebook et Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 83431184d7e9c5970ece6af143ee9b5166da96d5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050951"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Gérer votre page et votre chronologie Facebook à l’aide d'Azure Logic Apps

Connectez-vous à Facebook et publiez dans un journal, obtenez un flux de page et bien plus encore. Avec Facebook, vous pouvez effectuer les opérations suivantes :

* Créer votre flux d’activité en fonction des données que vous obtenez de Facebook. 
* Utiliser un déclencheur quand une publication est reçue.
* Utiliser des actions pour publier dans votre journal, obtenir un flux de page et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand il y a une nouvelle publication dans votre journal, vous pouvez la transférer vers votre flux Twitter. 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Créer une connexion à Facebook

Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Facebook.

1. Connectez-vous à votre compte Facebook.

2. Sélectionnez **Autoriser**et permettez à vos applications logiques de se connecter à votre compte Facebook et de l’utiliser. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme décrit dans le fichier OpenAPI (anciennement Swagger) du connecteur, consultez la [page de référence du connecteur](/connectors/facebook/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)