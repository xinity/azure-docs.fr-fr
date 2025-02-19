---
title: Résolution des problèmes de connexion courants à Azure SQL Database
description: Opérations servant à identifier et résoudre les erreurs de connexion courantes pour Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: cd0ab6d89d88c594d283dc0718c0f58ebb98bf43
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090796"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Résoudre des problèmes de connexion à Azure SQL Database

Si la connexion à Azure SQL Database échoue, vous recevez des [messages d’erreur](sql-database-develop-error-messages.md). Cet article est une rubrique centralisée qui vous permet de résoudre les problèmes de connectivité Azure SQL Database. Il présente les [causes courantes](#cause) des problèmes de connexion, vous recommande un [outil de dépannage](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) qui vous permet d’identifier le problème, et fournit les étapes de dépannage permettant de résoudre les [erreurs temporaires](#troubleshoot-transient-errors) et les [erreurs persistantes ou non temporaires](#troubleshoot-persistent-errors). 

Si vous rencontrez des problèmes de connexion, essayez de suivre les étapes de dépannage décrites dans cet article.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Cause :

Les problèmes de connexion peuvent avoir l’une des causes suivantes :

* Échec de la mise en œuvre de bonnes pratiques et de directives de conception pendant le processus de conception de l’application.  Pour commencer, consultez la page [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) .
* Reconfiguration d’Azure SQL Database
* Paramètres du pare-feu
* Expiration du délai de connexion
* Informations de connexion incorrectes
* Dépassement de la limite maximale sur certaines ressources de la base de données Azure SQL

En général, les problèmes de connexion à la base de données Azure SQL peuvent être classés ainsi :

* [Erreurs temporaires (de courte durée ou intermittentes)](#troubleshoot-transient-errors)
* [Erreurs persistantes ou non temporaires (erreurs qui se produisent régulièrement)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Essayez l’utilitaire de résolution des problèmes de connectivité des bases de données Azure SQL

Si vous rencontrez une erreur de connexion spécifique, essayez [cet outil](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), qui vous aidera à identifier et à résoudre rapidement votre problème.

## <a name="troubleshoot-transient-errors"></a>Résoudre les erreurs temporaires

Lorsqu’une application se connecte à une base de données Azure SQL, le message d’erreur suivant s’affiche :

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ce message d’erreur est généralement temporaire (de courte durée).
> 
> 

Cette erreur se produit lorsque la base de données est déplacée (ou reconfigurée) et que votre application perd sa connexion à la base de données. Les événements de reconfiguration de la base de données sont liés à un événement planifié (par exemple, une mise à niveau logicielle) ou à un événement non planifié (par exemple, un arrêt de processus ou un équilibrage de charge). La plupart des événements de reconfiguration sont généralement de courte durée et se terminent en l’espace de 60 secondes maximum. Cependant, ces événements peuvent parfois prendre plus de temps, par exemple lorsqu’une transaction volumineuse entraîne une récupération de longue durée.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Étapes pour résoudre les problèmes de connectivité transitoire

1. Consultez le [tableau de bord du service Microsoft Azure](https://azure.microsoft.com/status) pour obtenir la liste des coupures prévues qui se sont produites au moment où les erreurs ont été signalées par l’application.
2. Les applications qui se connectent à un service cloud, tel qu’Azure SQL Database, doivent s’attendre à des événements périodiques de reconfiguration et implémenter une logique de nouvelle tentative pour gérer ces erreurs au lieu d’afficher ces événements en tant qu’erreurs de l’application aux utilisateurs. Consultez la section [Erreurs temporaires](sql-database-connectivity-issues.md), ainsi que les bonnes pratiques et les instructions de conception dans [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) pour obtenir plus d’informations et découvrir les stratégies générales de nouvelle tentative. Passez ensuite en revue les exemples de code dans l’article [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md) pour obtenir des informations spécifiques.
3. Lorsqu’une base de données approche des limites de ressources, cela peut s’apparenter à un problème de connectivité transitoire. Consultez l’article [Limites des ressources](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Si les problèmes de connectivité persistent ou si la durée pendant laquelle votre application rencontre une erreur dépasse les 60 secondes ou si plusieurs occurrences de l’erreur s’affichent dans un jour donné, créez une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Résoudre les erreurs persistantes
Si l’application échoue de façon permanente à se connecter à Azure SQL Database, cela indique généralement un problème avec l’un des éléments suivants :

* Configuration du pare-feu. Le pare-feu Azure SQL Database ou côté client bloque les connexions à Azure SQL Database.
* Reconfiguration du réseau côté client : par exemple, une nouvelle adresse IP ou un nouveau serveur proxy.
* Erreur utilisateur : par exemple, paramètres de connexion saisis de façon incorrecte, comme le nom du serveur dans la chaîne de connexion.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Étapes permettant résoudre les problèmes de connectivité persistants
1. Configurez les [règles de pare-feu](sql-database-configure-firewall-settings.md) pour autoriser l’adresse IP du client. Définissez une règle de pare-feu avec une plage d’adresses IP allant de 0.0.0.0 à 255.255.255.255 à des fins de test temporaire. Cette opération ouvrira le serveur à toutes les adresses IP. Si elle résout votre problème de connectivité, supprimez cette règle et créez une règle de pare-feu pour une adresse ou une plage d’adresses IP correctement bornée. 
2. Sur tous les pare-feu situés entre le client et Internet, assurez-vous que le port 1433 est ouvert pour les connexions sortantes. Consultez les pages [Configurer le pare-feu Windows de façon à autoriser l’accès à SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) et [Ports et protocoles requis pour l’identité hybride](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) pour connaître les pointeurs supplémentaires liés aux ports supplémentaires que vous devez ouvrir pour l’authentification Azure Active Directory.
3. Vérifiez votre chaîne de connexion et d’autres paramètres de connexion. Consultez la section Chaîne de connexion dans la [rubrique concernant les problèmes de connectivité](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Vérifiez l’état du service dans le tableau de bord. Si vous soupçonnez une panne régionale, consultez [Restauration à la suite d’une panne](sql-database-disaster-recovery.md) pour connaître les étapes de restauration vers une nouvelle zone.

## <a name="next-steps"></a>Étapes suivantes
* [Rechercher dans la documentation de Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Voir les dernières mises à jour du service Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* [Aide générale sur le traitement des erreurs temporaires](../best-practices-retry-general.md)
* [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)

