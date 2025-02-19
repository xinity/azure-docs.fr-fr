---
title: Monter en puissance les fonctionnalités et les capacités - Azure App Service | Microsoft Docs
description: Découvrez comment activer la montée en puissance d’une application dans Azure App Service pour ajouter des capacités et des fonctionnalités.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d2c3b09277963781b90f65705e03f936f81b14ee
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232364"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Montez une application en puissance dans Azure App Service

Cet article décrit la mise à l’échelle d’une application web dans Azure App Service. Il existe deux workflows de mise à l’échelle : montée en puissance et augmentation de la taille des instances. Cet article décrit le workflow de montée en puissance.

* [Monter en puissance](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) : bénéficiez d’un surcroît de capacité d’UC, de mémoire et d’espace disque, ainsi que de fonctionnalités supplémentaires, comme des machines virtuelles dédiées, des domaines et des certificats personnalisés, des emplacements intermédiaires, la mise à l’échelle automatique, et bien davantage. Pour monter en puissance en modifiant le niveau tarifaire du plan App Service auquel appartient votre application.
* [Augmentation de la taille des instances](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) : augmentez le nombre d’instances de machine virtuelle qui exécutent votre application.
  Ce nombre peut atteindre 20 instances, en fonction de votre niveau tarifaire. La solution [Environnements App Service](environment/intro.md) au niveau **Isolé** augmente votre capacité de montée en puissance parallèle à 100 instances. Pour plus d’informations sur l’augmentation de la taille des instances, voir [Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md). Vous y trouverez comment utiliser la mise à l’échelle automatique, qui permet de mettre à l’échelle le nombre d’instances automatiquement en fonction des planifications et des règles prédéfinies.

Ces paramètres de mise à l’échelle sont applicables en quelques secondes et affectent toutes les applications de votre [plan App Service](../app-service/overview-hosting-plans.md).
Ils ne nécessitent pas de modifier votre code ou de redéployer votre application.

Pour plus d’informations sur la tarification et les fonctionnalités de chaque plan App Service, voir [Détails de la tarification - App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Avant de changer le niveau **Gratuit** d’un plan App Service, commencez par supprimer les [limites de dépense](https://azure.microsoft.com/pricing/spending-limits/) en place pour votre abonnement Azure. Pour afficher ou modifier les options de votre abonnement Microsoft Azure App Service, consultez la page [Abonnements Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Montée en puissance de votre niveau de tarification

> [!NOTE]
> Pour savoir comment monter en puissance jusqu’au niveau **PremiumV2**, voir [Configurer le niveau PremiumV2 pour App Service](app-service-configure-premium-tier.md).
>

1. Dans votre navigateur, ouvrez le [portail Azure][portal].

1. Dans le volet de navigation de gauche de la page de votre application App Service, sélectionnez **Monter en puissance (plan App Service)** .
   
3. Choisissez votre niveau, puis sélectionnez **Appliquer**. Sélectionnez les différentes catégories (par exemple, **Production**) et **Afficher les options supplémentaires** pour afficher plus de niveaux.
   
    ![Accédez à la montée en puissance pour votre application Azure.][ChooseWHP]

    Une fois l’opération terminée avec succès, une fenêtre contextuelle de notification s’affiche avec une coche verte.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Mettre à l’échelle des ressources associées
Si votre application dépend d’autres services, comme Azure SQL Database ou Stockage Azure, vous pouvez effectuer un scale-up de ces ressources séparément. Ces ressources ne sont pas gérées par le plan App Service.

1. Dans la page **Vue d’ensemble** de votre application, sélectionnez le lien **Groupe de ressources**.
   
    ![Montée en puissance des ressources connexes de votre application Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Dans la partie **Résumé** de la page **Groupe de ressources**, cliquez sur une ressource que vous souhaitez mettre à l’échelle. La capture d’écran ci-après illustre une ressource de SQL Database.
   
    ![Accéder à la page du groupe de ressources pour activer la montée en puissance de votre application Azure](./media/web-sites-scale/ResourceGroup.png)

    Pour monter en puissance la ressource associée, consultez la documentation relative à ce type de ressource précise. Par exemple, pour monter en puissance un SQL Database unique, consultez la page [Mettre à l'échelle des ressources de base de données uniques dans Azure SQL Database](../sql-database/sql-database-single-database-scale.md). Pour monter en puissance une ressource Azure Database pour MySQL, consultez la page [Mettre à l'échelle mes ressources MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparer les niveaux tarifaires

Pour plus d’informations, par exemple sur les tailles de machine virtuelle pour chaque niveau tarifaire, voir [Tarification App Service](https://azure.microsoft.com/pricing/details/app-service).

Pour un tableau des limites, quotas et contraintes du service, ainsi que des fonctionnalités prises en charge dans chaque niveau, voir [Limites d’App Service](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Autres ressources

[Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Configurer le niveau PremiumV2 pour App Service](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
