---
title: Options de stockage pour conteneurs de profil FSLogix dans Windows Virtual Desktop - Azure
description: Options pour le stockage de votre profil FSLogix de Windows Virtual Desktop dans le stockage Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
ms.openlocfilehash: fc869bc0c52a54044cbc095cd20f0395e590c852
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332815"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Options de stockage pour conteneurs de profil FSLogix dans Windows Virtual Desktop

Azure propose plusieurs solutions de stockage que vous pouvez utiliser pour stocker votre conteneur de profil FSLogix. Cet article compare les solutions de stockage que le stockage Azure propose pour les profils utilisateur des conteneurs de profil FSLogix de Windows Virtual Desktop.

Windows Virtual Desktop propose des conteneurs de profil FSLogix en tant que solution recommandée de profil utilisateur. FSLogix est conçu pour l’itinérance des profils dans des environnements informatiques à distance, comme Windows Virtual Desktop. Lors de la connexion, ce conteneur est attaché dynamiquement à l’environnement informatique à l’aide d’un disque dur virtuel (VHD) et d’un disque dur virtuel Hyper-V (VHDX) pris en charge en mode natif. Le profil utilisateur est immédiatement disponible et apparaît dans le système exactement comme un profil utilisateur natif.

Les tableaux suivants comparent les solutions de stockage que le stockage Azure propose pour les profils utilisateur des conteneurs de profil FSLogix de Windows Virtual Desktop.

## <a name="azure-platform-details"></a>Détails de la plateforme Azure

|Caractéristiques|Azure Files|Azure NetApp Files|Espaces de stockage direct|
|--------|-----------|------------------|---------------------|
|Service de plateforme|Oui, solution Azure native|Oui, solution Azure native|Non, auto-managé|
|Disponibilité régionale|Toutes les régions|[Sélectionner les régions](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Toutes les régions|
|Redondance|Localement redondant/redondant interzone/géoredondant|Localement redondant|Localement redondant/redondant interzone/géoredondant|
|Niveaux et performances|standard<br>Premium<br>Jusqu’à 100 000 IOPS par partage avec 5 Gbits/s par partage avec environ 3 ms de latence|standard<br>Premium<br>Ultra<br>Jusqu’à 320 000 (16K) IOPS avec 4,5 Gbits/s par volume avec environ 1 ms de latence|Disque dur Standard : limites par disque jusqu’à 500 IOPS<br>Disque SSD Standard : limites par disque jusqu’à 4 000 IOPS<br>Disque SSD Premium : limites par disque jusqu’à 20 000 IOPS<br>Nous vous recommandons d’utiliser des disques Premium pour espaces de stockage direct|
|Capacité|100 Tio par partage|100 Tio par volume, jusqu’à 12,5 Pio par abonnement|Maximum de 32 Tio par disque|
|Infrastructure requise|Taille de partage minimale de 1 Gio|Pool de capacité minimal de 4 Tio, taille de volume min. de 100 Gio|Deux machines virtuelles sur Azure IaaS (+ témoin de cloud) ou au moins trois machines virtuelles sans coûts pour les disques|
|Protocoles|SMB 2.1/3. et REST|NFSv3, NFSv4.1 (préversion), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Détails de la gestion Azure

|Caractéristiques|Azure Files|Azure NetApp Files|Espaces de stockage direct|
|--------|-----------|------------------|---------------------|
|Access|Cloud, local et hybride (Azure File Sync)|Cloud, local (via ExpressRoute)|Cloud, local|
|Sauvegarde|Intégration des instantanés de sauvegarde Azure|Instantanés Azure NetApp Files|Intégration des instantanés de sauvegarde Azure|
|Sécurité et conformité|[Tous les certificats pris en charge par Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|Terminé selon ISO|[Tous les certificats pris en charge par Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Intégration d’Azure Active Directory|Azure Active Directory et Azure Active Directory Domain Services|[Azure Active Directory Domain Services et Active Directory natif](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Prise en charge d’Active Directory natif ou d’Azure Active Directory Domain Services uniquement|

Une fois que vous avez choisi votre méthode de stockage, consultez [Tarification de Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) pour obtenir des informations sur nos plans de tarification.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les conteneurs de profil FSLogix, les disques de profil utilisateur et d’autres technologies de profil utilisateur, consultez le tableau dans [Conteneurs de profil FSLogix et fichiers Azure](fslogix-containers-azure-files.md).

Si vous êtes prêt à créer vos propres conteneurs de profil FSLogix, prenez en main l’un de ces tutoriels :

- [Bien démarrer avec les conteneurs de profils FSLogix sur Azure Files dans Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Créer un conteneur de profils FSLogix pour un pool d’hôtes à l’aide d’Azure NetApp Files](create-fslogix-profile-container.md)
- Les instructions de l’article [Déployer un serveur de fichiers scale-out avec des espaces de stockage direct à deux nœuds pour le stockage UPD dans Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) s’appliquent également lorsque vous utilisez un conteneur de profil FSLogix à la place d’un disque de profil utilisateur

Vous pouvez également commencer par le tout début et configurer votre propre solution Windows Virtual Desktop dans [Créer un locataire dans Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
