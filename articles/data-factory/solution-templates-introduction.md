---
title: Vue d’ensemble des modèles pour Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle prédéfini pour démarrer rapidement avec Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: eb7a7eb8e1bdacae4b74e3a0019a376c440fe4d5
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091972"
---
# <a name="templates"></a>Modèles

Les modèles sont des pipelines Azure Data Factory prédéfinis qui permettent de démarrer rapidement avec Data Factory. Ils sont utiles aux débutants qui souhaitent se lancer sans délai. Ces modèles réduisent le temps de développement nécessaire à la création de projets d’intégration de données, améliorant ainsi la productivité des développeurs.

## <a name="create-data-factory-pipelines-from-templates"></a>Créer des pipelines Data Factory à partir de modèles

Vous pouvez commencer à créer un pipeline Data Factory à partir d’un modèle de deux façons :

1.  Sélectionnez **Créer un pipeline à partir d’un modèle** sur la page Vue d’ensemble pour ouvrir la galerie de modèles.

    ![Ouvrir la galerie de modèles sur la page Vue d’ensemble](media/solution-templates-introduction/templates-intro-image1.png)

1.  Dans l’onglet Auteur de l’Explorateur de ressources, sélectionnez **+** , puis **Pipeline à partir du modèle** pour ouvrir la galerie de modèles.

    ![Ouvrir la galerie de modèles dans l’onglet Auteur](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galerie de modèles

![Galerie de modèles](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Modèles Data Factory prêts à l’emploi

Data Factory utilise des modèles Azure Resource Manager pour enregistrer des modèles de pipelines Data Factory. Tous les modèles Resource Manager, ainsi que le fichier manifeste utilisé pour les modèles Data Factory prêts à l’emploi, se trouvent dans le [référentiel GitHub officiel d’Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Voici quelques exemples de modèles prédéfinis fournis par Microsoft :

-   Modèles de copie :

    -   [Copie en bloc à partir d’une base de données](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copier les nouveaux fichiers par LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copie de plusieurs conteneurs de fichiers entre différents magasins basés sur des fichiers](solution-template-copy-files-multiple-containers.md)

    -   [Déplacer des fichiers](solution-template-move-files.md)

    -   [Copie delta à partir d’une base de données](solution-template-delta-copy-with-control-table.md)

    -   Copie de \<source\> vers \<destination\>

        -   [D’Amazon S3 vers Azure Data Lake Storage Gen2](solution-template-migration-s3-azure.md)

        -   De Google BigQuery vers Azure Data Lake Storage Gen2

        -   De HDF vers Azure Data Lake Storage Gen2

        -   De Netezza vers Azure Data Lake Storage Gen1

        -   De SQL Server en local vers Azure SQL Database

        -   De SQL Server en local vers Azure SQL Data Warehouse

        -   D’Oracle en local vers Azure SQL Data Warehouse

-   Modèles SSIS

    -   Planifier l’exécution de packages SSIS par Azure-SSIS Integration Runtime

-   Modèles de transformation

    -   [ETL avec Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mes modèles

Vous pouvez également enregistrer un pipeline comme modèle en sélectionnant **Enregistrer comme modèle** dans l’onglet Pipeline.

![Enregistrer un pipeline comme modèle](media/solution-templates-introduction/templates-intro-image4.png)

Vous pouvez afficher les pipelines enregistrés comme modèles dans la section **Mes modèles** de la galerie de modèles, ou dans la section **Modèles** de l’Explorateur de ressources.

![Mes modèles](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Pour pouvoir utiliser la fonctionnalité Mes modèles, vous devez activer l’intégration GIT. Azure DevOps GIT et GitHub sont pris en charge.
