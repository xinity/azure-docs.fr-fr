---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: e687fc071272e7a509b1dcfb0a417828a07aa7a9
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182212"
---
Cet article contient des recommandations de sécurité pour les Machines virtuelles Azure. Suivez ces recommandations pour respecter plus facilement les obligations de sécurité décrites dans notre modèle de responsabilité partagée. Ces recommandations vous aideront également à améliorer la sécurité globale de vos solutions d’application web. Pour plus d’informations sur les mesures prises par Microsoft pour assumer ses responsabilités de fournisseur de services, consultez [Responsabilités partagées pour le cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Certaines recommandations de cet article peuvent être mises en œuvre automatiquement par Azure Security Center. Azure Security Center est la première ligne de défense de vos ressources dans Azure. Il analyse régulièrement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Puis, il fournit des recommandations sur la façon de les corriger. Pour plus d’informations, consultez les [recommandations de sécurité dans Azure Security Center](../articles/security-center/security-center-recommendations.md).

Pour obtenir des informations générales sur Azure Security Center, consultez [Qu’est-ce qu’Azure Security Center ?](../articles/security-center/security-center-intro.md).

## <a name="recommendations"></a>Recommandations

| Category | Recommandation | Commentaires | Security Center |
|-|-|----|--|
| Généralités | Quand vous créez des images de machine virtuelle personnalisées, appliquez les dernières mises à jour. | Avant de créer des images, installez les dernières mises à jour du système d’exploitation et de toutes les applications qui feront partie de votre image.  | - |
| Généralités | veiller à ce que les machines virtuelles soient toujours à jour. | Vous pouvez utiliser la solution [Update Management](../articles/automation/automation-update-management.md) dans Azure Automation pour gérer les mises à jour de vos systèmes d’exploitation Windows et Linux dans Azure. | [Oui](../articles/security-center/security-center-apply-system-updates.md) |
| Généralités | Sauvegardez vos machines virtuelles. | La [sauvegarde Azure](../articles/backup/backup-overview.md) vous permet de protéger vos données d’application à des coûts d’exploitation minimes. Les erreurs rencontrées par les applications peuvent endommager vos données et les erreurs humaines peuvent introduire des bogues dans vos applications. La sauvegarde Azure protège vos machines virtuelles exécutant Windows et Linux. | - |
| Généralités | Utilisez plusieurs machines virtuelles pour plus de résilience et de disponibilité. | Si votre machine virtuelle exécute des applications qui doivent être hautement disponibles, utilisez plusieurs machines virtuelles ou des [groupes à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md). | - |
| Généralités | Adoptez une stratégie BCDR (continuité d’activité et reprise d’activité). | Azure Site Recovery vous permet de choisir parmi différentes options conçues pour la continuité de l’activité. Il prend en charge différents scénarios de réplication et de basculement. Pour plus d’informations, consultez [À propos de Site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |
| Gestion de l’identité et de l’accès | Centralisez l’authentification des machines virtuelles. | Vous pouvez centraliser l’authentification de vos machines virtuelles Windows et Linux à l’aide de l’[authentification Azure Active Directory](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Sécurité des données | Chiffrez les disques de système d’exploitation. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vous permet de chiffrer vos disques de machines virtuelles IaaS Windows et Linux. Sans les clés nécessaires, le contenu des disques chiffrés est illisible. Le chiffrement des disques protège les données stockées contre tout accès non autorisé qui serait rendu possible par la copie du disque.| [Oui](../articles/security-center/security-center-apply-disk-encryption.md) |
| Sécurité des données | Chiffrez les disques de données. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vous permet de chiffrer vos disques de machines virtuelles IaaS Windows et Linux. Sans les clés nécessaires, le contenu des disques chiffrés est illisible. Le chiffrement des disques protège les données stockées contre tout accès non autorisé qui serait rendu possible par la copie du disque.| -  |
| Sécurité des données | Limitez le nombre de logiciels installés. | Installez uniquement les logiciels nécessaires à l’application correcte de votre solution. Cette règle permet de réduire la surface d’attaque de votre solution. | - |
| Sécurité des données | Utilisez des logiciels antivirus ou anti-programmes malveillants. | Azure vous permet d’utiliser des logiciels anti-programmes malveillants provenant de fournisseurs de sécurité tels que Microsoft, Symantec, Trend Micro et Kaspersky. Ces logiciels permettent de protéger vos machines virtuelles contre les fichiers malveillants, les logiciels de publicité et d’autres menaces. Vous pouvez déployer Microsoft Antimalware en fonction des charges de travail de votre application. Utilisez une configuration de base sécurisée par défaut ou une configuration personnalisée avancée. Pour plus d’informations, consultez [Microsoft Antimalware pour les services cloud Azure et les machines virtuelles](../articles/security/azure-security-antimalware.md). | - |
| Sécurité des données | Stockez les secrets et les clés de façon sécurisée. | Simplifiez la gestion de vos secrets et de vos clés en fournissant aux propriétaires d’applications une option sécurisée et gérée de manière centralisée. Cette gestion réduit le risque de compromissions ou de fuites accidentelles. Azure Key Vault permet de stocker les clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Si vous avez besoin de stocker vos clés et vos secrets à l’aide de la norme FIPS 140.2 de niveau 3, vous pouvez utiliser [Azure Dedicated HSM](../articles/dedicated-hsm/overview.md). | - |
| Mise en réseau | Limitez l’accès aux ports de gestion. | Les attaquants analysent les plages d’adresses IP de cloud public pour détecter les ports de gestion ouverts et tenter des attaques « faciles », en utilisant des mots de passe courants ou en exploitant des vulnérabilités non corrigées connues. Vous pouvez utiliser l’[accès juste-à-temps (JAT) aux machines virtuelles](../articles/security-center/security-center-just-in-time.md) pour verrouiller le trafic entrant vers vos machines virtuelles Azure, ce qui réduit l’exposition aux attaques et facilite la connexion aux machines virtuelles en cas de besoin. | - |
| Mise en réseau | Limitez l’accès réseau. | Les groupes de sécurité réseau vous permettent de restreindre l’accès réseau et de contrôler le nombre de points de terminaison exposés. Pour plus d’informations, consultez [Créer, changer ou supprimer un groupe de sécurité réseau](../articles/virtual-network/manage-network-security-group.md). | - |
| Surveillance | Supervisez vos machines virtuelles. | Vous pouvez utiliser [Azure Monitor pour machines virtuelles](../articles/azure-monitor/insights/vminsights-overview.md) pour superviser l’état des machines virtuelles et des groupes de machines virtuelles identiques Azure. Une machine virtuelle insuffisamment performante peut entraîner une interruption de service, réduisant ainsi la disponibilité. | - |

## <a name="next-steps"></a>Étapes suivantes

Consultez votre fournisseur d’applications pour en savoir plus sur les exigences de sécurité supplémentaires. Pour plus d’informations sur le développement d’applications sécurisées, consultez [Documentation sur le développement sécurisé](../articles/security/fundamentals/abstract-develop-secure-apps.md).
