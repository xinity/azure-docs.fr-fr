---
title: Intégration de Git pour Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment Azure Machine Learning intègre un dépôt Git local.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377374"
---
# <a name="git-integration-for-azure-machine-learning"></a>Intégration de Git pour Azure Machine Learning

[Git ](https://git-scm.com/) est un système de gestion de versions connue qui vous permet de partager vos projets et de collaborer. Lors de la soumission d’un travail d’entraînement à Azure Machine Learning, si les fichiers d’entraînement sont stockés dans un dépôt Git local, les informations sur le dépôt font l’objet d’un suivi dans le cadre du processus d’entraînement.

Étant donné qu’Azure Machine Learning effectue le suivi des informations à partir d’un dépôt Git local, il n’est lié à aucun dépôt central spécifique. Votre dépôt peut être cloné à partir de GitHub, GitLab, Bitbucket, Azure DevOps ou de tout autre service compatible Git.

## <a name="how-does-git-integration-work"></a>Fonctionnement de l’intégration de Git

Lorsque vous soumettez une exécution d’entraînement à partir du kit SDK Python ou de l’interface CLI de Machine Learning, les fichiers nécessaires à l’entraînement du modèle sont chargés dans votre espace de travail. Si la commande `git` est disponible dans votre environnement de développement, le processus de chargement l’utilise pour vérifier si les fichiers sont stockés dans un dépôt Git. Si tel est le cas, les informations de votre dépôt Git sont également chargées lors de l’exécution d’entraînement. Ces informations sont stockées dans les propriétés suivantes pour l’exécution d’entraînement :

| Propriété | Description |
| ----- | ----- |
| `azureml.git.repository_uri` | URI à partir duquel votre dépôt a été cloné. |
| `mlflow.source.git.repoURL` | URI à partir duquel votre dépôt a été cloné. |
| `azureml.git.branch` | Branche active lorsque l’exécution a été envoyée. |
| `mlflow.source.git.branch` | Branche active lorsque l’exécution a été envoyée. |
| `azureml.git.commit` | Hachage de validation du code qui a été envoyé pour l’exécution. |
| `mlflow.source.git.commit` | Hachage de validation du code qui a été envoyé pour l’exécution. |
| `azureml.git.dirty` | `True`, si l’intégrité de la validation est compromise ; sinon, `false`. |

Ces informations sont envoyées pour les exécutions qui utilisent un estimateur, un pipeline Machine Learning ou une exécution de script.

Si vos fichiers d’entraînement ne se trouvent pas dans un dépôt Git dans votre environnement de développement, ou si la commande `git` n’est pas disponible, aucune information liée à Git ne fait l’objet d’un suivi.

## <a name="view-the-logged-information"></a>Voir les informations journalisées

Les informations Git sont stockées dans les propriétés d’une exécution d’entraînement. Vous pouvez voir ces informations en utilisant le portail Azure, le kit SDK Python et l’interface CLI. 

### <a name="azure-portal"></a>Portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail.
1. Sélectionnez __Expériences__, puis sélectionnez l’une de vos expériences.
1. Sélectionnez l’une des exécutions dans la colonne __NUMÉRO D’EXÉCUTION__.
1. Sélectionnez __Journaux__, puis développez les entrées __logs__ et __azureml__. Sélectionnez le lien qui commence par __###\_azure__.

Les informations journalisées contiennent du texte similaire au code JSON suivant :

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Kit de développement logiciel (SDK) Python

Après l’envoi d’une exécution d’entraînement, un objet [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) est retourné. L’attribut `properties` de cet objet contient les informations Git journalisées. Par exemple, le code suivant récupère le hachage de validation :

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interface de ligne de commande

Vous pouvez utiliser la commande CLI `az ml run` pour récupérer les propriétés d’une exécution. Par exemple, la commande suivante retourne les propriétés de la dernière exécution dans l’expérience nommée `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Pour plus d’informations, consultez la documentation de référence [az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest).

## <a name="next-steps"></a>Étapes suivantes

* Pour une procédure pas à pas montrant comment entraîner avec Azure Machine Learning dans Visual Studio Code, consultez [Tutoriel : Former des modèles avec Azure Machine Learning](tutorial-train-models-with-aml.md).
* Pour une procédure détaillée sur la modification, l’exécution et le débogage de code localement, consultez le [didacticiel Python Hello World](https://code.visualstudio.com/docs/Python/Python-tutorial).
