---
title: Utilisation des vues T-SQL dans Azure SQL Data Warehouse | Microsoft Docs
description: Conseils relatifs à l’utilisation de vues T-SQL dans  Azure SQL Data Warehouse dans le cadre du développement de solutions.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479520"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Vues dans Azure SQL Data Warehouse
Conseils relatifs à l’utilisation de vues T-SQL dans  Azure SQL Data Warehouse dans le cadre du développement de solutions. 

## <a name="why-use-views"></a>Pourquoi utiliser des vues ?
Des vues peuvent être utilisées de différentes façons afin d’améliorer la qualité de votre solution.  Cet article met en évidence quelques exemples montrant comment enrichir votre solution avec des vues, ainsi que les limites à prendre en considération.


> [!IMPORTANT]
> Consultez la nouvelle syntaxe de vue matérialisée à la page [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  Pour plus d’informations, consultez les [notes de publication](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> La syntaxe de CREATE VIEW n’est pas abordée dans cet article. Pour plus d’informations, consultez la documentation [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql).
> 

## <a name="architectural-abstraction"></a>Abstraction architecturale

Un modèle d’application courant consiste à recréer des tables au moyen de la commande CREATE TABLE AS SELECT (CTAS). Ceci est suivi d’un modèle de changement de nom d’un objet lors du chargement des données.

L’exemple suivant permet d’ajouter de nouveaux enregistrements de date à une dimension de date. Notez comment une nouvelle table, DimDate_New, est d’abord créée, puis renommée pour remplacer la version d’origine de la table.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Toutefois, cette approche peut provoquer l’apparition et la disparition de table d’une vue utilisateur, ainsi que l’affichage de messages d’erreur de type « la table n’existe pas ». Vous pouvez utiliser des vues pour fournir aux utilisateurs une couche de présentation cohérente alors que les objets sous-jacents sont renommés. L’accès aux données étant fourni via des vues, les utilisateurs n’ont pas besoin d’une visibilité des tables sous-jacentes. Cette couche fournit une expérience utilisateur homogène, tout en garantissant que les concepteurs d’entrepôts de données puissent faire évoluer le modèle de données. Pouvoir faire évoluer les tables sous-jacentes signifie que les concepteurs peuvent utiliser CTAS pour optimiser les performances lors du processus de chargement de données.   

## <a name="performance-optimization"></a>Optimisation des performances
Une vue peut également être utilisée pour mettre en place des jointures plus performantes entre les tables. Ainsi, une vue peut inclure une clé de distribution redondante parmi ses critères de jointure, afin de réduire le nombre de déplacements de données. Elle peut également permettre de forcer une indication de jointure ou de requête spécifique. Utiliser des vues de cette manière permet de garantir que les jointures sont toujours effectuées de façon optimale en évitant d’avoir à se souvenir de leur construction correcte.

## <a name="limitations"></a>Limites
Dans SQL Data Warehouse, les vues sont exclusivement stockées sous forme de métadonnées. De ce fait, les options suivantes ne sont pas disponibles :

* Il n’existe aucune option de liaison de schéma.
* Les tables de base ne peuvent pas être mises à jour par le biais de la vue.
* Vous ne pouvez pas créer des vues sur des tables temporaires.
* Les indications EXPAND/NOEXPAND ne sont pas prises en charge.
* SQL Data Warehouse n’inclut aucune vue indexée.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


