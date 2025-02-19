---
title: Configurer la méthode de routage du trafic en tourniquet par pondération à l’aide d’Azure Traffic Manager | Microsoft Docs
description: Cet article explique comment équilibrer le trafic à l’aide d’une méthode en tourniquet dans Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 4ca43bf958606a71911bf5d35f31e4fe0b342601
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071269"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Configurer la méthode de routage du trafic par pondération dans Traffic Manager

Il est courant d'utiliser un ensemble de points de terminaison identiques, comprenant des services cloud et des sites web, et de répartir le trafic à parts égales entre eux. Les étapes suivantes décrivent comment configurer ce type de méthode de routage du trafic.

> [!NOTE]
> Azure Web App propose déjà une fonctionnalité d’équilibrage de charge en tourniquet (round robin) pour les sites web d’une région Azure (comportant potentiellement plusieurs centres de données). Traffic Manager permet de répartir le trafic entre les sites web de différents centres de données.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Pour configurer la méthode de routage du trafic par pondération

1. Dans un navigateur, connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne possédez pas encore de compte, vous pouvez [vous inscrire pour bénéficier d’un essai gratuit d’un mois](https://azure.microsoft.com/free/). 
2. Dans la barre de recherche du portail, recherchez les **profils Traffic Manager** et cliquez sur le nom du profil pour lequel vous souhaitez configurer la méthode de routage.
3. Dans le panneau **Profil Traffic Manager**, vérifiez que les services cloud et les sites web que vous souhaitez inclure dans votre configuration sont présents.
4. Dans la section **Paramètres**, cliquez sur **Configuration** et dans le panneau **Configuration**, procédez comme suit :
    1. Pour les **paramètres de la méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **pondérée**. Si ce n’est pas le cas, cliquez sur **Pondérée** dans la liste déroulante.
    2. Définissez l’option **Paramètres de surveillance des points de terminaison** de manière identique pour tous les points de terminaison de ce profil comme suit :
        1. Sélectionnez le **protocole** approprié et spécifiez le numéro du **port**. 
        2. Pour **Chemin d’accès**, entrez une barre oblique */* . Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Une barre oblique (« / ») est une entrée valide pour le chemin d’accès relatif. Elle implique que le fichier se trouve dans le répertoire racine (par défaut).
        3. En haut de la page, cliquez sur **Enregistrer**.
5. Testez les modifications dans votre configuration comme suit :
    1.  Dans la barre de recherche du portail, recherchez le nom du profil Traffic Manager et cliquez sur le profil Traffic Manager dans les résultats affichés.
    2.  Dans le panneau du profil **Traffic Manager**, cliquez sur **Vue d’ensemble**.
    3.  Le panneau **Profil Traffic Manager** affiche le nom DNS de votre profil Traffic Manager nouvellement créé. Celui-ci peut être utilisé par tous les clients (par exemple, en y accédant à l’aide d’un navigateur web) pour être routés vers le point de terminaison correct, comme déterminé par le type de routage. Dans ce cas, toutes les demandes sont routées vers chaque point de terminaison de manière alternée.
6. Une fois le profil Traffic Manager opérationnel, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d’entreprise vers le nom de domaine Traffic Manager.

![Configuration de la méthode de routage du trafic par pondération à l’aide de Traffic Manager][1]

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [méthode de routage du trafic prioritaire](traffic-manager-configure-priority-routing-method.md).
- En savoir plus sur la [méthode de routage du trafic basé sur les performances](traffic-manager-configure-performance-routing-method.md).
- En savoir plus sur la [méthode de routage géographique](traffic-manager-configure-geographic-routing-method.md).
- Découvrez comment [tester les paramètres Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
