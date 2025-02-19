---
title: azcopy remove | Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fc23afb9a407fc2e6689c5c8766cb4beba868269
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513430"
---
# <a name="azcopy-remove"></a>azcopy remove

Supprimez des objets BLOB ou des fichiers d’un compte de stockage Azure.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Exemples

Supprimer un objet blob avec l’authentification SAP :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Supprimer l’intégralité d’un répertoire virtuel avec l’authentification SAP :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Supprimer uniquement les principaux objets blob d’un répertoire virtuel, mais pas ses sous-répertoires :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Supprimer un sous-ensemble d’objets blob dans un répertoire virtuel (par exemple, uniquement les fichiers .jpg et PDF, ou uniquement les objets blob dont le nom est « exactName ») :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Supprimer l’intégralité d’un répertoire virtuel, mais exclure certains objets blob (par exemple, tous ceux dont le nom commence par « foo » ou se termine par « bar ») :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Supprimez des objets BLOB et des répertoires virtuels spécifiques en plaçant leurs chemins d’accès relatifs (NON codés URL) dans un fichier :

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Supprimez un seul fichier d’un compte de stockage d’objets BLOB qui a un espace de noms hiérarchique (inclure/exclure non pris en charge).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Supprimez un seul répertoire d’un compte de stockage d’objets BLOB qui a un espace de noms hiérarchique (inclure/exclure non pris en charge) :

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Options

Chaîne **--exclude-path**      Exclut ces chemins d’accès lors de la suppression. Cette option ne prend pas en charge les caractères génériques (*). Vérifie le préfixe du chemin d’accès relatif. Par exemple : myFolder;myFolder/subDirName/file.pdf.

Chaîne **--exclude-pattern** Exclut les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName

**-h, --help**                     Aide pour la suppression

Chaîne **--include-path**      Inclut uniquement ces chemins lors de la suppression. Cette option ne prend pas en charge les caractères génériques (*). Vérifie le préfixe du chemin d’accès relatif. Par exemple : myFolder;myFolder/subDirName/file.pdf

Chaîne **--include-pattern** Inclut uniquement les fichiers dont le nom correspond à la liste de caractères génériques. Par exemple : *.jpg;* .pdf;exactName

Chaîne **--list-of-files**    Définit l’emplacement d’un fichier qui contient la liste des fichiers et répertoires à supprimer. Les chemins d’accès relatifs doivent être délimités par des sauts de ligne, et les chemins d’accès ne doivent pas être codés URL.

Chaîne **--log-level**         Définit la verbosité du journal pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes/réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie) (« INFO » par défaut) (« INFO » par défaut)

**--recursive**                Examine les sous-répertoires de manière récursive lors de la synchronisation des répertoires.

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

|Option|Description|
|---|---|
|--cap-mbps uint32|Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.|
|--output-type (chaîne)|Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ».|

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
