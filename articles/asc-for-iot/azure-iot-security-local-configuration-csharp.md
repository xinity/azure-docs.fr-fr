---
title: Compréhension d’Azure Security Center pour le fichier de configuration local de l’agent pour C# | Microsoft Docs
description: Découvrez plus d’informations sur Azure Security Center pour le fichier de configuration local de l’agent pour C#.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cc7b9f0b6e537ca3bdcbb82a357b2f2b9451fab0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600336"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Compréhension du fichier de configuration local (agent C#)


L’agent de sécurité Azure Security Center pour IoT utilise des configurations à partir d’un fichier de configuration local.

L’agent de sécurité lit le fichier de configuration une seule fois, au démarrage de l’agent. Les configurations trouvées dans le fichier de configuration local contiennent la configuration de l’authentification et d’autres configurations associées à l’agent.

L’agent de sécurité C# utilise plusieurs fichiers de configuration :

- **General. config** : configurations associées à l’agent.
- **Authentication.config** : configuration associée à l’authentification (y compris les détails de cette dernière).
- **SecurityIotInterface.config** : configurations liées à l’IoT.

Les fichiers de configuration contiennent la configuration par défaut. La configuration de l’authentification est remplie lors de l’installation de l’agent, et les modifications apportées au fichier de configuration sont appliquées au redémarrage de l’agent. 

## <a name="configuration-file-location"></a>Emplacement du fichier de configuration
Pour Linux :
- Les fichiers de configuration du système d’exploitation sont situés ici : `/var/ASCIoTAgent`.

Pour Windows :
- Les fichiers de configuration du système d’exploitation se trouvent dans le répertoire de l’agent de sécurité. 

### <a name="generalconfig-configurations"></a>Configurations du fichier General.config

| Nom de la configuration | Valeurs possibles | Détails | 
|:-----------|:---------------|:--------|
| agentId | GUID | Identificateur unique de l’agent. |
| readRemoteConfigurationTimeout | TimeSpan | Intervalle d’extraction de la configuration distante à partir de IoT Hub. Si l’agent ne parvient pas à extraire la configuration dans le délai spécifié, l’opération arrive à expiration.|
| schedulerInterval | TimeSpan | Intervalle du planificateur interne. |
| schedulerInterval | TimeSpan | Intervalle du rôle de travail du producteur d’événements. |
| consumerInterval | TimeSpan | Intervalle du rôle de travail du consommateur d'événements. |
| highPriorityQueueSizePercentage | 0 < nombre < 1 | Partie du cache total dédiée aux messages à priorité élevée. |
| logLevel | "Off", "Fatal", "Error", "Warning", "Information", "Debug"  | Les messages de journal équivalant à ce niveau de gravité sont enregistrés dans la console de débogage (Syslog dans Linux). |
| fileLogLevel |  "Off", "Fatal", "Error", "Warning", "Information", "Debug"| Les messages de journal équivalant à ce niveau de gravité sont enregistrés dans un fichier (Syslog dans Linux). |
| diagnosticVerbosityLevel | "None", "Some", "All" | Niveau de détail des événements de diagnostic. « None » : les événements de diagnostic ne sont pas envoyés ; « Some » : certains événements de diagnostic avec un niveau d’importance élevé sont envoyés ; « All » : tous les journaux sont également envoyés en tant qu’événements de diagnostic. |
| logFilePath | Chemin d’accès au fichier | Si fileLogLevel > Off, les journaux sont écrits dans ce fichier. |
| defaultEventPriority | "High", "Low", "Off" | Priorité par défaut des événements. |

### <a name="generalconfig-example"></a>Exemple de fichier General.config
```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Nom de la configuration | Valeurs possibles | Détails | 
|:-----------|:---------------|:--------|
| moduleName | string | Nom de l’identité du module de sécurité. Ce nom doit correspondre à celui de l’identité du module dans l’appareil. |
| deviceId | string | ID de l’appareil (comme inscrit dans Azure IoT Hub). || schedulerInterval | Chaîne TimeSpan | Intervalle du planificateur interne. |
| gatewayHostname | string | Nom d’hôte du hub Azure Iot Hub. En général, il s’agit de <mon-hub>.azure-devices.net |
| filePath | Chaîne - Chemin d’accès au fichier | Chemin d’accès au fichier qui contient le secret d’authentification.|
| Type | "SymmetricKey", "SelfSignedCertificate" | Secret de l’utilisateur pour l’authentification. Choisissez *SymmetricKey* si le secret de l’utilisateur est une clé symétrique, ou *SelfSignedCertificate* si le secret est un certificat auto-signé. |
| identité | "DPS", "Module", "Device" | Identité d’authentification : « DPS » si l’authentification est effectuée via DPS, « Module » si elle est effectuée via les informations d’identification du module de sécurité ou « Device » si elle est effectuée avec les informations d’identification de l’appareil.
| certificateLocationKind |  "LocalFile", "Store" | « LocalFile » si le certificat est stocké dans un fichier, « store » s’il se trouve dans un magasin de certificats. |
| idScope | string | Étendue de l’ID de DPS. |
| registrationId | string  | ID d’inscription de l’appareil DPS. |
|

### <a name="authenticationconfig-example"></a>Exemple de fichier Authentication.config
```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```
### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Nom de la configuration | Valeurs possibles | Détails | 
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | Type de transport IoT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>Exemple de fichier SecurityIotInterface. config
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Étapes suivantes
- Lire la [Vue d’ensemble](overview.md) du service Microsoft Azure Security Center pour IoT
- En savoir plus sur [l’architecture](architecture.md) d’Azure Security Center pour IoT
- Activer le [service](quickstart-onboard-iot-hub.md) Azure Security Center pour IoT
- Lire la [FAQ](resources-frequently-asked-questions.md) du service Azure Security Center pour IoT
- Découvrir comment accéder aux [données de sécurité brutes](how-to-security-data-access.md)
- Comprendre les [recommandations](concept-recommendations.md)
- Comprendre les [alertes de sécurité](concept-security-alerts.md)