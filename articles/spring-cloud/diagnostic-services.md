---
title: Analyser les journaux et les métriques dans Azure Spring Cloud | Microsoft Docs
description: Découvrez comment analyser les données de diagnostic dans Azure Spring Cloud.
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 955641f3511989baa5bfc3c0fa4d7df7ccbf9bfa
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554583"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analyser les journaux et les métriques avec les paramètres de diagnostic

La fonctionnalité de diagnostic d’Azure Spring Cloud vous permet d’analyser les journaux et les métriques à l’aide d’un des services suivants :

* Utilisez Azure Log Analytics, où les données sont écrites immédiatement sans avoir à être écrites au préalable dans le stockage.
* Enregistrez-les dans un compte de stockage pour les auditer ou les inspecter manuellement. Spécifiez la durée de conservation (en jours).
* Envoyez-les par streaming à votre hub d’événements pour ingestion par un service tiers ou une solution d’analyse personnalisée.

Pour commencer, activez l’un de ces services pour recevoir les données. Pour en savoir plus sur la configuration de Log Analytics, consultez [Prise en main de Log Analytics dans Azure Monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Configurer les paramètres de diagnostic

1. Dans le Portail Azure, accédez à votre instance Azure Spring Cloud.
1. Sélectionnez l’option **Paramètres de diagnostic**, puis **Ajouter un paramètre de diagnostic**.
1. Entrez un nom pour le paramètre, puis choisissez l’emplacement où vous souhaitez envoyer les journaux. Vous pouvez sélectionner n’importe quelle combinaison des trois options suivantes :
    * **Archiver dans un compte de stockage**
    * **Diffuser vers un hub d’événements**
    * **Envoyer à Log Analytics**

1. Choisissez la catégorie de journal et la catégorie de métrique à superviser, puis spécifiez la durée de conservation (en jours). La durée de conservation s’applique uniquement au compte de stockage.
1. Sélectionnez **Enregistrer**.

> [!NOTE]
> Il peut s’écouler jusqu’à 15 minutes entre le moment où les journaux ou les métriques sont émis et le moment où ils apparaissent dans votre compte de stockage, votre hub d’événements ou Log Analytics.

## <a name="view-the-logs"></a>Afficher les journaux

### <a name="use-log-analytics"></a>Utiliser Log Analytics

1. Dans le Portail Azure, dans le volet de gauche, sélectionnez **Log Analytics**.
1. Sélectionnez l’espace de travail Log Analytics que vous avez choisi quand vous avez ajouté vos paramètres de diagnostic.
1. Pour ouvrir le volet **Recherche dans les journaux**, sélectionnez **Journaux**.
1. Dans la zone de recherche **Journal**, entrez une requête simple telle que :

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Pour afficher le résultat de la recherche, sélectionnez **Exécuter**.
1. Vous pouvez rechercher les journaux de l’application ou de l’instance spécifique en définissant une condition de filtre :

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Pour en savoir plus sur le langage de requête qui est utilisé dans Log Analytics, consultez [Requêtes de journal Azure Monitor](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Utiliser votre compte de stockage 

1. Dans le Portail Azure, dans le volet de gauche, sélectionnez **Comptes de stockage**.

1. Sélectionnez le compte de stockage que vous avez choisi quand vous avez ajouté vos paramètres de diagnostic.
1. Pour ouvrir le volet **Conteneur d’objets blob**, sélectionnez **Objets blob**.
1. Pour consulter les journaux d’application, recherchez un conteneur appelé **insights-logs-applicationconsole**.
1. Pour consulter les métriques des applications, recherchez un conteneur appelé **insights-metrics-pt1m**.

Pour en savoir plus sur l’envoi d’informations de diagnostic à un compte de stockage, consultez [Stocker et afficher des données de diagnostic dans le stockage Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Utiliser votre hub d’événements

1. Dans le Portail Azure, dans le volet de gauche, sélectionnez **Hubs d’événements**.

1. Recherchez et sélectionnez le hub d’événements que vous avez choisi quand vous avez ajouté vos paramètres de diagnostic.
1. Pour ouvrir le volet **Liste des hubs d’événements**, sélectionnez **Hubs d’événements**.
1. Pour consulter les journaux d’application, recherchez un hub d’événements appelé **insights-logs-applicationconsole**.
1. Pour consulter les métriques des applications, recherchez un hub d’événements appelé **insights-metrics-pt1m**.

Pour en savoir plus sur l’envoi d’informations de diagnostic à un hub d’événements, consultez [Diffusion des données de Diagnostics Azure dans le chemin réactif à l’aide d’Event Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analyser les journaux

Azure Log Analytics fournit Kusto afin que vous puissiez interroger vos journaux à des fins d’analyse. Pour obtenir une présentation rapide de l’interrogation des journaux à l’aide de Kusto, passez en revue le [tutoriel sur Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Les journaux d’application fournissent des informations critiques sur l’intégrité de votre application, ses performances, et bien plus encore. Les sections suivantes présentent quelques requêtes simples pour vous aider à comprendre les états actuels et passés de votre application.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Afficher les journaux d’application à partir d’Azure Spring Cloud

Pour consulter la liste des journaux d’application à partir d’Azure Spring Cloud, triés par heure avec les journaux les plus récents affichés en premier, exécutez la requête suivante :

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Afficher les entrées de journaux contenant des erreurs ou des exceptions

Pour examiner les entrées de journal non triées qui mentionnent une erreur ou une exception, exécutez la requête suivante :

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Utilisez cette requête pour rechercher des erreurs, ou modifiez les termes de la requête afin de rechercher des codes d’erreur ou des exceptions spécifiques. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Afficher le nombre d’erreurs et d’exceptions signalées par votre application au cours de la dernière heure

Pour créer un graphique à secteurs indiquant le nombre d’erreurs et d’exceptions journalisées par votre application au cours de la dernière heure, exécutez la requête suivante :

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>En savoir plus sur l’interrogation des journaux d’application

Azure Monitor fournit une prise en charge étendue de l’interrogation des journaux d’application à l’aide de Log Analytics. Pour plus d’informations sur ce service, consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](../azure-monitor/log-query/get-started-queries.md). Pour plus d’informations sur la création de requêtes pour analyser vos journaux d’application, consultez [Vue d’ensemble des requêtes de journal dans Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
