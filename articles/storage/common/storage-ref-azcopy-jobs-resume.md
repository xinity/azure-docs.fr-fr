---
title: azcopy jobs resume | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy jobs resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9e8dbbd40259c7a71f252d0d6e93dd6f135973de
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513542"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Reprend la tâche avec l’ID de tâche donné.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Options

|Option|Description|
|--|--|
|--destination-sas (chaîne)|SAP de destination de la destination pour le JobId donné.|
|--exclude (chaîne)|Filtre : Exclut les transferts ayant échoué lors de la reprise du travail. Les fichiers doivent être séparés par un point-virgule (« ; »).|
|-h, --help|Affiche l’aide de la commande resume.|
|--include (chaîne)|Filtre : Inclut uniquement les transferts ayant échoué lors de la reprise du travail. Les fichiers doivent être séparés par un point-virgule (« ; »).|
|--source-sas (chaîne) |SAP source de la source pour le JobId donné.|

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy jobs](storage-ref-azcopy-jobs.md)
