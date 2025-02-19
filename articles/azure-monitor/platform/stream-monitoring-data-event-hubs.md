---
title: Envoyer en streaming des données de supervision Azure vers un hub d’événements
description: Découvrez comment diffuser vos données de surveillance Azure vers un hub d’événements afin de les intégrer à un système SIEM ou à un outil d’analytique partenaire.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 535c74fd161019db28e691ff916ad03eaaf07c90
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260377"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Envoyer en streaming des données de supervision Azure vers un hub d’événements
Azure Monitor fournit une solution de supervision de pile complète pour les applications et services dans Azure, dans d’autres clouds et en local. En plus d’utiliser Azure Monitor pour analyser ces données et en tirer parti dans différents scénarios de supervision, vous aurez peut-être besoin de les envoyer à d’autres outils de supervision de votre environnement. Dans la plupart des cas, la méthode la plus efficace de streaming des données de supervision vers des outils externes consiste à utiliser [Azure Event Hubs](/azure/event-hubs/). Cet article fournit une brève description de la façon dont vous pouvez envoyer en streaming des données de supervision à partir de différentes sources vers un hub d’événements, ainsi que des liens vers des instructions détaillées.


## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Avant de configurer le streaming pour une source de données, vous avez besoin de [créer un espace de noms Event Hubs et un hub d’événements](../../event-hubs/event-hubs-create.md). Cet espace de noms et ce hub d’événements constituent la destination de toutes vos données de surveillance. Un espace de noms Event Hubs est un regroupement logique de hubs d’événements qui partagent une même stratégie d’accès, tout comme un compte de stockage contient des objets blob. Prenez en compte les informations suivantes sur l’espace de noms Event hubs et les hubs d’événements que vous utilisez pour le streaming des données de supervision :

* Le nombre d’unités de débit vous permet d’augmenter l’échelle de débit de vos hubs d’événements. En règle générale, une seule unité de débit est nécessaire. Si vous avez besoin de plus pour répondre à l’augmentation de l’utilisation de votre journal, vous pouvez augmenter manuellement le nombre d’unités de débit pour l’espace de noms ou activer l’inflation automatique.
* Le nombre de partitions vous permet de paralléliser la consommation sur un grand nombre de consommateurs. Une seule partition peut prendre en charge jusqu’à 20 Mbits/s, soit environ 20 000 messages par seconde. En fonction de l’outil qui consomme les données, la consommation simultanée de plusieurs partitions risque de ne pas être prise en charge. Si vous n’êtes pas certain du nombre de partitions à définir, il est raisonnable de commencer avec quatre partitions.
* Vous définissez la conservation des messages sur votre hub d’événements sur au moins 7 jours. Si l’outil consommateur est en panne pendant plus d’un jour, cela garantit qu’il pourra reprendre là où il s’est arrêté pour les événements des 7 derniers jours.
* Il est recommandé d’utiliser le groupe de consommateurs par défaut pour votre hub d’événements. Il n’est pas nécessaire de créer d’autres groupes de consommateurs ou d’utiliser un groupe de consommateurs distinct, sauf si deux outils doivent utiliser les mêmes données d’un même hub d’événements.
* Pour le journal d’activité Azure, vous pouvez choisir un espace de noms Event Hubs afin qu’Azure Monitor y crée un hub d’événements appelé _insights-logs-operational-logs_. Pour les autres types de journaux, vous pouvez soit choisir un hub d’événements existant, soit demander à Azure Monitor de créer un hub d’événements par catégorie de journal.
* En règle générale, les ports de sortie 5671 et 5672 doivent être ouverts sur l’ordinateur ou le réseau virtuel qui consomme les données du hub d’événements.


## <a name="monitoring-data-available"></a>Données de supervision disponibles
[Sources des données de supervision pour Azure Monitor](data-sources.md) décrit les différents niveaux de données des applications Azure et les types de données de supervision disponibles pour chacun d’eux. Le tableau suivant liste chacun de ces niveaux et une description de la façon dont ces données peuvent être envoyées en streaming vers un hub d’événements. Pour plus d’informations, suivez les liens fournis.

| Niveau | Données | Méthode |
|:---|:---|:---|
| [Locataire Azure](data-sources.md#azure-tenant) | Journaux d’audit Azure Active Directory | Configurez un paramètre de diagnostic de locataire sur votre locataire AAD. Consultez [Tutoriel : Streaming des journaux Azure Active Directory vers un hub d’événements Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) pour plus d’informations. |
| [Abonnement Azure](data-sources.md#azure-subscription) | Journaux d’activité | Créez un profil de journal pour exporter les événements du journal d’activité vers Event Hubs.  Consultez [Exporter le journal d’activité vers le stockage ou Azure Event Hubs](activity-log-export.md) pour plus d’informations. |
| [Ressources Azure](data-sources.md#azure-resources) | Métriques de plateforme<br>Journaux de diagnostic |Ces deux types de données sont envoyés à un hub d’événements à l’aide d’un paramètre de diagnostic des ressources. Consultez [Streaming des journaux Azure Diagnostics vers un hub d’événements](resource-logs-stream-event-hubs.md) pour plus d’informations. |
| [Système d’exploitation (invité)](data-sources.md#operating-system-guest) | Machines virtuelles Azure | Installez l’[extension Diagnostics Azure](diagnostics-extension-overview.md) sur les machines virtuelles Windows et Linux dans Azure. Consultez [Streaming des données Diagnostics Azure dans le chemin chaud à l’aide d’Event Hubs](diagnostics-extension-stream-event-hubs.md) pour plus d’informations sur les machines virtuelles Windows et [Utiliser l’extension Diagnostics Linux pour superviser des métriques et des journaux](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) pour plus d’informations sur les machines virtuelles Linux. |
| [Code d’application](data-sources.md#application-code) | Application Insights | Application Insights ne fournit pas de méthode directe pour envoyer en streaming des données vers des hubs d’événements. Vous pouvez [configurer l’exportation continue](../../azure-monitor/app/export-telemetry.md) des données Application Insights vers un compte de stockage, puis utiliser une application logique pour envoyer les données à un hub d’événements comme décrit dans [Streaming manuel avec une application logique](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Streaming manuel avec une application logique
Vous pouvez écrire dans le stockage Azure les données que vous ne pouvez pas envoyer directement vers un hub d’événements, puis utiliser une application logique déclenchée sur des critères d’heure qui [tire (pull) les données du stockage d’objets blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) et [les envoie (push) en tant que message à un hub d’événements](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="tools-with-azure-monitor-integration"></a>Outils avec intégration à Azure Monitor

Le routage de vos données de supervision vers un hub d’événements avec Azure Monitor vous permet d’intégrer facilement des systèmes SIEM et des outils de supervision. Voici quelques exemples d’outils avec intégration à Azure Monitor :

| Outil | Description |
|:---|:---|
|  IBM QRadar | Le module DSM Microsoft Azure et Microsoft Azure Event Hub Protocol sont disponibles au téléchargement sur le [site Web du support IBM](https://www.ibm.com/support). Pour en savoir plus sur l’intégration à Azure, consultez [Configuration du module DSM QRadar](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | [Le module complémentaire Azure Monitor pour Splunk](https://splunkbase.splunk.com/app/3534/) est un projet open source disponible dans Splunkbase. La documentation est disponible dans [Module complémentaire Azure Monitor pour Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Si vous ne pouvez pas installer de module complémentaire dans votre instance Splunk, par exemple, si vous utilisez un proxy ou exécutez sur un cloud Splunk, vous pouvez transférer ces événements au collecteur d’événements HTTP Splunk en utilisant [Azure Functions pour Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), déclenché par les nouveaux messages dans le hub d’événements. |
| sumologic | Les instructions de configuration de SumoLogic pour consommer les données d’un hub d’événements sont disponibles dans [Collecter des journaux pour une application d’audit Azure à partir d’un hub d’événements](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Le connecteur intelligent ArcSight Azure Event Hub est disponible dans la [collection de connecteurs intelligents ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Serveur syslog | Si vous voulez envoyer en streaming des données Azure Monitor directement vers un serveur syslog, vous pouvez utiliser une [solution basée sur une fonction Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).


## <a name="next-steps"></a>Étapes suivantes
* [Archiver le journal d’activité dans un compte de stockage](../../azure-monitor/platform/archive-activity-log.md)
* [Lire la présentation du journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Définir une alerte basée sur un événement de journal d’activité](../../azure-monitor/platform/alerts-log-webhook.md)


