---
title: Stratégies de clé de contenu dans Media Services - Azure | Microsoft Docs
description: Cet article explique ce que sont les stratégies de clé de contenu et comment Azure Media Services les utilise.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8809bf25c3bcfb26fb0ad251a2b09dfdca2a3e04
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679191"
---
# <a name="content-key-policies"></a>Stratégies de clé de contenu

Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés. 

Pour spécifier des options de chiffrement sur votre flux, vous devez créer une [stratégie de streaming](streaming-policy-concept.md), puis l’associer à votre [localisateur de streaming](streaming-locators-concept.md). Vous créez la [stratégie de clé de contenu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) pour configurer la façon dont la clé de contenu (qui fournit un accès sécurisé à vos [ressources](assets-concept.md)) est remise aux clients finaux. Vous devez définir les exigences (restrictions) sur la stratégie de clé de contenu qui doivent être respectées afin que des clés ayant la configuration spécifiée soient remises à des clients. La stratégie de clé de contenu n’est pas nécessaire pour un téléchargement ou un streaming en clair. 

En général, vous associez votre stratégie clé de contenu à votre [localisateur de streaming](streaming-locators-concept.md). Vous pouvez également spécifier la stratégie de clé de contenu dans une [stratégie de streaming](streaming-policy-concept.md) (lors de la création d’une stratégie de streaming personnalisée pour les scénarios avancés). 

> [!NOTE]
> Les propriétés des stratégies de clé de contenu de type `Datetime` sont toujours au format UTC.

## <a name="best-practices-and-considerations"></a>Meilleures pratiques et considérations

> [!IMPORTANT]
> Veuillez consulter les recommandations suivantes.

* Vous devez concevoir un ensemble limité de stratégies pour votre compte Media Services et les réutiliser pour vos éléments localisateurs de diffusion en continu chaque fois que les mêmes options sont nécessaires. Pour plus d’informations, consultez [Quotas et limitations](limits-quotas-constraints.md).
* Les stratégies de clé de contenu peuvent être mises à jour. Cela peut prendre jusqu’à 15 minutes pour que les caches de livraison de clés mettent à jour et récupèrent la stratégie mise à jour. 

   En mettant à jour la stratégie, vous remplacez votre cache CDN existant, ce qui peut entraîner un problème de lecture pour les clients qui utilisent du contenu mis en cache.  
* Nous vous recommandons de ne pas créer une nouvelle stratégie de clé de contenu pour chaque ressource. Les principaux avantages du partage de la même stratégie de clé de contenu entre les ressources qui nécessitent les mêmes options de stratégie sont les suivants :
   
   * Il est plus facile de gérer un petit nombre de stratégies.
   * Si vous devez effectuer des mises à jour de la stratégie de clé de contenu, les modifications s’appliquent sur toutes les nouvelles requêtes de licence presque immédiatement.
* Si vous avez besoin de créer une nouvelle stratégie, vous devez créer un nouveau localisateur de streaming pour la ressource.
* Il est recommandé de laisser Media Services générer automatiquement la clé de contenu. 

   Généralement, vous utilisez une clé à longue durée de vie et vous vérifiez l’existence de la stratégie de clé de contenu avec [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Pour vous procurer la clé, vous devez appeler une méthode d'action distincte afin d'obtenir des informations d'identification ou des secrets ; reportez-vous à l'exemple ci-dessous.

## <a name="example"></a>Exemples

Pour accéder à la clé, utilisez `GetPolicyPropertiesWithSecretsAsync`, comme indiqué dans l’exemple [Obtenir une clé de signature à partir de la stratégie existante](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets).

## <a name="filtering-ordering-paging"></a>Filtrage, tri, pagination

Consultez [Filtrage, tri et pagination des entités Media Services](entities-overview.md).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser le chiffrement dynamique AES-128 et le service de distribution des clés](protect-with-aes128.md)
* [Utilisation du chiffrement dynamique DRM et du service de remise des licences](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
