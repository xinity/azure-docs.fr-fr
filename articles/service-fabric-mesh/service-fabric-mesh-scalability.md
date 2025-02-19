---
title: Scalabilité des applications Azure Service Fabric Mesh | Microsoft Docs
description: Découvrez les services de mise à l’échelle dans Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811091"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Mise à l’échelle des applications Service Fabric Mesh

Le déploiement d’applications vers Service Fabric Mesh permet notamment d’augmenter ou de diminuer la taille de vos services en toute simplicité. Vous pouvez ainsi gérer des charges variables sur vos services ou améliorer la disponibilité. Vous pouvez manuellement mettre à l’échelle vos services ou configurer des stratégies de mise à l’échelle automatique.

## <a name="manual-scaling-instances"></a>Mise à l’échelle manuelle des instances

Dans le modèle de déploiement de la ressource d’application, chaque service a une propriété *replicaCount* qui peut être utilisée pour définir le nombre de déploiements de ce service. Une application peut se composer de plusieurs services, chaque service disposant d’un nombre *replicaCount* unique, qui sont déployés et gérés ensemble. Pour mettre à l’échelle le nombre de réplicas de service, modifiez la valeur *replicaCount* pour chaque service que vous souhaitez mettre à l’échelle dans le modèle de déploiement ou les fichiers de paramètres. Mettez ensuite à niveau l’application.

Pour des exemples de mise l’échelle manuelle d’instances de service, consultez [Augmenter ou diminuer manuellement la taille de vos services](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Mise à l’échelle automatique des instances de service
La mise à l’échelle automatique est une fonctionnalité supplémentaire de Service Fabric qui permet de mettre à l’échelle dynamiquement le nombre de vos instances de service (mise à l’échelle horizontale). La mise à l’échelle automatique offre une grande souplesse et permet de provisionner ou de supprimer des instances de service en se basant sur l’utilisation du processeur ou de la mémoire.  La mise à l’échelle automatique vous permet d’exécuter le bon nombre d’instances de service pour votre charge de travail et d’optimiser les coûts.

Une stratégie de mise à l’échelle automatique est définie par service dans le fichier de ressources de service. Chaque stratégie de mise à l’échelle se compose de deux parties :

- Un déclencheur de mise à l’échelle, qui décrit quand la mise à l’échelle du service est effectuée. Trois facteurs déterminent quand le service est mis à l’échelle. Le *seuil inférieur de charge* est une valeur qui détermine quand le service fait l’objet d’un scale-in. Si la charge moyenne de toutes les instances des partitions est inférieure à cette valeur, la taille du service est diminuée. Le *seuil supérieur de charge* est une valeur qui détermine quand le service fait l’objet d’un scale-out. Si la charge moyenne de toutes les instances de la partition est supérieure à cette valeur, la taille du service est augmentée. L’*intervalle de mise à l’échelle* détermine la fréquence (en secondes) à laquelle le déclencheur est vérifié. Une fois que le déclencheur est vérifié, le mécanisme est appliqué si une mise à l’échelle est nécessaire. Si la mise à l’échelle n’est pas nécessaire, aucune action n’est entreprise. Dans les deux cas, aucune nouvelle vérification du déclencheur n’est effectuée avant l’expiration de l’intervalle de mise à l’échelle.

- Un mécanisme de mise à l’échelle, qui décrit comment la mise à l’échelle est effectuée quand elle est déclenchée. *Incrément d’échelle* détermine le nombre d’instances ajoutées ou supprimées quand le mécanisme est déclenché. *Nombre maximum d’instances* définit la limite supérieure de la mise à l’échelle. Si le nombre d’instances atteint cette limite, aucun scale-out n’est effectué sur le service, quelle que soit la charge. *Nombre minimum d’instances* définit la limite inférieure de la mise à l’échelle. Si le nombre d’instances de la partition atteint cette limite, la taille du service n’est pas diminuée, quelle que soit la charge.

Pour savoir comment définir une stratégie de mise à l’échelle automatique pour votre service, consultez [Mettre automatiquement à l’échelle les services](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le modèle d’application, voir [Ressources Service Fabric](service-fabric-mesh-service-fabric-resources.md)
