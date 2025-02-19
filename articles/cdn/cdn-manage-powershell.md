---
title: Gérer Azure CDN avec PowerShell | Microsoft Docs
description: Apprenez à utiliser des applets de commande Azure PowerShell pour gérer Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 44274c2a46ce51e51d3d8f16d96a0b50c43a3aa1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593705"
---
# <a name="manage-azure-cdn-with-powershell"></a>Gérer Azure CDN avec PowerShell
PowerShell fournit une des méthodes les plus flexibles pour gérer vos points de terminaison et profils Azure CDN.  Vous pouvez utiliser PowerShell de manière interactive ou en écrivant des scripts pour automatiser les tâches de gestion.  Ce didacticiel illustre plusieurs des tâches les plus courantes que vous pouvez accomplir avec PowerShell pour gérer vos points de terminaison et profils Azure CDN.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour utiliser PowerShell pour gérer vos points de terminaison et profils Azure CDN, vous devez avoir installé le module Azure PowerShell.  Pour savoir comment installer Azure PowerShell et vous connecter à Azure à l’aide de l’applet de commande `Connect-AzAccount` , consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Vous devez vous connecter avec `Connect-AzAccount` avant de pouvoir exécuter les applets de commande Azure PowerShell.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Liste des applets de commande Azure CDN
Vous pouvez répertorier toutes les applets de commande Azure CDN à l’aide de l’applet de commande `Get-Command` .

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Obtenir de l’aide
Vous pouvez obtenir de l’aide pour ces applets de commande à l’aide de l’applet de commande `Get-Help` .  `Get-Help` fournit des informations sur l’utilisation et la syntaxe et présente des exemples.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Liste des profils Azure CDN existants
L’applet de commande `Get-AzCdnProfile` sans aucun paramètre récupère tous vos profils CDN existants.

```powershell
Get-AzCdnProfile
```

Cette sortie peut être transmise aux applets de commande pour l’énumération.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Vous pouvez également renvoyer un seul profil en spécifiant le groupe de ressources et le nom du profil.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Il est possible d’avoir plusieurs profils CDN portant le même nom, tant qu’ils se trouvent dans différents groupes de ressources.  Si vous omettez le paramètre `ResourceGroupName` , cela renvoie tous les profils dont le nom correspond.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Liste des points de terminaison CDN existants
`Get-AzCdnEndpoint` peut récupérer un point de terminaison individuel ou tous les points de terminaison d’un profil.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Création de profils et points de terminaison CDN
`New-AzCdnProfile` et `New-AzCdnEndpoint` sont utilisés pour créer des profils et des points de terminaison CDN. Les références (SKU) suivantes sont prises en charge :
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Vérification de la disponibilité du nom de point de terminaison
`Get-AzCdnEndpointNameAvailability` renvoie un objet qui indique si un nom de point de terminaison est disponible.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Ajout d’un domaine personnalisé
`New-AzCdnCustomDomain` ajoute un nom de domaine personnalisé à un point de terminaison existant.

> [!IMPORTANT]
> Vous devez configurer le CNAME avec votre fournisseur DNS comme décrit dans [Comment mapper un domaine personnalisé à un point de terminaison de réseau de distribution de contenu (CDN)](cdn-map-content-to-custom-domain.md).  Vous pouvez tester le mappage avant de modifier votre point de terminaison à l’aide de `Test-AzCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modification d’un point de terminaison
`Set-AzCdnEndpoint` modifie un point de terminaison existant.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Purge/pré-chargement des ressources CDN
`Unpublish-AzCdnEndpointContent` purge les ressources mises en cache, tandis que `Publish-AzCdnEndpointContent` précharge les ressources sur les points de terminaison pris en charge.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Démarrage/arrêt des points de terminaison CDN
`Start-AzCdnEndpoint` et `Stop-AzCdnEndpoint` peuvent être utilisés pour démarrer et arrêter des points de terminaison individuels ou des groupes de points de terminaison.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Suppression des ressources CDN
`Remove-AzCdnProfile` et `Remove-AzCdnEndpoint` peuvent être utilisés pour supprimer des profils et des points de terminaison.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Étapes suivantes
Apprenez à automatiser Azure CDN avec [.NET](cdn-app-dev-net.md) ou [Node.js](cdn-app-dev-node.md).

Pour en savoir plus sur les fonctionnalités CDN, consultez [Présentation du CDN](cdn-overview.md).

