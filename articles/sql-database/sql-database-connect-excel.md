---
title: Connexion d’Excel à une base de données unique dans Azure SQL Database | Microsoft Docs
description: Découvrez comment connecter Microsoft Excel à une base de données unique dans Azure SQL Database. Importez des données dans Excel pour les rapports et l’exploration des données.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: 8711d8f7ebc00b2d0fa51ff9f420293e96766d1c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569359"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Connexion d’Excel à une base de données unique dans Azure SQL Database et création d’un rapport

Connectez Excel à une base de données unique dans Azure SQL Database, importez des données et créez des tables et des graphiques en fonction de valeurs présentes dans la base de données. Dans ce didacticiel, vous allez configurer la connexion entre Excel et une table de base de données, enregistrer le fichier qui stocke les données et les informations de connexion pour Excel, puis créer un graphique croisé dynamique à partir des valeurs de base de données.

Vous avez besoin d’une base de données unique avant de commencer. Si vous n’en avez pas, consultez [Créer une base de données unique](sql-database-single-database-get-started.md) et [Créer un pare-feu IP au niveau du serveur](sql-database-server-level-firewall-rule.md) pour obtenir une base de données unique avec des exemples de données opérationnelle en quelques minutes.

Dans cet article, vous allez importer des données d’exemple dans Excel à partir de cet article, mais vous pouvez suivre les mêmes étapes avec vos propres données.

Vous aurez besoin d’une copie d’Excel. Cet article utilise [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Connecter Excel à une base de données SQL et charger des données

1. Pour connecter Excel à la base de données SQL, ouvrez Excel, puis créez un nouveau classeur ou ouvrez un classeur Excel existant.
2. Dans la barre de menus en haut de la page, sélectionnez l’onglet **Données**, puis sélectionnez **Obtenir des données**, À partir d’Azure et **À partir d’Azure SQL Database**. 

   ![Sélectionnez la source de données : connectez Excel à la base de données SQL.](./media/sql-database-connect-excel/excel_data_source.png)

   L’Assistant Connexion de données s’ouvre.
3. Dans la boîte de dialogue **Connexion à un serveur de base de données**, saisissez le **Nom du serveur** SQL Database auquel vous voulez vous connecter selon le format <*nom_serveur*> **.database.windows.net**. Par exemple, **msftestserver.database.windows.net**. Si vous le souhaitez, entrez le nom de votre base de données. Sélectionnez **OK** pour ouvrir la fenêtre des informations d’identification. 

   ![Boîte de dialogue Connexion à un serveur de base de données](media/sql-database-connect-excel/server-name.png)

4. Dans la boîte de dialogue **Base de données SQL Server**, sélectionnez **Base de données** sur le côté gauche et entrez vos **Nom d’utilisateur** et **Mot de passe** pour le serveur SQL Database auquel vous souhaitez vous connecter. Sélectionnez **Se connecter** pour ouvrir le **Navigateur**. 

   ![Tapez le nom du serveur et les informations de connexion](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Selon votre environnement réseau, vous n’êtes peut-être pas en mesure de vous connecter ou vous risquez de perdre la connexion si le serveur SQL Database n’autorise pas le trafic à partir de l’adresse IP du client. Accédez au [portail Azure](https://portal.azure.com/), cliquez sur Serveurs SQL, cliquez sur votre serveur, cliquez sur le pare-feu sous paramètres et ajoutez l’adresse IP du client. Pour plus d’informations, voir [Configuration des paramètres du pare-feu](sql-database-configure-firewall-settings.md)

5. Dans le **Navigateur**, sélectionnez la base de données à utiliser dans la liste, sélectionnez les tables ou vues à utiliser (nous avons choisi **vGetAllCategories**), puis sélectionnez **Charger** pour déplacer les données depuis votre base de données vers votre feuille de calcul Excel.

    ![Sélectionner une base de données et une table](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importer les données dans Excel et créer un graphique croisé dynamique

La connexion étant établie, vous avez plusieurs options pour charger les données. Par exemple, la procédure suivante crée un graphique croisé dynamique à partir des données trouvées dans votre base de données SQL. 

1. Suivez les étapes de la section précédente, mais cette fois, au lieu de sélectionner **Charger**, sélectionnez **Charger dans** dans la liste déroulante **Charger**.
2. Ensuite, sélectionnez la méthode d’affichage de ces données dans votre classeur. Nous avons choisi **Graphique croisé dynamique**. Vous pouvez également choisir de créer une **Nouvelle feuille de calcul** ou **d’Ajouter ces données au modèle de données**. Pour plus d’informations sur les modèles de données, consultez la page [Créer un modèle de données dans Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Choix du format des données dans Excel](./media/sql-database-connect-excel/import-data.png)

    La feuille de calcul comporte désormais un tableau et un graphique croisés dynamiques vides.
3. Sous **Champs de tableau croisé dynamique**, cochez toutes les cases correspondant aux champs que vous souhaitez afficher.

    ![Configurez le rapport de base de données.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Si vous souhaitez connecter d’autres classeurs et feuilles de calcul Excel à la base de données, sélectionnez l’onglet **Données** et sélectionnez **Sources récentes** pour lancer la boîte de dialogue **Sources récentes**. À partir de cette boîte de dialogue, choisissez la connexion que vous avez créée dans la liste, puis cliquez sur **Ouvrir**.
> ![Boîte de dialogue Sources récentes](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Créer une connexion permanente à l’aide d’un fichier .odc

Pour enregistrer les détails de la connexion de façon permanente, vous pouvez créer un fichier .odc et faire de cette connexion une option sélectionnable dans la boîte de dialogue **Connexions existantes**. 

1. Dans la barre de menus en haut de la page, sélectionnez l’onglet **Données**, puis sélectionnez **Connexions existantes** pour lancer la boîte de dialogue **Connexions existantes**. 
   1. Sélectionnez **Parcourir** pour ouvrir la boîte de dialogue **Sélectionner une source de données**.   
   2. Sélectionnez le fichier **+NewSqlServerConnection.odc**, puis sélectionnez **ouvrir** pour ouvrir **l’Assistant de connexion de données**.

      ![Boîte de dialogue Nouvelle connexion](media/sql-database-connect-excel/new-connection.png)

2. Dans **l’Assistant de connexion de données**, tapez le nom du serveur et les informations de connexion SQL Database. Sélectionnez **Suivant**. 
   1. Dans la liste déroulante, sélectionnez la base de données qui contient vos données. 
   2. Sélectionnez la table ou vue qui vous intéresse. Nous avons choisi vGetAllCategories.
   3. Sélectionnez **Suivant**. 

      ![Assistant de connexion de données](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Sélectionnez l’emplacement de votre fichier, le **Nom de fichier** et le **Nom convivial** dans l’écran suivant de l’Assistant de connexion de données. Vous pouvez également choisir d’enregistrer le mot de passe dans le fichier, bien que cette opération puisse exposer vos données à un accès indésirable. Sélectionnez **Terminer** quand vous êtes prêt. 

    ![Enregistrer la connexion de données](media/sql-database-connect-excel/save-data-connection.png)

4. Sélectionnez la façon dont vous souhaitez importer vos données. Nous avons choisi de créer un tableau croisé dynamique. Vous pouvez également modifier les propriétés de la connexion en sélectionnant **Propriétés**. Sélectionnez **OK** quand vous êtes prêt. Si vous n’avez pas choisi d’enregistrer le mot de passe avec le fichier, vous devez entrer vos informations d’identification. 

    ![Importer des données](media/sql-database-connect-excel/import-data2.png)

5. Vérifiez que votre nouvelle connexion a été enregistrée en développant l’onglet **Données**, puis en sélectionnant **Connexions existantes**. 

    ![Connexion existante](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [se connecter à la base de données SQL avec SQL Server Management Studio](sql-database-connect-query-ssms.md) pour une interrogation et une analyse avancées.
* En savoir plus sur les avantages des [pools élastiques](sql-database-elastic-pool.md).
* Découvrez comment [créer une application web qui se connecte à la base de données SQL sur le serveur principal](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
