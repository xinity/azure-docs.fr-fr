---
title: Instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse | Microsoft Docs
description: Explication et exemples concernant l’instruction CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse pour le développement de solutions.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: e347287a6d77cdc947a79ca497fdb2ffe83ad1bc
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882471"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse

Cet article explique l’instruction T-SQL CREATE TABLE AS SELECT (CTAS) dans Azure SQL Data Warehouse pour le développement de solutions. L’article fournit également des exemples de code.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

L’instruction [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) est l’une des fonctionnalités T-SQL les plus importantes. CTAS désigne une opération parallèle qui crée une table en fonction de la sortie d’une instruction SELECT. C’est le moyen le plus rapide pour créer et insérer des données dans une table avec une seule commande.

## <a name="selectinto-vs-ctas"></a>SELECT…INTO et CTAS

CTAS est une version plus personnalisable de l’instruction [SELECT…INTO](/sql/t-sql/queries/select-into-clause-transact-sql).

Voici un exemple simple d’instruction SELECT…INTO :

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECT…INTO ne vous permet pas de modifier la méthode de distribution ou le type d’index dans le cadre de l’opération. Vous créez `[dbo].[FactInternetSales_new]` en utilisant le type de distribution par défaut de ROUND_ROBIN et la structure de table par défaut de CLUSTERED COLUMNSTORE INDEX.

En revanche, avec CTAS, vous pouvez spécifier la distribution de données de la table, ainsi que le type de structure de table. Pour convertir l’exemple précédent en CTAS :

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Si vous essayez uniquement de modifier l’index dans votre opération CTAS et si la table source est distribuée par hachage, conservez la même colonne de distribution et le même type de données. Cela évite le déplacement des données entre les distributions pendant l’opération, d’où une efficacité accrue.

## <a name="use-ctas-to-copy-a-table"></a>Utiliser CTAS pour copier une table

L’une des utilisations les plus courantes de CTAS consiste sans doute à créer une copie de table afin que vous puissiez modifier le DDL. Supposons que vous avez créé votre table `ROUND_ROBIN` et que vous souhaitez maintenant la remplacer par une table distribuée sur une colonne. CTAS permet de modifier la colonne de distribution. Vous pouvez aussi utiliser CTAS pour modifier le partitionnement, l’indexation ou les types de colonnes.

Supposons que vous avez créé cette table en utilisant le type de distribution par défaut `ROUND_ROBIN`, sans spécifier de colonne de distribution dans `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Maintenant, vous souhaitez créer une copie de cette table avec `Clustered Columnstore Index` afin de tirer parti des performances de tables Columnstore en cluster. Vous souhaitez également distribuer cette table sur `ProductKey` dans la mesure où vous anticipez des jointures sur cette colonne et vous souhaitez éviter le déplacement de données au cours des jointures sur `ProductKey`. Enfin, vous souhaitez ajouter le partitionnement sur `OrderDateKey` pour supprimer rapidement les anciennes données en supprimant les anciennes partitions. Voici l’instruction CTAS qui copie votre ancienne table dans une nouvelle table.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Pour finir, vous pouvez renommer vos tables pour intégrer votre nouvelle table et supprimer votre ancienne table.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Utiliser CTAS pour contourner les fonctionnalités non prises en charge

Vous pouvez aussi utiliser CTAS pour contourner un certain nombre de fonctionnalités non prises en charge et répertoriées ci-après. Cette méthode est souvent utile, car elle assure non seulement la conformité de votre code, mais également une vitesse d’exécution accrue de ce dernier sur SQL Data Warehouse. Ces performances découlent de sa conception entièrement parallélisée. Il s’agit entre autres des scénarios suivants :

* Jointures ANSI sur les opérations UPDATE
* Jointures ANSI sur les opérations DELETE
* Instruction MERGE

> [!TIP]
> Envisagez toujours de recourir à l’instruction CTAS en premier lieu. Il est souvent intéressant de résoudre un problème à l’aide de CTAS, même si vous écrivez ainsi davantage de données.

## <a name="ansi-join-replacement-for-update-statements"></a>Remplacement de jointures ANSI pour les instructions de mise à jour

Vous constaterez peut-être que vous disposez d’une mise à jour complexe. Cette mise à jour joint plusieurs tables à l’aide d’une syntaxe de jointure ANSI pour l’exécution d’une opération UPDATE ou DELETE.

Imaginons que vous souhaitiez mettre à jour la table suivante :

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

La requête d’origine pourrait ressembler à ceci :

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la clause `FROM` d’une instruction `UPDATE`. Vous ne pouvez donc pas utiliser l’exemple précédent sans le modifier.

Vous pouvez remplacer l’exemple précédent en combinant une instruction CTAS et une jointure implicite :

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Remplacement de jointures ANSI pour les instructions de suppression

Parfois, la meilleure approche pour la suppression des données consiste à utiliser CTAS, en particulier pour les instructions `DELETE` qui utilisent les syntaxes de jointure ANSI. En effet, SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la clause `FROM` d’une instruction `DELETE`. Plutôt que de supprimer les données, vous pouvez sélectionner les données à conserver.

Vous trouverez ci-dessous un exemple d’instruction `DELETE` convertie :

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Remplacement d’instructions MERGE

Vous pouvez remplacer les instructions MERGE, du moins partiellement, à l’aide de CTAS. Vous pouvez combiner les instructions `INSERT` et `UPDATE` dans une seule et même instruction. Tous les enregistrements supprimés doivent être limités à partir de l’instruction `SELECT` à omettre dans les résultats.

L’exemple suivant concerne `UPSERT` :

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>déclarer explicitement le type de données et la possibilité de valeur NULL de la sortie

Lorsque vous procédez à la migration de votre code, vous pouvez constater que vous exécutez le type de modèle de codage suivant :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Vous pourriez envisager de convertir ce code en instruction CTAS, et vous auriez raison. Toutefois, un problème se dissimule derrière ce scénario.

Le code suivant ne génère pas le même résultat :

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Notez que la colonne « result » indique le type de données et les valeurs de possibilité de valeur NULL de l’expression. Ce type de données peut entraîner de légers écarts de valeurs si vous n’y prenez pas garde.

Essayez l’exemple suivant :

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

La valeur stockée en guise de résultat diffère. Étant donné que la valeur persistante dans la colonne de résultat est utilisée dans d’autres expressions, l’erreur devient encore plus significative.

![Capture d’écran des résultats CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

C’est important dans le cas des migrations de données. Bien que la seconde requête soit effectivement plus précise, un problème se pose. Les données obtenues diffèrent de celles du système source, ce qui compromet l’intégrité de la migration. Il s’agit là de l’un des rares cas dans lesquels la « mauvaise » réponse est en réalité la bonne !

Cet écart entre les deux résultats est dû à la conversion de type (transtypage) implicite. Dans le premier exemple, la table définit la définition de colonne. Lorsque la ligne est insérée, une conversion de type implicite est effectuée. Dans le second exemple, aucune conversion de type implicite ne se produit, car l’expression définit le type de données de la colonne.

La colonne figurant dans le second exemple a été définie comme une colonne Nullable, ce qui n’est pas son cas dans le premier exemple. Lorsque la table a été créée dans le premier exemple, la possibilité de valeur Null de la colonne a été définie explicitement. Dans le second exemple, cet aspect est déterminé par l’expression et entraîne par défaut une définition de valeur Null.

Pour résoudre ces problèmes, vous devez explicitement définir la conversion de type et la possibilité de valeur Null dans la partie SELECT de l’instruction CTAS. Vous ne pouvez pas définir ces propriétés dans la partie CREATE TABLE.
L’exemple suivant montre comment réparer le code :

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Notez les points suivants :

* Vous pouvez utiliser CAST ou CONVERT.
* Utilisez ISNULL, et non COALESCE, pour forcer la possibilité de valeur Null. Reportez-vous à la remarque suivante.
* ISNULL est la fonction située la plus à l’extérieur.
* La seconde partie de l’instruction ISNULL est une constante : 0.

> [!NOTE]
> La possibilité de valeur Null doit être impérativement définie avec ISNULL, et non avec COALESCE. COALESCE n’est pas une fonction déterministe et par conséquent, le résultat de l’expression sera toujours NULLable. ISNULL est différente. Cette fonction est déterministe. Par conséquent, lorsque la seconde partie de la fonction ISNULL est une constante ou un littéral, la valeur résultante est NOT NULL.

Il est également important de vérifier l’intégrité de vos calculs pour le basculement de partitions de table. Imaginons que vous ayez défini la table de faits suivante :

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Toutefois, le champ Quantité est une expression calculée. Il ne fait pas partie des données sources.

Pour créer votre jeu de données partitionné, vous pouvez utiliser le code suivant :

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Cette requête s’exécute parfaitement. Toutefois, un problème se pose lorsque vous essayez de procéder au basculement de partitions. Les définitions de table ne correspondent pas. Pour faire correspondre les définitions de table, modifiez le CTAS afin d’ajouter une fonction `ISNULL` dans le but de conserver l’attribut de possibilité de valeur null de la colonne.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Vous pouvez constater que la cohérence des types et le maintien des propriétés de possibilité de valeur Null sur une instruction CTAS constituent une meilleure pratique d’ingénierie. Ces opérations contribuent à garantir l’intégrité de vos calculs, ainsi que la possibilité de basculement de partitions.

CTAS est l’une des instructions les plus importantes de SQL Data Warehouse. Vous devez donc faire en sorte d’en comprendre les moindres aspects. Consultez la [documentation CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des conseils supplémentaires sur le développement, consultez la [vue d’ensemble sur le développement](sql-data-warehouse-overview-develop.md).

