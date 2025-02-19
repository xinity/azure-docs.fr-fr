---
title: Fichier Include
description: Fichier Include
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: f85605610727ef2c1e1987b7ef93a41ce2417a25
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "69626327"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Machines virtuelles par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> par région. |25 000 par région. |
| Nombre total de cœurs de machine virtuelle par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région. | Contactez le support technique. |
| Machine virtuelle par série, telle que Dv2, et F, cœurs par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région. | Contactez le support technique. |
| [Coadministrateurs](../articles/billing-add-change-azure-subscription-administrator.md) par abonnement |Illimité. |Illimité. |
| [Comptes de stockage](../articles/storage/common/storage-quickstart-create-account.md) par région et par abonnement |250 |250 |
| [Groupes de ressources](../articles/azure-resource-manager/resource-group-overview.md) par abonnement |980 |980 |
| [Groupes à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) par abonnement |2 000 par région. |2 000 par région. |
| Taille de la requête d’API Resource Manager |4 194 304 octets. |4 194 304 octets. |
| Balises par abonnement<sup>2</sup> |Illimité. |Illimité. |
| Calculs de balise unique par abonnement<sup>2</sup> | 10 000 | 10 000 |
| [Services cloud](../articles/cloud-services/cloud-services-choose-me.md) par abonnement |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Groupes d'affinités](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) par abonnement |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Déploiements de niveau d’abonnement](../articles/azure-resource-manager/deploy-to-subscription.md) par emplacement | 800<sup>4</sup> | 800 |

<sup>1</sup>Les limites par défaut varient selon le type de catégorie d’offre, comme la version d’évaluation gratuite et le paiement à l’utilisation, et selon la série, par exemple dv2, F et G. Par exemple, la valeur par défaut pour les abonnements Accord Entreprise est 350.

<sup>2</sup>Vous pouvez appliquer un nombre illimité de balises par abonnement. Le nombre de balises par ressource ou groupe de ressources est limité à 50. Le Gestionnaire des ressources retourne une [liste comportant le nom et les valeurs de balise unique](/rest/api/resources/tags) dans l’abonnement uniquement lorsque le nombre de balises est inférieur ou égal à 10 000. Vous pouvez toujours trouver une ressource par balise lorsque le nombre de balises est supérieur à 10 000.  

<sup>3</sup>Ces fonctionnalités ne sont plus nécessaires avec les groupes de ressources Azure et le Gestionnaire des ressources.

<sup>4</sup>Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles dans l’historique. Pour supprimer les déploiements au niveau de l’abonnement, utilisez [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Les cœurs de machines virtuelles sont soumis à une limite totale régionale. Ils ont également une limite pour les séries par taille régionales, telles que Dv2 et F. Ces limites sont appliquées séparément. Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région USA Est, de 30 pour la gamme A et de 30 pour la gamme D. Cet abonnement peut déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total. Par exemple, 10 machines virtuelles A1 et 20 machines virtuelles D1.  
> <!-- -->
> 
> 

