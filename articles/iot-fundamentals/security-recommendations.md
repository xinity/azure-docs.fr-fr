---
title: Recommandations de sécurité pour Azure IoT | Microsoft Docs
description: Cet article résume les étapes supplémentaires pour assurer la sécurité de votre solution Azure IoT Hub.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 09/25/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 9c8891eca87444c2be43294f208ad5257ffce889
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177184"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Recommandations de sécurité pour le déploiement d’Azure IoT

Cet article contient des recommandations de sécurité pour IoT. Mettez en œuvre ces recommandations pour répondre à vos obligations de sécurité comme décrit dans notre modèle de responsabilité partagée. Pour plus d’informations sur les mesures prises par Microsoft pour répondre à ses responsabilités de fournisseur de services, lisez [Responsabilités partagées pour le cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Certaines recommandations contenues dans cet article peuvent être supervisées automatiquement par Azure Security Center. Azure Security Center est la première ligne de défense dans la protection de vos ressources Azure. Il analyse régulièrement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Il fournit ensuite des recommandations sur la façon de les corriger.

- Pour plus d’informations sur les recommandations Azure Security Center, consultez [Recommandations de sécurité dans Azure Security Center](../security-center/security-center-recommendations.md).
- Pour plus d’informations sur Azure Security Center, consultez [Qu’est-ce qu’Azure Security Center ?](../security-center/security-center-intro.md)

## <a name="recommendations"></a>Recommandations

| Category | Recommandation | Commentaires | Pris en charge par ASC |
|-|-|----|--|
| Généralités | Rester à jour | Utilisez les dernières versions des plateformes, langages de programmation, protocoles et infrastructures pris en charge. | - |
| Généralités | Protéger les clés d’authentification | Sécurisez physiquement les ID de périphérique et leurs clés d’authentification après le déploiement. Cela évitera qu’un appareil malveillant se fasse passer pour un appareil inscrit. | - |
| Généralités | Utiliser les Kits de développement logiciel (SDK) de services lorsque cela est possible | Les SDK pour appareils implémentent diverses fonctionnalités de sécurité, telles que le chiffrement, l’authentification, etc. pour vous aider à développer une application d’appareil robuste et sécurisée. Pour en savoir plus, consultez [Comprendre et utiliser les kits Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks). | - |
| Gestion de l’identité et de l’accès | Définir le contrôle d’accès pour le hub | [Comprenez et définissez le type d’accès](iot-security-deployment.md#securing-the-cloud) que chaque composant aura dans votre solution IoT Hub, selon sa fonctionnalité. Les autorisations admises sont *Lecture du Registre*, *RegistryReadWrite*, *ServiceConnect* et *DeviceConnect*. Les [stratégies d’accès partagé par défaut dans votre hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) peuvent également aider à définir les autorisations pour chaque composant en fonction de son rôle. | - |
| Gestion de l’identité et de l’accès | Définir le contrôle d’accès pour les services principaux | Les données reçues par votre solution IoT Hub peuvent être utilisées par d’autres services Azure tels que [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/) et le [stockage d’objets Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Assurez-vous de comprendre et d’autoriser les autorisations d’accès appropriées comme indiqué pour ces services. | - |
| Protection des données | Sécuriser l’authentification des appareils | Assurez une communication sécurisée entre vos appareils et votre IoT Hub, en utilisant soit [une clé d’identité ou un jeton de sécurité unique](iot-security-deployment.md#iot-hub-security-tokens), soit [un certificat X.509 sur l’appareil](iot-security-deployment.md#x509-certificate-based-device-authentication) pour chaque appareil. Utilisez la méthode appropriée pour [ utiliser les jetons de sécurité basés sur le protocole choisi (MQTT, AMQP ou HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Protection des données | Sécuriser la communication des appareils | IoT Hub sécurise la connexion aux appareils à l’aide du standard TLS (Transport Layer Security) pour les versions 1.2 et 1.0. Utilisez [TLS 1.2](https://tools.ietf.org/html/rfc5246) afin de garantir une sécurité maximale. | - |
| Protection des données | Sécuriser les communications des services | IoT Hub fournit des points de terminaison pour se connecter à des services principaux tels que [Stockage Azure](/azure/storage/) ou [Event Hubs](/azure/event-hubs) en utilisant uniquement le protocole TLS, et aucun point de terminaison n’est exposé sur un canal non chiffré. Une fois que ces données atteignent ces services principaux à des fins de stockage ou d’analyse, assurez-vous d’utiliser des méthodes de sécurité et de chiffrement appropriées pour ce service et de protéger les informations sensibles sur le serveur principal. | - |
| Mise en réseau | Protéger l’accès à vos appareils | Maintenez le nombre de ports physiques au strict minimum sur vos appareils pour éviter les accès indésirables. De plus, générez des mécanismes pour prévenir ou détecter toute falsification physique de l’appareil. Pour plus de détails, consultez [IoT : meilleures pratiques en matière de sécurité](iot-security-best-practices.md). | - |
| Mise en réseau | Générer le matériel sécurisé | Intégrez des fonctionnalités de sécurité telles que le stockage chiffré ou le module TPM (Trusted Platform Module) pour mieux sécuriser les appareils et l’infrastructure. Tenez le système d’exploitation et les pilotes à niveau avec les dernières versions et, si l’espace le permet, installez des fonctionnalités de protection antivirus et anti-programme malveillant. Lisez [Architecture de la sécurité IoT](iot-security-architecture.md) pour comprendre comment cela peut contribuer à atténuer plusieurs menaces de sécurité. | - |
| Surveillance | Surveiller l’accès non autorisé à vos appareils |  Utiliser la fonctionnalité de journalisation du système d’exploitation de votre appareil pour surveiller les failles de sécurité ou les altérations physiques de l’appareil ou de ses ports. | - |
| Surveillance | Surveiller votre solution IoT à partir du cloud | Surveillez l’intégrité globale de votre solution IoT Hub à l’aide des [métriques dans Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Surveillance | Configurer les diagnostics | Regardez attentivement vos opérations en consignant les événements dans votre solution, puis en envoyant les journaux de diagnostic à Azure Monitor pour obtenir une visibilité sur les performances. Pour plus d’informations, lisez [Surveiller et diagnostiquer les problèmes dans votre hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health). | - |

## <a name="next-steps"></a>Étapes suivantes

Pour les scénarios avancés impliquant Azure IoT, il se peut que vous deviez prendre en compte des exigences de sécurité supplémentaires. Consultez [Architecture de sécurité IoT](iot-security-architecture.md) pour obtenir des instructions.

