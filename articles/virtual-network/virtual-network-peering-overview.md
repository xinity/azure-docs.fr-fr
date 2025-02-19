---
title: Peering de réseaux virtuels Azure
titlesuffix: Azure Virtual Network
description: En savoir plus sur le peering de réseaux virtuels dans Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: anavin
ms.openlocfilehash: 728d32ddb63658d24e932e8eeef4a3f50371ccc3
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72265052"
---
# <a name="virtual-network-peering"></a>Peering de réseau virtuel

Le Peering d’un réseau virtuel vous permet de connecter des [réseaux virtuels](virtual-networks-overview.md) Azure en toute transparence. Une fois homologués, les réseaux virtuels apparaissent comme un seul réseau à des fins de connectivité. Le trafic entre les machines virtuelles des réseaux virtuels homologués est acheminé via l’infrastructure principale de Microsoft de façon assez semblable au trafic entre des machines virtuelles d’un même réseau virtuel via des adresses IP *privées* seulement. Supports Azure :
* VNET Peering : connexion de réseaux virtuels dans la même région Azure
* Global VNET Peering : connexion de réseaux virtuels entre des régions Azure

Voici quelques-uns des avantages du peering de réseaux virtuels, qu’il soit local ou global :

* Le trafic réseau entre les réseaux virtuels homologués est privé. Le trafic entre les réseaux virtuels reste sur le réseau principal de Microsoft. Aucun chiffrement et aucune connexion Internet publique, ni passerelle ne sont nécessaires pour que les réseau virtuels communiquent.
* Connexion à latence faible et haut débit entre les ressources de différents réseaux virtuels.
* La possibilité pour les ressources d’un réseau virtuel de communiquer avec les ressources d’un autre réseau virtuel, une fois que les réseaux virtuels sont homologués.
* La possibilité de transférer des données dans des abonnements Azure, des modèles de déploiement et dans les régions Azure.
* Possibilité d’homologuer deux réseaux virtuels créés via le modèle de déploiement Azure Resource Manager ou d’homologuer un réseau virtuel créé via Resource Manager pour un réseau virtuel créé via le modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, consultez l’article [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Aucune interruption des ressources dans un réseau virtuel lors de la création du peering ou après que le peering est créé.

## <a name="connectivity"></a>Connectivité

Une fois les réseaux virtuels homologués, les ressources de l’un peuvent se connecter directement à celles du réseau virtuel homologué.

La latence du réseau entre des machines virtuelles de réseaux virtuels homologués dans la même région est la même que celle d’un seul réseau virtuel. Le débit du réseau repose sur la bande passante autorisée pour la machine virtuelle proportionnellement à sa taille. Aucune restriction de bande passante supplémentaire n’est appliquée au sein du peering.

Le trafic entre les machines virtuelles dans des réseaux virtuels homologués est acheminé directement via l’infrastructure principale de Microsoft et non via une passerelle ou une connexion Internet publique.

Des groupes de sécurité réseau peuvent être appliqués dans l’un ou l’autre des réseaux virtuels pour bloquer l’accès à d’autres réseaux virtuels ou à des sous-réseaux si besoin est.
Lors de la configuration du peering de réseaux virtuels, vous pouvez ouvrir ou fermer les règles du groupe de sécurité réseau entre les réseaux virtuels. Si vous ouvrez totalement la connectivité entre les réseaux virtuels homologués (ce qui est l’option par défaut), vous pouvez alors appliquer des groupes de sécurité réseau à des sous-réseaux ou des machines virtuelles spécifiques pour bloquer ou refuser certains accès. Pour en savoir plus sur les groupes de sécurité réseau, consultez [Filtrer le trafic réseau avec les groupes de sécurité réseau](security-overview.md).

## <a name="service-chaining"></a>Chaînage de services

Vous pouvez configurer des itinéraires définis par l’utilisateur qui pointent vers des machines virtuelles de réseaux virtuels homologués en tant qu’adresse IP du *tronçon suivant*, ou vers des passerelles de réseau virtuel, pour activer le chaînage de services. Le chaînage de services vous permet de diriger le trafic d’un réseau virtuel vers une appliance virtuelle, ou une passerelle de réseau virtuel, d’un réseau virtuel homologué via les itinéraires définis.

Vous pouvez également déployer des réseaux de type hub-and-spoke où le nœud du réseau virtuel peut héberger des composants d’infrastructure tels qu’une appliance virtuelle réseau ou une passerelle VPN. Tous les réseaux virtuels spoke peuvent ensuite être homologués avec le réseau virtuel hub. Le trafic peut passer par des appliances virtuelles réseau ou des réseaux VPN sur le réseau virtuel hub. 

Le peering de réseaux virtuels permet de définir le tronçon suivant dans un itinéraire défini par l’utilisateur sur l’adresse IP d’une machine virtuelle du réseau virtuel appairé ou une passerelle VPN. Toutefois, vous ne pouvez pas définir d’itinéraires entre plusieurs réseaux virtuels avec un itinéraire défini par l’utilisateur en spécifiant une passerelle ExpressRoute comme type de tronçon suivant. Pour en savoir plus sur les routages définis par l’utilisateur, voir [Vue d’ensemble des routages définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined). Pour découvrir comment créer une topologie de réseau de type hub et spoke, consultez [Implement a hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json) (Implémenter une topologie de réseau de type hub et spoke dans Azure).

## <a name="gateways-and-on-premises-connectivity"></a>Passerelles et connectivité locale

Chaque réseau virtuel, qu’il soit homologué ou non avec un autre réseau virtuel, peut posséder sa propre passerelle et l’utiliser pour se connecter à un réseau local. Vous pouvez également configurer des [connexions de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à l’aide de passerelles, même si les réseaux virtuels sont homologués.

Lorsque les deux options d’interconnexion de réseaux virtuels sont configurées, le trafic entre les réseaux virtuels transite via la configuration de peering (c’est-à-dire via le réseau principal Azure).

Lorsque des réseaux virtuels sont homologués, vous pouvez également configurer la passerelle du réseau virtuel homologué en tant que point de transit vers un réseau local. Dans ce cas, le réseau virtuel qui utilise une passerelle distante ne peut pas posséder sa propre passerelle. Un réseau virtuel ne peut posséder qu’une seule passerelle. Il peut s’agir d’une passerelle locale ou distante (dans le réseau virtuel homologué), comme l’illustre l’image suivante :

![Transit de peering de réseaux virtuels](./media/virtual-networks-peering-overview/figure04.png)

Le transit de la passerelle est pris en charge pour VNET Peering et pour Global VNet Peering. Le transit de la passerelle entre les réseaux virtuels créés par le biais de modèles de déploiement différents (Resource Manager et classique) est pris en charge uniquement si la passerelle se trouve dans le réseau virtuel (Resource Manager). Pour en savoir plus sur l’utilisation d’une passerelle pour le transit, consultez [Configure a VPN gateway for transit in a virtual network peering (Configurer une passerelle VPN pour le transit dans une homologation de réseaux virtuels)](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Lorsque des réseaux virtuels qui partagent une même connexion Azure ExpressRoute sont appairés, le trafic entre eux transite via la relation de peering (c’est-à-dire via le réseau principal Azure). Vous pouvez toujours utiliser des passerelles locales dans chaque réseau virtuel pour vous connecter au circuit local. Vous pouvez également utiliser une passerelle partagée et configurer le transit pour la connectivité locale.

## <a name="troubleshoot"></a>Résolution des problèmes

Pour confirmer un peering de réseaux virtuels, vous pouvez [vérifier les itinéraires effectifs](diagnose-network-routing-problem.md) pour une interface réseau dans n’importe quel sous-réseau d’un réseau virtuel. Si une homologation de réseaux virtuels existe, tous les sous-réseaux au sein du réseau virtuel ont des itinéraires avec le type de tronçon suivant *VNet Peering* pour chaque espace d’adressage de chaque réseau virtuel homologué.

Vous pouvez aussi résoudre les problèmes de connectivité à une machine virtuelle d’un réseau virtuel homologué à l’aide de la [vérification de la connectivité](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de Network Watcher. La vérification de la connectivité vous permet de voir comment le trafic est acheminé à partir de l’interface réseau d’une machine virtuelle source vers l’interface réseau d’une machine virtuelle de destination.

Vous pouvez également essayer [Utilitaire de dépannage pour les problèmes de peering de réseau virtuel](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues).

## <a name="requirements-and-constraints"></a>Exigences et contraintes

Les contraintes ci-après s’appliquent uniquement quand des réseaux virtuels sont appairés à l’échelle mondiale :
- Les ressources situées dans un réseau virtuel ne peuvent pas communiquer avec l’adresse IP frontale d’un équilibreur de charge interne de base dans un réseau virtuel appairé à l’échelle mondiale. La prise en charge d’un équilibreur de charge de base n’est proposée que dans la même région. La prise en charge d’un Standard Load Balancer existe pour VNet Peering et Global VNet Peering. Les services utilisant un équilibreur de charge de base qui ne fonctionne pas sur un Peering Global VNet Peering sont détaillés [ici.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

Pour en savoir plus sur les exigences et les contraintes, consultez la section correspondante de l’article [Créer, modifier ou supprimer un peering de réseau virtuel](virtual-network-manage-peering.md#requirements-and-constraints). Pour en savoir plus sur les limites concernant le nombre de peerings que vous pouvez créer pour un réseau virtuel, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Autorisations

Pour en savoir plus sur les autorisations requises pour créer un peering de réseaux virtuels, consultez [Créer, modifier ou supprimer un peering de réseau virtuel](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Tarifs

Un coût nominal s’applique pour le trafic entrant et sortant qui utilise une connexion de peering de réseau virtuel. Pour plus d’informations sur la tarification de VNet Peering (homologation de réseaux virtuels) et Global VNet Peering, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/virtual-network).

Le transit de passerelle est une propriété de peering qui permet à un réseau virtuel d’exploiter une passerelle VPN/ExpressRoute d’un réseau virtuel appairé pour la mise en œuvre d’une connectivité intersite ou de réseau virtuel à réseau virtuel. Le trafic vers la passerelle (en entrée ou en sortie) dans le réseau virtuel appairé entraîne des frais d’appairage de réseau virtuel. Pour plus d’informations, consultez les pages dédiées aux [frais de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/), aux frais de passerelle ExpressRoute ou aux [frais d’appairage de réseau virtuel](https://azure.microsoft.com/pricing/details/virtual-network).

>[!NOTE]
> Une version précédente de ce document indiquait que les frais de VNET Peering ne s’appliquaient pas au transit par passerelle. Elle a été mise à jour pour refléter le tarif correct indiqué sur la page de tarification.

## <a name="next-steps"></a>Étapes suivantes

* Un peering est généré entre des réseaux virtuels créés via des modèles de déploiement identiques ou différents qui existent dans des abonnements identiques ou différents. Suivez un didacticiel pour l’un des scénarios suivants :

    |Modèle de déploiement Azure             | Subscription  |
    |---------                          |---------|
    |Les deux modèles Resource Manager              |[Identique](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Différent](create-peering-different-subscriptions.md)|
    |Un modèle Resource Manager, un modèle classique  |[Identique](create-peering-different-deployment-models.md)|
    |                                   |[Différent](create-peering-different-deployment-models-subscriptions.md)|

* Découvrez comment créer une [topologie de réseau Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* En savoir plus sur tous les [paramètres de peering de réseaux virtuels et comment les modifier](virtual-network-manage-peering.md)
* Découvrez les réponses aux questions courantes concernant VNet Peering et Global VNet Peering par le biais de notre [Forum Aux Questions sur l’homologation de réseaux virtuels](virtual-networks-faq.md#vnet-peering).
