---
title: Exemple de script Azure PowerShell - Créer une sauvegarde planifiée pour une application web | Microsoft Docs
description: Exemple de script Azure PowerShell - Créer une sauvegarde planifiée pour une application web
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b0223d093cc82becf66a903ea80c03c8435ea84c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098395"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>Créer une sauvegarde planifiée pour une application web à l'aide de PowerShell

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis crée une sauvegarde planifiée pour celle-ci. 

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure. 

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Crée un compte de stockage. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Crée un conteneur de stockage Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Génère un jeton SAS pour un conteneur de stockage Azure. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Crée un plan App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crée une application web. |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | Modifie la configuration de sauvegarde pour l’application web. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtient une liste des sauvegardes d’une application web. |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | Obtient la configuration de sauvegarde pour l’application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../samples-powershell.md) (Exemples Azure PowerShell).
