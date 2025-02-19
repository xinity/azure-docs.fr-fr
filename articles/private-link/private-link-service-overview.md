---
title: Qu’est-ce que le service Azure Private Link ?
description: Découvrez le service Azure Private Link.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: cad8e3e4f32a8773fe914362b637d39765a23c21
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672529"
---
# <a name="what-is-azure-private-link-service"></a>Qu’est-ce que le service Azure Private Link ?

Le service Azure Private Link est la référence à votre propre service Azure Private Link. L’accès Private Link peut être activé pour un service qui s’exécute derrière [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md). De cette façon, les utilisateurs du service pourront y accéder à l’aide d’une connexion privée, à partir de leurs propres réseaux virtuels. Vos clients peuvent créer un point de terminaison privé dans leur réseau virtuel et le mapper dans ce service. Cet article explique les concepts qui sont utilisés côté fournisseur de services. 

## <a name="workflow"></a>Workflow

![Workflow du service Private Link](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Créer un service Private Link

- Configurez votre application pour qu’elle s’exécute derrière un équilibreur de charge standard dans votre réseau virtuel. Si votre application est déjà configurée pour s’exécuter derrière un équilibreur de charge standard, vous pouvez ignorer cette étape.   
- Créez un service Private Link référençant l’équilibreur de charge ci-dessus. Durant le processus de sélection de l’équilibrage de charge, choisissez la configuration d’adresse IP front-end où vous souhaitez recevoir le trafic. Choisissez un sous-réseau pour les adresses IP NAT du service Private Link. Il est recommandé de disposer d’au moins 8 adresses IP NAT disponibles dans le sous-réseau. Pour le fournisseur de services, tout le trafic des utilisateurs semble provenir de ce pool d’adresses IP privées. Choisissez les propriétés et les paramètres nécessaires pour le service Private Link.    

    > [!NOTE]
    > Le service Azure Private Link est uniquement pris en charge sur Standard Load Balancer. 
    
### <a name="share-your-service"></a>Partager un service

Une fois que vous avez créé un service Private Link, Azure génère un moniker global unique appelé « alias », basé sur le nom que vous avez fourni pour votre service. Vous pouvez partager l’alias ou l’URI de ressource de votre service avec vos clients hors connexion. Les utilisateurs peuvent démarrer une connexion Private Link à l’aide de l’alias ou de l’URI de ressource.
 
### <a name="manage-your-connection-requests"></a>Gérer les demandes de connexion

Lorsque l’utilisateur démarre une connexion, le fournisseur de services peut accepter ou rejeter la demande de connexion. Toutes les demandes de connexion sont listées sous la propriété **privateendpointconnections** dans le service Private Link.
 
### <a name="delete-your-service"></a>Supprimer le service

Si le service Private Link n’est plus utilisé, vous pouvez le supprimer. Toutefois, avant de le supprimer, vérifiez qu’il n’est associé à aucune connexion de point de terminaison privé. Vous pouvez rejeter toutes les connexions et supprimer le service.

## <a name="properties"></a>properties

Le service Private Link spécifie les propriétés suivantes : 

|Propriété |Explication  |
|---------|---------|
|État de provisionnement (provisioningState)  |Propriété en lecture seule qui liste l’état de provisionnement actuel du service Private Link. Les états de provisionnement applicables sont les suivants : « Deleting; Failed; Succeeded; Updating » (Suppression, Échec, Réussite, Mise à jour). Lorsque l’état de provisionnement est « Succeeded », cela signifie que votre service Private Link a bien été provisionné.        |
|Alias (alias)     | L’alias est une chaîne globale unique en lecture seule qui représente votre service. Il vous permet de masquer les données client de votre service, et de créer un nom facile à partager pour votre service. Lorsque vous créez un service Private Link, Azure génère un alias pour ce service, que vous pouvez ensuite partager avec vos clients. Vos clients peuvent utiliser cet alias pour demander une connexion à votre service.          |
|Visibilité (visibility)     | La propriété visibility permet de contrôler les paramètres d’exposition de votre service Private Link. Les fournisseurs de services peuvent choisir d’exposer leurs services aux abonnements disposant d’autorisations de contrôle d’accès en fonction du rôle (RBAC), à un groupe restreint d’abonnements ou à tous les abonnements Azure.          |
|Approbation automatique (autoApproval)    |   L’approbation automatique permet de contrôler l’accès automatisé au service Private Link. Les abonnements qui figurent dans la liste d’approbation automatique sont approuvés automatiquement lorsqu’une connexion est demandée à partir des points de terminaison privés de ces abonnements.          |
|Configuration de l’adresse IP front-end de l’équilibreur de charge (loadBalancerFrontendIpConfigurations)    |    Le service Private Link est associé à l’adresse IP front-end d’un équilibreur de charge standard. Tout le trafic destiné au service atteindra le front-end de l’équilibreur de charge standard. Vous pouvez configurer des règles d’équilibreur de charge standard pour diriger ce trafic vers les pools back-end où sont exécutées vos applications. Les adresses IP front-end d’équilibreur de charge sont différentes des adresses IP NAT.      |
|Configuration d’adresse IP NAT (ipConfigurations)    |    Cette propriété concerne la configuration de l’adresse IP NAT (traduction d’adresses réseau) du service Private Link. L’adresse IP NAT peut être choisie à partir de n’importe quel sous-réseau du réseau virtuel d’un fournisseur de services. Le service Private Link effectue la traduction d’adresses réseau (NAT) côté destination pour le trafic Private Link. Cela évite tout conflit d’adresses IP entre l’espace d’adressage source (côté client) et l’espace d’adressage de destination (fournisseur de services). Côté destination (fournisseur de services), l’adresse IP NAT s’affichera en tant qu’adresse IP source pour tous les paquets reçus par votre service, et en tant qu’adresse IP de destination pour tous les paquets envoyés par votre service.       |
|Connexions de points de terminaison privés (privateEndpointConnections)     |  Cette propriété liste les points de terminaison privés qui se connectent au service Private Link. Plusieurs points de terminaison privés peuvent se connecter au même service Private Link, et le fournisseur de services peut contrôler l’état de chaque point de terminaison privé.        |
|||


### <a name="details"></a>Détails

- Le service Private Link est accessible à partir des points de terminaison privés approuvés qui sont situés dans la même région que lui. Vous pouvez accéder à un point de terminaison privé à partir du réseau virtuel auquel il appartient, à partir de réseaux virtuels appairés au niveau régional, de réseaux virtuels appairés au niveau mondial, ainsi que localement, à l’aide de connexions privées VPN ou ExpressRoute. 
 
- Lors de la création d’un service Private Link, une interface réseau est créée pour le cycle de vie de la ressource. Cette interface ne peut pas être gérée par le client.
 
- Le service Private Link doit être déployé dans la même région que le réseau virtuel et Standard Load Balancer.  
 
- Plusieurs points de terminaison privés appartenant à différents réseaux virtuels, abonnements et/ou locataires Active Directory peuvent accéder à un service Private Link. La connexion est établie via un workflow de connexion. 
 
- Vous pouvez créer plusieurs services Private Link sur une même instance de Standard Load Balancer à l’aide de différentes configurations d’adresses IP front-end. Le nombre de services Private Link qu’il est possible de créer dans chaque instance Standard Load Balancer et dans chaque abonnement est limité. Pour plus d’informations, consultez  [Limites Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).
 
- Le service Private Link peut être associé à plusieurs configurations d’adresses IP NAT. Le fait de choisir plusieurs configurations d’adresses IP NAT peut permettre aux fournisseurs de services d’effectuer une mise à l’échelle. Aujourd’hui, les fournisseurs de services peuvent attribuer jusqu’à 8 adresses IP NAT à chaque service Private Link. Avec chaque adresse IP NAT, vous pouvez affecter davantage de ports pour vos connexions TCP et, donc, effectuer un scale-out. Une fois que vous avez ajouté des adresses IP NAT à un service Private Link, vous ne pouvez pas supprimer ces adresses. Ceci a été fait dans le but d’empêcher que les connexions actives ne soient pas impactées par la suppression d’adresses IP NAT.


## <a name="alias"></a>Alias

Un **alias** est un nom global unique correspondant à votre service. Il vous permet de masquer les données client de votre service, et de créer un nom facile à partager pour votre service. Lorsque vous créez un service Private Link, Azure génère un alias pour ce service, que vous pouvez ensuite partager avec vos clients. Vos clients peuvent utiliser cet alias pour demander une connexion à votre service.

L’alias se compose de trois parties : *Préfixe*.*GUID*.*Suffixe*

- Le préfixe correspond au nom du service. Vous pouvez choisir votre propre préfixe. Une fois l’alias créé, vous ne pouvez pas le modifier. Vous devez donc bien réfléchir au choix de votre préfixe.  
- Le GUID sera fourni par la plateforme. Cela permet d’obtenir plus facilement un nom global unique. 
- Le suffixe est ajouté par Azure : *région*.azure.privatelinkservice 

Alias complet :  *Préfixe*. {GUID}.*région*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Contrôler l’exposition du service

Le service Private Link fournit des options pour contrôler l’exposition de votre service par le biais du paramètre « Visibility ». Vous pouvez rendre le service privé pour qu’il ne soit utilisé qu’à partir des réseaux virtuels dont vous êtes propriétaire (autorisations RBAC uniquement), vous pouvez restreindre l’exposition à un groupe d’abonnements que vous approuvez, ou vous pouvez le rendre public afin que tous les abonnements Azure puissent demander à se connecter au service Private Link. Les paramètres de visibilité déterminent si un utilisateur peut se connecter à votre service. 

## <a name="control-service-access"></a>Contrôler l’accès au service

Les utilisateurs qui ont accès à votre service Private Link (par le biais du paramètre de visibilité) peuvent créer un point de terminaison privé dans leurs réseaux virtuels et demander à se connecter à votre service Private Link. La connexion de point de terminaison privé sera créée à l’état « Pending » (En attente) dans l’objet du service Private Link. Le fournisseur de services est chargé d’approuver, rejeter ou supprimer la demande de connexion. Vous pouvez approuver, rejeter ou supprimer la connexion. Seules les connexions approuvées peuvent envoyer du trafic vers le service Private Link.

L’approbation des connexions peut être automatisée à l’aide de la propriété d’approbation automatique du service Private Link. L’approbation automatique permet aux fournisseurs de services de préapprouver un groupe d’abonnements pour leur permettre d’accéder automatiquement à leur service. Les clients devront partager leurs abonnements hors connexion pour que les fournisseurs de services puissent les ajouter à la liste d’approbation automatique. L’approbation automatique est un sous-ensemble du tableau de visibilité. La propriété de visibilité permet de contrôler l’exposition de votre service, tandis que la propriété d’approbation automatique permet de contrôler les paramètres d’approbation de votre service. Si un client demande à se connecter à partir d’un abonnement figurant dans la liste d’approbation automatique, la connexion est automatiquement approuvée puis établie. Les fournisseurs de services n’ont plus besoin d’approuver manuellement les demandes. En revanche, si un client demande à se connecter à partir d’un abonnement du tableau de visibilité et non du tableau d’approbation automatique, la demande sera reçue par le fournisseur de services, et celui-ci devra approuver manuellement les connexions.

## <a name="limitations"></a>Limites

Voici les limitations connues lors de l’utilisation du service Private Link :
- Il est pris en charge uniquement avec Standard Load Balancer. 
- Il prend en charge uniquement le trafic IPv4.
- Il prend en charge uniquement le trafic TCP.
- Il est accessible uniquement à partir des points de terminaison privés situés dans la même région que lui.
- La création et la gestion ne sont pas possibles sur le portail Azure.
- Les informations de connexion clients utilisant le protocole proxy ne sont pas disponibles pour le fournisseur de services.

## <a name="next-steps"></a>Étapes suivantes
- [Créer un service Private Link à l’aide d’Azure PowerShell](create-private-link-service-powershell.md)
- [Créer un service Private Link à l’aide d’Azure CLI](create-private-link-service-cli.md)
