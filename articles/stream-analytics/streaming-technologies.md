---
title: Choisir une technologie de traitement de streaming et d’analytique en temps réel sur Azure
description: Découvrez comment choisir la technologie adaptée pour l’analytique en temps réel et le traitement du streaming pour créer votre application sur Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: f46a35d971c008b61d4899e30101ea562d3cefea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483406"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Choisir une technologie de traitement de streaming et d’analytique en temps réel sur Azure

Plusieurs services sont disponibles pour le traitement du streaming et l’analytique en temps réel sur Azure. Cet article fournit les informations nécessaires pour déterminer la technologie la mieux adaptée à votre application.

## <a name="when-to-use-azure-stream-analytics"></a>Quand utiliser Azure Stream Analytics

Azure Stream Analytics est le service recommandé pour l’analytique de flux sur Azure. Il est destiné à un large éventail de scénarios qui incluent, sans s’y limiter, les scénarios suivants :

* Tableaux de bord pour la visualisation des données
* [Alertes](stream-analytics-set-up-alerts.md) en temps réel à partir de modèles temporels et spatiaux ou d’anomalies
* Extraire, transformer, charger (ETL)
* [Modèle d’approvisionnement en événements](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

L’ajout d’un travail Azure Stream Analytics à votre application est le moyen le plus rapide d’obtenir une analytique du streaming opérationnelle, en utilisant le langage SQL que vous connaissez déjà. Stream Analytics est un service de travaux. Vous n’êtes donc pas obligé de passer votre temps à gérer des clusters, et les temps d’arrêt ne sont pas un problème grâce au contrat SLA qui propose une disponibilité de 99,9 % des travaux. La facturation est également effectuée au niveau du travail, ce qui rend les coûts de démarrage peu élevés (une seule unité de streaming), mais scalables (jusqu’à 192 unités de streaming). Il est beaucoup plus économique d’exécuter des travaux Stream Analytics que d’exécuter et de maintenir un cluster.

Azure Stream Analytics fournit une riche expérience prête à l’emploi. Vous pouvez immédiatement tirer parti des fonctionnalités suivantes sans aucune installation supplémentaire :

* Opérateurs temporels intégrés, tels que des [agrégations fenêtrées](stream-analytics-window-functions.md), des jointures temporelles et des fonctions analytiques temporelles.
* Adaptateurs Azure natifs [d’entrée](stream-analytics-add-inputs.md) et [de sortie](stream-analytics-define-outputs.md).
* Prise en charge de la modification lente des [données de référence](stream-analytics-use-reference-data.md) (également appelées tables de choix), notamment la jonction avec des données de référence géospatiales pour le geofencing.
* Des solutions intégrées, telles que [Détection d’anomalies](stream-analytics-machine-learning-anomaly-detection.md).
* Plusieurs fenêtres de temps dans la même requête.
* Possibilité de composer plusieurs opérateurs temporels dans des séquences arbitraires.
* Latence de bout en bout inférieure à 100 ms à partir de l’entrée arrivant à Event Hubs, à la sortie dans Event Hubs, notamment le délai réseau depuis et vers Event Hubs, à haut débit soutenu.

## <a name="when-to-use-other-technologies"></a>Quand utiliser d’autres technologies

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Vous avez besoin d’une entrée ou sortie à partir de ou vers Kafka

Azure Stream Analytics ne comporte pas d’adaptateur d’entrée ni de sortie Apache Kafka. Si vous avez des événements qui arrivent ou que vous devez envoyer à Kafka, et que vous n’avez pas besoin d’exécuter votre propre cluster Kafka, vous pouvez continuer à utiliser Stream Analytics en envoyant des événements à Event Hubs à l’aide de l’API Kafka Event Hubs, sans modifier l’expéditeur d’événement. Si vous n’avez pas besoin exécuter votre propre cluster Kafka, vous pouvez utiliser Spark Structured Streaming, qui est entièrement pris en charge sur [Azure Databricks](../azure-databricks/index.yml), ou Storm sur [Azure HDInsight](../hdinsight/storm/apache-storm-overview.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Vous voulez écrire des fonctions définies par l’utilisateur, des agrégats définis par l’utilisateur et des désérialiseurs personnalisés dans un langage autre que JavaScript ou C#

Azure Stream Analytics prend en charge les fonctions définies par l’utilisateur ou les agrégats définis par l’utilisateur en JavaScript pour les travaux cloud et en C# pour les travaux IoT Edge. Les désérialiseurs C# définis par l’utilisateur sont également pris en charge. Si vous souhaitez implémenter un désérialiseur, une fonction définie par l’utilisateur ou un agrégat défini par l’utilisateur dans d’autres langages, tels que Java ou Python, vous pouvez utiliser Spark Structured Streaming. Vous pouvez également exécuter le **EventProcessorHost** Event Hubs sur vos propres machines virtuelles pour le traitement arbitraire du streaming.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Votre solution est située dans un environnement incluant plusieurs cloud ou en local

Azure Stream Analytics est une technologie propriétaire de Microsoft et est uniquement disponible sur Azure. Si vous avez besoin que votre solution soit portable sur les clouds ou localement, envisagez des technologies open source telles que Structured Streaming Spark ou Storm.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md)
* [Créer un travail Stream Analytics à l’aide d’Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Créer un travail Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md)
* [Créer un travail Stream Analytics à l’aide de Visual Studio Code](quick-create-vs-code.md)