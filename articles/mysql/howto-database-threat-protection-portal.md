---
title: Advanced Threat Protection – Azure Database pour MySQL
description: Découvrez comment configurer Advanced Threat Protection de façon à détecter les activités de base de données anormales pouvant indiquer des menaces sur la sécurité de la base de données.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: fe058433cfe4c6c9f89f14d2c74e33ff1b8372ac
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970499"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Protection avancée contre les menaces d’Azure Database pour MySQL

La protection avancée contre les menaces d’Azure Database pour MySQL détecte des activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos bases de données.

La protection avancée contre les menaces fait partie de l’offre Advanced Data Security qui est un package unifié de fonctionnalités de sécurité avancées. La protection avancée contre les menaces peut être accessible et gérée via le [portail Azure](https://portal.azure.com), et est actuellement en préversion.

> [!NOTE]
> La fonctionnalité Advanced Threat Protection **n’est pas** disponible dans les régions administratives et souveraines suivantes du cloud Azure : US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginie, US DoD Est, US DoD Central, Allemagne Centre, Allemagne Nord, Chine Est, Chine Est 2. Consultez les [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/) pour connaître la disponibilité générale des produits.
>

> [!NOTE]
> Cette fonctionnalité est disponible dans toutes les régions Azure où Azure Database pour MySQL est déployé, pour les serveurs à usage général et à mémoire optimisée.

## <a name="set-up-threat-detection"></a>Configurer la détection des menaces
1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration du serveur Azure Database pour MySQL que vous voulez protéger. Dans les paramètres de sécurité, sélectionnez **Protection avancée contre les menaces (préversion)** .
3. Dans la page de configuration **Protection avancée contre les menaces (préversion)** :

   - Activez la protection avancée contre les menaces sur le serveur.
   - Dans les **paramètres de protection avancée contre les menaces**, dans la zone de texte **Envoyer les alertes à**, fournissez la liste d’adresses e-mail devant recevoir des alertes de sécurité lors de la détection d’activités de base de données anormales.
  
   ![Configurer la détection des menaces](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Explorer les activités de base de données anormales

Vous recevez une notification par e-mail quand des activités anormales sont détectées sur la base de données. L’e-mail contient des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur, le nom de l’application et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées pour examiner et atténuer la menace potentielle pesant sur la base de données.
 
1. Cliquez sur le lien **Afficher les alertes récentes** figurant dans l’e-mail pour ouvrir le portail Azure et accéder à la page d’alertes d’Azure Security Center, qui fournit une vue d’ensemble des menaces actives détectées sur la base de données SQL.
    
    ![Rapports d’activités anormales](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Affichez les menaces actives :

    ![Menaces actives](./media/howto-database-threat-protection-portal/active-threats.png)

2. Cliquez sur une alerte spécifique pour obtenir des détails supplémentaires et des actions permettant d’examiner cette menace et d’atténuer les menaces futures.
    
    ![Alerte spécifique](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explorer les alertes de détection de menace

SQL Database Threat Detection intègre ses alertes avec [Azure Security Center](https://azure.microsoft.com/services/security-center/). Une vignette de détection des menaces SQL en direct assure le suivi de l’état les menaces actives au niveau de la base de données et des pages SQL ATP sur le portail Azure.

Cliquez sur **Threat detection alert** (Alerte de détection des menaces) pour ouvrir la page d’alertes d’Azure Security Center et obtenir une vue d’ensemble des menaces SQL actives détectées sur la base de données.

   ![Alerte de détection des menaces](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Pour plus d’informations sur les prix, consultez la [page Tarification d’Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/mysql/).  
