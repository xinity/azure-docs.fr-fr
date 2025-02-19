---
title: Configurer la surveillance et les alertes pour les tâches Azure Stream Analytics
description: Cet article explique comment utiliser le portail Azure pour configurer la surveillance, ainsi que des alertes pour les tâches Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0fd489d856a16953a5a450a347c9737fe440ad28
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621771"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configuration d’alertes pour des tâches Azure Stream Analytics

Vous devez superviser votre travail Azure Stream Analytics pour assurer la continuité de l’exécution du travail sans problème. Cet article décrit comment configurer des alertes pour les scénarios courants qui doivent être supervisés. 

Vous pouvez définir des règles sur les métriques à partir des données des journaux des opérations via le portail, ainsi que [par programmation](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurer des alertes dans le portail Azure
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Recevoir des alertes lorsqu’un travail s’arrête de façon inattendue

L’exemple suivant montre comment configurer des alertes quand votre travail entre dans un état d’échec. Cette alerte est recommandée pour tous les travaux.

1. Dans le portail Azure, ouvrez le travail Stream Analytics pour lequel vous souhaitez créer une alerte.

2. Dans la page **Travail**, accédez à la section **Supervision**.  

3. Sélectionnez **Métriques**, puis **Nouvelle règle d’alerte**.

   ![Configuration des alertes Stream Analytics sur le portail Azure](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Le nom de votre travail Stream Analytics doit automatiquement apparaître sous **RESSOURCE**. Cliquez sur **Ajouter une condition**, puis sélectionnez **Toutes les opérations d’administration** sous **Configurer la logique du signal**.

   ![Sélectionnez le nom du signal pour l’alerte Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Sous **Configurer la logique du signal**, définissez **Niveau d’événement** sur **Tous** et **État** sur **Échec**. Laissez vide **Événement lancé par** et sélectionnez **Terminé**.

   ![Configurer la logique du signal de l’alerte Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Sélectionnez un groupe d’actions existant ou créez-en un. Dans cet exemple, un nouveau groupe d’actions appelé **TIDashboardGroupActions** a été créé avec une action **Envoie un e-mail** qui envoie un e-mail aux utilisateurs qui ont le rôle Azure Resource Manager **Propriétaire**.

   ![Configuration d’une alerte pour un travail Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Les champs **RESSOURCE**, **CONDITION** et **GROUPES D’ACTIONS** doivent chacun avoir une entrée. Notez que pour que les alertes se déclenchent, les conditions définies doivent être respectées. Par exemple, vous pouvez mesurer la valeur moyenne d’une métrique sur les 15 dernières minutes, toutes les 5 minutes.

   ![Créer une règle d’alerte Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Ajoutez un **nom de règle d’alerte**, une **description** et votre **groupe de ressources** aux **DÉTAILS DE L’ALERTE** et cliquez sur **Créer une règle d’alerte** pour créer la règle de votre travail Stream Analytics.

   ![Créer une règle d’alerte Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scénarios à superviser

Les alertes suivantes sont recommandées pour superviser les performances de votre travail Stream Analytics. Ces métriques doivent être évaluées toutes les minutes sur les 5 dernières minutes.

|Métrique|Condition|Agrégation de temps|Seuil|Actions correctives|
|-|-|-|-|-|
|Utilisation d’unité de streaming (%)|Supérieur à|Maximale|80|Plusieurs facteurs augmentent le pourcentage d’utilisation d’unités de streaming. Vous pouvez effectuer une mise à l’échelle avec la parallélisation de requête ou augmenter le nombre d’unités de streaming. Pour plus d’informations, consultez [Profiter de la parallélisation de requête dans Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erreurs d’exécution|Supérieur à|Total|0|Examinez l’activité ou les journaux de diagnostic, et effectuez les changements appropriés sur les entrées, la requête ou les sorties.|
|Délai en filigrane|Supérieur à|Maximale|Quand la valeur moyenne de cette métrique sur les 15 dernières minutes est supérieure à la tolérance d’arrivée tardive (en secondes). Si vous n’avez pas modifié la tolérance d’arrivée tardive, la valeur par défaut est définie sur 5 secondes.|Essayez d’augmenter le nombre d’unités de streaming ou de paralléliser votre requête. Pour plus d’informations sur les unités de streaming, consultez [Comprendre et ajuster les unités de streaming](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Pour plus d’informations sur la parallélisation de votre requête, consultez [Tirer parti de la parallélisation de requête dans Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erreurs de désérialisation d’entrée|Supérieur à|Total|0|Examinez l’activité ou les journaux de diagnostic, et effectuez les changements appropriés sur l’entrée. Pour plus d’informations sur les journaux de diagnostic, consultez [Résoudre les problèmes d’Azure Stream Analytics à l’aide des journaux de diagnostic](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Obtenir de l’aide

Pour plus d’informations sur la configuration d’alertes dans le portail Azure, consultez [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

