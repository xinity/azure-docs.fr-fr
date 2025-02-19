---
title: 'Démarrage rapide : Créer un service Recherche cognitive Azure dans le portail'
titleSuffix: Azure Cognitive Search
description: Provisionnez une ressource Recherche cognitive Azure dans le portail Azure. Choisissez les groupes de ressources, régions et références SKU ou niveaux tarifaires.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 21f55805e0486d987922a1aa160f2938f3a50155
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792437"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Démarrage rapide : Créer un service Recherche cognitive Azure dans le portail

La Recherche cognitive Azure est une ressource autonome utilisée pour ajouter une expérience utilisateur de recherche à des applications personnalisées. Bien que la Recherche cognitive Azure s’intègre facilement à d’autres services Azure, vous pouvez également l’utiliser en tant que composant autonome. De même, vous pouvez l’intégrer à des applications situées sur des serveurs réseau, ou à des logiciels s’exécutant sur d’autres plateformes cloud.

Dans cet article, découvrez comment créer une ressource Recherche cognitive Azure dans le [portail Azure](https://portal.azure.com/).

[![GIF animé](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Vous préférez PowerShell ? Utilisez le [modèle de service](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manager. Pour obtenir de l’aide et bien démarrer, consultez [Gérer la Recherche cognitive Azure avec PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>S’abonner (payant ou gratuit)

[Ouvrez un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) et utilisez les crédits gratuits pour essayer les services payants d’Azure. Une fois les crédits épuisés, conservez le compte et continuez à utiliser les services Azure gratuits, tels que les sites Web. Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres pour demander à l’être.

Vous pouvez également [activer les avantages d’abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Un abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants. 

## <a name="find-azure-cognitive-search"></a>Localiser la Recherche cognitive Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Cliquez sur le signe plus (« + Créer une ressource ») en haut à gauche.
3. Utilisez la barre de recherche pour trouver la « Recherche cognitive Azure », ou accédez à la ressource via **Web** > **Recherche cognitive Azure**.

![Accéder à une ressource Recherche cognitive Azure](./media/search-create-service-portal/find-search3.png "Chemin de navigation vers la Recherche cognitive Azure")

## <a name="select-a-subscription"></a>Sélectionner un abonnement

Si vous avez plusieurs abonnements, choisissez celui qui a également des services de stockage de données ou de fichiers. La Recherche cognitive Azure peut détecter automatiquement Table Azure, Stockage Blob, SQL Database et Azure Cosmos DB afin d’effectuer l’indexation via des [*indexeurs*](search-indexer-overview.md), mais uniquement pour les services du même abonnement.

## <a name="set-a-resource-group"></a>Définir un groupe de ressources

Un groupe de ressources est nécessaire et utile pour gérer les ressources, y compris la gestion des coûts. Un groupe de ressources peut se composer d’un service ou de plusieurs services utilisés ensemble. Par exemple, si vous utilisez la Recherche cognitive Azure pour indexer une base de données Azure Cosmos DB, vous pouvez associer les deux services au sein du même groupe de ressources à des fins de gestion. 

Si vous ne combinez pas des ressources dans un même groupe, ou si les groupes de ressources existants sont remplis de ressources utilisées dans des solutions non liées, créez un groupe de ressources uniquement pour votre ressource Recherche cognitive Azure. 

Lorsque vous utilisez le service, vous pouvez effectuer le suivi des coûts actuels et prévus (comme indiqué dans la capture d’écran) ou faire défiler vers le haut pour afficher les frais des différentes ressources.

![Gérer les coûts au niveau du groupe de ressources](./media/search-create-service-portal/resource-group-cost-management.png "Gérer les coûts au niveau du groupe de ressources")

> [!TIP]
> La suppression d’un groupe de ressources supprime également les services qu’il contient. Pour les projets de prototype utilisant plusieurs services, le fait de les placer tous dans le même groupe de ressources facilite le nettoyage une fois le projet terminé.

## <a name="name-the-service"></a>Nommer le service

Dans Détails de l’instance, indiquez un nom de service dans le champ **URL**. Le nom fait partie du point de terminaison URL par le biais duquel les appels d’API sont émis : `https://your-service-name.search.windows.net`. Par exemple, si vous souhaitez que le point de terminaison soit `https://myservice.search.windows.net`, vous devez entrer `myservice`.

Configuration requise du nom du service :

* il doit être unique dans l’espace de noms search.windows.net ;
* entre 2 et 60 caractères ;
* contenir des lettres minuscules, des chiffres ou des tirets (« - ») ;
* éviter les tirets (« - ») pour les 2 premiers caractères ou le dernier ;
* pas de tirets consécutifs (« -- »).

> [!TIP]
> Si vous pensez utiliser plusieurs services, nous vous recommandons d’inclure la région (ou l’emplacement) dans le nom du service comme convention d’affectation de noms. Les services d’une même région peuvent échanger des données gratuitement. Ainsi, si la Recherche cognitive Azure est située dans la région USA Ouest et si vous disposez d’autres services également dans cette région, un nom tel que `mysearchservice-westus` peut vous éviter de passer par la page de propriétés quand vous décidez de la façon d’associer ou d’attacher des ressources.

## <a name="choose-a-location"></a>Choisir un emplacement

En tant que service Azure, la Recherche cognitive Azure peut être hébergée dans les centres de données du monde entier. Vous trouverez la liste des régions prises en charge dans la [page de tarification](https://azure.microsoft.com/pricing/details/search/). 

Vous pouvez réduire ou éviter les frais de bande passante en choisissant le même emplacement pour plusieurs services. Par exemple, si vous indexez des données fournies par un autre service Azure (Stockage Azure, Azure Cosmos DB, Azure SQL Database), la création de votre service Recherche cognitive Azure dans la même région évite les frais de bande passante (il n’existe aucun frais pour les données sortantes quand les services se trouvent dans la même région).

De plus, si vous utilisez des enrichissements IA pour la recherche cognitive, créez votre service dans la même région que votre ressource Cognitive Services. *La colocalisation de la Recherche cognitive Azure et de Cognitive Services dans la même région est obligatoire pour l’enrichissement par IA*.

> [!Note]
> Inde Centre est actuellement indisponible pour les nouveaux services. Pour les services déjà dans la région Inde Centre, vous pouvez effectuer un scale-up sans aucune restriction, et votre service est entièrement pris en charge dans cette région. La restriction appliquée à cette région est temporaire et ne concerne que les nouveaux services. Nous supprimerons cette note lorsque la restriction ne s’appliquera plus.

## <a name="choose-a-pricing-tier-sku"></a>Sélectionner un niveau tarifaire (SKU)

[La Recherche cognitive Azure est proposée à plusieurs niveaux tarifaires](https://azure.microsoft.com/pricing/details/search/) : Gratuit, De base ou Standard. Chaque niveau a ses propres [capacité et limites](search-limits-quotas-capacity.md). Pour obtenir de l’aide, voir [Choisir un niveau tarifaire ou une référence (SKU)](search-sku-tier.md) .

De base et Standard sont les options les plus courantes pour les charges de production, mais la plupart des clients démarrent avec le service gratuit. Les principales différences entre les niveaux sont la taille et la vitesse des partitions, ainsi que les limites du nombre d’objets que vous pouvez créer.

Nous vous rappelons que vous ne pouvez pas changer de niveau tarifaire une fois le service créé. Si vous souhaitez un niveau plus élevé ou moins élevé, vous devez recréer le service.

## <a name="create-your-service"></a>Créer votre service

Une fois que vous avez fourni les entrées nécessaires, continuez et créez le service. 

![Passer en revue et créer le service](./media/search-create-service-portal/new-service3.png "Passer en revue et créer le service")

Votre service est déployé en quelques minutes. Vous pouvez superviser la progression du déploiement en utilisant les notifications Azure. Vous pouvez épingler le service à votre tableau de bord afin d’y accéder plus facilement la prochaine fois.

![Superviser et épingler le service](./media/search-create-service-portal/monitor-notifications.png "Superviser et épingler le service")

## <a name="get-a-key-and-url-endpoint"></a>Obtenir une clé et un point de terminaison d’URL

Si vous n’utilisez pas le portail, l’accès programmatique à votre nouveau service nécessite de spécifier le point de terminaison d’URL et une clé d’API d’authentification.

1. Dans la page Vue d’ensemble du service, recherchez et copiez le point de terminaison d’URL sur le côté droit de la page.

2. Dans le volet de navigation gauche, sélectionnez **Clés**, puis copiez une des clés d’administrateur (elles sont équivalentes). Les clés d’API d’administrateur sont nécessaires pour la création, la mise à jour et la suppression d’objets sur votre service.

   ![Page Vue d’ensemble du service avec le point de terminaison d’URL](./media/search-create-service-portal/get-url-key.png "Point de terminaison d’URL et autres détails du service")

Un point de terminaison et une clé ne sont pas nécessaires pour les tâches effectuées via le portail. Le portail est déjà lié à votre ressource Recherche cognitive Azure avec des droits d’administrateur. Pour obtenir une procédure pas à pas à effectuer dans le portail, commencez par [Guide de démarrage rapide : Créez un index de Recherche cognitive Azure dans le portail](search-get-started-portal.md).

## <a name="scale-your-service"></a>Mettre à l’échelle le service

Une fois votre service approvisionné, vous pouvez le mettre à l’échelle en fonction de vos besoins. Si vous avez choisi le niveau Standard pour votre service Recherche cognitive Azure, vous pouvez le mettre à l’échelle dans deux dimensions : réplicas et partitions. Si vous choisissez le niveau De base, vous pouvez uniquement ajouter des réplicas. Si vous configurez le service gratuit, la mise à l’échelle n’est pas disponible.

Les ***partitions*** permettent à votre service de stocker plus de documents et d’effectuer des recherches dans un plus grand nombre de documents.

Les ***réplicas*** permettent à votre service de gérer une charge supérieure de requêtes de recherche.

L’ajout de ressources augmente votre facture mensuelle. Le [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) peut vous aider à comprendre les conséquences de l’ajout de ressources pour la facturation. N’oubliez pas que vous pouvez ajuster les ressources en fonction de la charge. Par exemple, vous pouvez augmenter les ressources pour créer un index initial complet, puis les réduire ultérieurement à un niveau plus approprié pour l’indexation incrémentielle.

> [!Important]
> Un service doit avoir [2 réplicas pour SLA en lecture seule et 3 réplicas pour SLA en lecture/écriture](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Accédez à la page du service de recherche dans le portail Azure.
2. Dans le volet de navigation de gauche, sélectionnez **Paramètres** > **Mise à l’échelle**.
3. Utilisez le curseur pour ajouter des ressources de chaque type.

![Ajouter de la capacité](./media/search-create-service-portal/settings-scale.png "Ajouter de la capacité via des réplicas et des partitions")

> [!Note]
> Le stockage par partition et la vitesse sont plus élevés dans les niveaux de service supérieurs. Pour plus d’informations, consultez [Capacité et limitations](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Quand ajouter un deuxième service

La plupart des clients n’utilisent qu’un seul service provisionné à un niveau qui fournit le [bon équilibre des ressources](search-sku-tier.md). Un service peut héberger plusieurs index, soumis aux [limites maximales du niveau sélectionné](search-capacity-planning.md), chaque index étant isolé des autres. Dans la Recherche cognitive Azure, les requêtes ne peuvent être dirigées que vers un seul index, ce qui réduit les risques d’extraction accidentelle ou intentionnelle de données à partir d’autres index du même service.

Bien que la plupart des clients utilisent un seul service, une redondance des services peut être nécessaire en cas d’exigences opérationnelles particulières, notamment :

* Récupération d’urgence (panne du centre de données). La Recherche cognitive Azure ne fournit pas de basculement instantané en cas de panne. Pour obtenir de l’aide et des recommandations, consultez la page [Administration des services](search-manage.md).
* Vos recherches sur la modélisation d’une architecture mutualisée ont déterminé que des services supplémentaires représentent la conception optimale. Pour plus d’informations, consultez la page [Conception pour une architecture mutualisée](search-modeling-multitenant-saas-applications.md).
* Pour les applications déployées mondialement, vous pouvez avoir besoin d’une instance de la Recherche cognitive Azure dans plusieurs régions afin de réduire la latence du trafic international de votre application.

> [!NOTE]
> Dans la Recherche cognitive Azure, vous ne pouvez pas séparer les opérations d’indexation et d’interrogation. Vous ne créez donc jamais plusieurs services pour des charges de travail distinctes. Un index est toujours interrogé sur le service dans lequel il a été créé (vous ne pouvez pas créer un index dans un service et le copier dans un autre).

Il n’est pas nécessaire de disposer d’un second service pour la haute disponibilité. La haute disponibilité des requêtes est atteinte si vous utilisez au moins deux réplicas dans le même service. Les mises à jour des réplicas sont séquentielles, ce qui signifie qu’au moins l’un d’eux est opérationnel lors du déploiement d’une mise à jour de service. Pour plus d’informations sur la disponibilité, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Étapes suivantes

Après avoir provisionné un service Recherche cognitive Azure, vous pouvez rester sur le portail pour créer votre premier index.

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un index de Recherche cognitive Azure dans le portail](search-get-started-portal.md)
