---
title: Déployer des hôtes dédiés Azure à l’aide d’Azure PowerShell | Microsoft Docs
description: Déployez des machines virtuelles sur des hôtes dédiés à l’aide d’Azure PowerShell.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 1dcea4f56d778b0e6320634286a25d478c78a5bc
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261708"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Aperçu : Déployez des machines virtuelles sur des hôtes dédiés à l’aide d’Azure PowerShell

Cet article vous guide dans la création d’un [hôte dédié](dedicated-hosts.md) Azure pour héberger vos machines virtuelles. 

Assurez-vous que vous avez installé Azure PowerShell version 2.4.2 ou ultérieure, et que vous êtes connecté à un compte Azure avec `Connect-AzAccount`. Pour installer la version 2.4.2, ouvrez une invite de commandes PowerShell et tapez :

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrerelease
```

Vous aurez besoin de la version 1.6.0 du module PowerShellGet au minimum pour activer la fonctionnalité d’aperçu du module dans PowerShell. Cette fonctionnalité est automatiquement intégrée dans les versions les plus récentes de PowerShell Core, mais pour les anciennes versions de PowerShell, vous pouvez exécuter la commande suivante pour mettre l’outil à jour vers la dernière version :

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> Les hôtes dédiés Azure sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitations connues de la préversion**
> - Actuellement, les hôtes dédiés ne prennent pas en charge les groupes de machines virtuelles identiques.
> - La préversion initiale prend en charge les séries de machines virtuelles suivantes : DSv3 et ESv3. 



## <a name="create-a-host-group"></a>Créer un groupe hôte

Un **groupe hôte** est une ressource qui représente une collection d’hôtes dédiés. Vous créez un groupe hôte dans une région et une zone de disponibilité, et lui ajoutez des hôtes. Lors de la planification de la haute disponibilité, vous pouvez accéder à des options supplémentaires. Vous pouvez utiliser l’une des options suivantes, ou les deux, avec vos hôtes dédiés : 
- Application sur plusieurs zones de disponibilité. Dans ce cas, vous devez disposer d’un groupe hôte dans chacune des zones que vous souhaitez utiliser.
- Application sur plusieurs domaines d’erreur mappés à des racks physiques. 
 
Dans les deux cas, vous devez fournir le nombre de domaines d’erreur pour votre groupe hôte. Si vous ne souhaitez pas appliquer plusieurs domaines d’erreur dans votre groupe, utilisez un seul domaine d’erreur. 

Vous pouvez également choisir d’utiliser des zones de disponibilité et des domaines d’erreur. Cet exemple crée un groupe hôte dans la zone 1, avec 2 domaines d’erreur. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Créer un hôte

À présent, créons un hôte dédié dans le groupe hôte. En plus d’un nom pour l’hôte, vous devez fournir la référence SKU pour l’hôte. La référence SKU de l’hôte capture la série de machines virtuelles prises en charge, ainsi que la génération du matériel pour l’hôte dédié.  Lors de la préversion, nous allons prendre en charge les valeurs de SKU d’hôte suivantes : DSv3_Type1 et ESv3_Type1.


Pour en savoir plus sur les références SKU et la tarification des hôtes, consultez la section relative à la [tarification des hôtes dédiés Azure](https://aka.ms/ADHPricing).

Si vous définissez un nombre de domaines d’erreur pour votre groupe hôte, vous êtes invité à spécifier le domaine d’erreur de votre hôte. Dans cet exemple, nous définissons le domaine d’erreur de l’hôte sur 1.


```powershell
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Créer une machine virtuelle

Créez une machine virtuelle sur l’hôte dédié. 

Si vous avez spécifié une zone de disponibilité lors de la création de votre groupe hôte, vous devez utiliser la même zone quand vous créez la machine virtuelle. Pour cet exemple, étant donné que notre groupe hôte se trouve dans la zone 1, nous devons créer la machine virtuelle dans la zone 1.  


```powershell
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Toute machine virtuelle créée sur un hôte qui ne dispose pas d’un nombre de ressources suffisant présente un état ÉCHEC. 

## <a name="check-the-status-of-the-host"></a>Vérifier l’état de l’hôte

Vous pouvez vérifier l’état d’intégrité de l’hôte et le nombre de machines virtuelles que vous pouvez encore déployer sur l’hôte à l’aide de la commande [GetAzHost](/powershell/module/az.compute/get-azhost), avec le paramètre `-InstanceView`.

```
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Le résultat ressemble à ce qui suit :

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="clean-up"></a>Nettoyer

Vous êtes facturé pour vos hôtes dédiés, même si aucune machine virtuelle n’est déployée. Supprimez tous les hôtes non utilisés pour réduire les coûts.  

Vous pouvez supprimer un hôte uniquement lorsqu’il n’est plus utilisé par aucune machine virtuelle. Supprimez les machines virtuelles avec la commande [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Cela fait, vous pouvez supprimer l’hôte avec la commande [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Une fois que vous avez supprimé tous vos hôtes, utilisez la commande [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) pour supprimer le groupe hôte. 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Vous pouvez également supprimer l’intégralité du groupe de ressources via une seule commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Cette opération supprime toutes les ressources créées dans le groupe, y compris l’ensemble des machines virtuelles, des hôtes et des groupes hôtes.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Étapes suivantes

- Un exemple de modèle, trouvé [ici](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.

- Le [Portail Microsoft Azure](dedicated-hosts-portal.md) permet également de déployer des hôtes dédiés.
