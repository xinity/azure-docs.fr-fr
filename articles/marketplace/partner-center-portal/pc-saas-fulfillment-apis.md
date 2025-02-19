---
title: API de traitement SaaS | Place de marché Azure
description: Présente les versions des API de traitement qui vous permettent d’intégrer vos offres SaaS avec la Place de marché Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 7896ed77d9dbb3358ddb1c809ca342828280f66a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258923"
---
# <a name="saas-fulfillment-apis"></a>API de traitement SaaS

Les API de traitement SaaS permettent aux éditeurs de logiciels indépendants (ISV) d’intégrer leurs applications SaaS avec la Place de marché Azure. Ces API permettent aux applications d’éditeurs de logiciels indépendants d’utiliser tous les canaux commerciaux : directs, gérés par des partenaires (revendeurs) et gérés par des acteurs de terrain.  Elles sont obligatoires pour répertorier les offres SaaS négociables sur la Place de marché Azure.

> [!WARNING]
> La version actuelle de cette API (version 2) doit être utilisée pour toutes les nouvelles offres SaaS.  La version 1 de l’API est déconseillée. Elle est conservée pour prendre en charge des offres existantes.


## <a name="business-model-support"></a>Prise en charge du modèle commercial

Cette API prend en charge les fonctionnalités suivantes du modèle commercial :

* Spécifier plusieurs plans pour une offre. Ces plans incluant des fonctionnalités distinctes peuvent être facturés différemment.
* Proposer une offre basée sur un modèle de facturation par site ou par utilisateur.
* Proposer des options de facturation mensuelle et annuelle (paiement anticipé).
* Proposer un tarif privé à un client sur la base d’un contrat entreprise négocié.


## <a name="next-steps"></a>Étapes suivantes

Si ce n’est déjà fait, inscrivez votre application SaaS via le [portail Azure](https://ms.portal.azure.com), comme expliqué dans [Inscrire une application Azure AD](./pc-saas-registration.md).  Ensuite, utilisez la version la plus récente de cette interface pour le développement : [API de traitement SaaS version 2](./pc-saas-fulfillment-api-v2.md).
