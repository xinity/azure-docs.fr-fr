---
title: 'Démarrage rapide : Créer une application Java sur Azure Service Fabric'
description: Dans le cadre de ce démarrage rapide, vous créez une application Java pour Azure à l’aide d’un exemple d’application de services fiables Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ef0db5f72f5849942bb043261f1166cf7c046b1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703293"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Démarrage rapide :  Déployer une application Java dans Azure Service Fabric sur Linux

Ce guide de démarrage rapide montre comment déployer votre première application Java dans Azure Service Fabric à l’aide de l’IDE Eclipse sur un ordinateur de développement Linux. Lorsque vous avez terminé, vous disposez d’une application de vote avec un frontend Java qui enregistre les résultats de vote dans un service backend avec état dans le cluster.

Azure Service Fabric est une plateforme de systèmes distribués pour le déploiement et la gestion de microservices et de conteneurs.

![Azure Service Fabric - Exemple de vote](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

Dans ce guide de démarrage rapide, vous apprenez à :

* Utiliser Eclipse comme outil pour vos applications Java Service Fabric
* Déployer l’application dans le cluster local
* Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

1. [Installez le SDK Service Fabric et l’interface CLI Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installez Git](https://git-scm.com/)
3. [Installez Eclipse](https://www.eclipse.org/downloads/)
4. [Configurez l’environnement Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), en suivant les étapes facultatives d’installation du plug-in Eclipse

## <a name="download-the-sample"></a>Télécharger l’exemple

Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Exécuter l’application localement

1. Démarrez le cluster local en exécutant la commande suivante :

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Le démarrage du cluster local prend un certain temps. Pour vérifier que le cluster est entièrement opérationnel, accédez à Service Fabric Explorer à l’adresse **http://localhost:19080** . Les cinq nœuds sains indiquent que le cluster local est opérationnel.

    ![Azure Service Fabric Explorer - Affichage des nœuds sains](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Ouvrez Eclipse.
3. Cliquez sur **Fichier** > **Importer** > **Gradle** > **Projet Gradle existant** et suivez l’assistant.
4. Cliquez sur **Répertoire**, puis sélectionnez le répertoire `Voting` dans le dossier `service-fabric-java-quickstart` que vous avez cloné à partir de GitHub. Sélectionnez **Terminer**.

    ![Importer un projet Gradle dans Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Le projet `Voting` se trouve désormais dans l’Explorateur de package pour Eclipse.
6. Cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier l’application** dans la liste déroulante **Service Fabric**. Choisissez **PublishProfiles/Local.json** comme profil cible, puis cliquez sur **Publier**.

    ![Azure Service Fabric - Publier du code JSON local](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Ouvrez votre navigateur web préféré, puis accédez à l’application à l’adresse `http://localhost:8080`.

    ![Azure Service Fabric - Hôte local](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Vous pouvez à présent ajouter un ensemble d’options de vote et commencer à enregistrer les votes. L’application s’exécute et stocke toutes les données dans votre cluster Service Fabric, sans recourir à une base de données séparée.

## <a name="scale-applications-and-services-in-a-cluster"></a>Mettre à l’échelle les applications et services dans un cluster

Les services peuvent facilement être mis à l’échelle dans un cluster pour prendre en compte une modification de la charge sur les services. Pour mettre à l’échelle un service, vous modifiez le nombre d’instances s’exécutant dans le cluster. Différentes méthodes sont disponibles pour mettre à l’échelle vos services. Par exemple, vous pouvez utiliser des scripts ou des commandes de la CLI Service Fabric (sfctl). Dans les étapes suivantes, utilisez Service Fabric Explorer.

Service Fabric Explorer s’exécute dans tous les clusters Service Fabric et est accessible à partir d’un navigateur, en accédant au port de gestion HTTP (19080) du cluster, par exemple, `http://localhost:19080`.

Pour mettre à l’échelle le service web frontal, procédez comme suit :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `https://localhost:19080`.
2. Cliquez sur le bouton de sélection ( **...** ) à côté du nœud **fabric:/Voting/VotingWeb** dans l’arborescence, puis sélectionnez **Scale Service** (Mettre à l’échelle le service).

    ![Mettre à l’échelle un service dans Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Vous pouvez maintenant choisir de mettre à l’échelle le nombre d’instances du service frontal web.

3. Définissez le nombre sur **2**, puis sélectionnez **Scale Service**.
4. Sélectionnez le nœud **fabric:/Voting/VotingWeb** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID).

    ![Service mis à l’échelle dans Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Vous pouvez maintenant voir que le service a deux instances et, dans l’arborescence, vous voyez les nœuds sur lesquels les instances s’exécutent.

Via cette tâche de gestion simple, vous avez doublé les ressources disponibles pour permettre au service frontal de traiter la charge utilisateur. Il est important de comprendre que vous n’avez pas besoin de plusieurs instances d’un service pour que celui-ci s’exécute de manière fiable. En cas de défaillance d’un service, Service Fabric veille à ce qu’une nouvelle instance du service s’exécute dans le cluster.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

* Utiliser Eclipse comme outil pour vos applications Java Service Fabric
* Déployer des applications Java sur votre cluster local
* Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds

Pour en savoir plus sur l’utilisation des applications Java dans Service Fabric, passez au tutoriel sur les applications Java.

> [!div class="nextstepaction"]
> [Didacticiel : créer et déployer une application avec un service frontal API Web Java et un service principal avec état](./service-fabric-tutorial-create-java-app.md)
