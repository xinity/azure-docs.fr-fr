---
title: Exemples Azure CLI pour Load Balancer
titlesuffix: Azure Load Balancer
description: Exemples d’interface de ligne de commande Azure
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: 9567dc1425ea74a9f46912532c5b8e4e4afc2fdb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275694"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Exemples Azure CLI pour Load Balancer

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|-|-|
| [Équilibrer le trafic sur les machines virtuelles pour la haute disponibilité](./scripts/load-balancer-linux-cli-sample-nlb.md) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Équilibrer la charge de machines virtuelles entre des zones de disponibilité](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Crée trois machines virtuelles dans différentes zones de disponibilité au sein d’une région et un équilibreur de charge standard avec une adresse IP frontend redondante interzone. Cette configuration d’équilibreur de charge vous permet de mieux protéger vos applications et vos données dans l’éventualité (peu probable) d’une défaillance ou d’une perte d’un centre de données entier. |
|[Équilibrer la charge de machines virtuelles dans une zone de disponibilité spécifique](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Crée trois machines virtuelles, un équilibreur de charge standard avec une adresse IP frontend zonale qui permet d’aligner le chemin de données et les ressources dans une zone unique d’une région donnée.|
| [Équilibrer la charge de plusieurs sites web sur des machines virtuelles](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Crée deux machines virtuelles avec plusieurs configurations IP, jointes à un groupe à haute disponibilité Azure, accessible via Azure Load Balancer. |
| | |

