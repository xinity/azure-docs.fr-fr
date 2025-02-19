---
title: Installer Jupyter localement et le connecter à Spark dans Azure HDInsight
description: Découvrez comment installer un bloc-notes Jupyter localement sur votre ordinateur et le connecter à un cluster Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: 46164cfc0c2baff919808a831a67180b65a23ff7
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337653"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installer un bloc-notes Jupyter sur votre ordinateur et le connecter à Apache Spark sur HDInsight

Cet article explique comment installer Jupyter Notebook avec les noyaux PySpark (pour Python) et Apache Spark (pour Scala) personnalisés avec Spark Magic et connecter le bloc-notes à un cluster HDInsight. Plusieurs raisons peuvent motiver l’installation de Jupyter sur votre ordinateur local. Différents défis peuvent également se présenter. Consultez la section [Pourquoi dois-je installer Jupyter sur mon ordinateur ?](#why-should-i-install-jupyter-on-my-computer) à la fin de cet article.

Le processus d’installation de Jupyter et de connexion à Apache Spark sur HDInsight comprend quatre étapes principales.

* Configuration du cluster Spark.
* Installation de Jupyter Notebook.
* Installation des noyaux PySpark et Spark avec Spark Magic.
* Configuration de Spark Magic pour accéder au cluster Spark sur HDInsight.

Pour plus d’informations sur les noyaux personnalisés et Spark Magic disponible pour les blocs-notes Jupyter avec le cluster HDInsight, consultez [Noyaux disponibles pour les blocs-notes Jupyter avec les clusters HDInsight Spark Linux sur HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Prérequis

La configuration requise indiquée ici ne concerne pas l’installation de Jupyter. Elle s’applique à la connexion du bloc-notes Jupyter à un cluster HDInsight une fois le bloc-notes installé.

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installer le bloc-notes Jupyter sur votre ordinateur

Vous devez installer Python avant de pouvoir installer les blocs-notes Jupyter. Le [distribution Anaconda](https://www.anaconda.com/download/) installera à la fois Python et Jupyter Notebook.

Téléchargez le [programme d’installation Anaconda](https://www.anaconda.com/download/) pour votre plateforme et exécutez le programme d’installation. Lors de l’exécution de l’assistant d’installation, veillez à sélectionner l’option permettant d’ajouter Anaconda à votre variable PATH.  Consultez aussi ces instructions d’[installation de Jupyter à l’aide d’Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Installer Spark magic

1. Entrez l’une des commandes ci-dessous pour installer Spark magic. Consultez également la [documentation sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Version du cluster | Commande install |
    |---|---|
    |versions 3.5 et 3.6 |`pip install sparkmagic==0.12.7`|
    |version 3.4|`pip install sparkmagic==0.2.3`|

1. Assurez-vous que `ipywidgets` est correctement installé en exécutant la commande suivante :

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Installer les noyaux PySpark et Spark

1. Identifiez l’emplacement où `sparkmagic` est installé en exécutant la commande suivante :

    ```cmd
    pip show sparkmagic
    ```

    Changez ensuite votre répertoire de travail vers l’emplacement identifié avec la commande ci-dessus.

1. À partir de votre nouveau répertoire de travail, entrez une ou plusieurs des commandes ci-dessous pour installer chaque noyau souhaité :

    |Noyau | Commande |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. facultatif. Exécutez la commande suivante pour activer l’extension serveur :

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurer Spark Magic pour la connexion au cluster Spark HDInsight

Dans cette section, vous configurez Spark magic, que vous avez installé précédemment, pour qu’il se connecte à un cluster Apache Spark.

1. Démarrez l’interpréteur de commandes Python avec la commande suivante :

    ```cmd
    python
    ```

2. Les informations de configuration de Jupyter sont généralement stockées dans le répertoire de base des utilisateurs. Entrez la commande suivante pour identifier le répertoire de base, puis créez un dossier nommé **.sparkmagic**.  Le chemin complet est généré.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dans le dossier `.sparkmagic`, créez un fichier **config.json** dans lequel vous ajoutez l’extrait de code JSON suivant.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Apportez les modifications suivantes au fichier :

    |Valeur du modèle | Nouvelle valeur |
    |---|---|
    |{USERNAME}|Nom de connexion au cluster ; la valeur par défaut est `admin`.|
    |{CLUSTERDNSNAME}|Nom du cluster|
    |{BASE64ENCODEDPASSWORD}|Mot de passe encodé en base64 pour votre mot de passe actuel.  Vous pouvez générer un mot de passe en base64 sur [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Valeur à conserver si vous utilisez `sparkmagic 0.12.7` (clusters versions 3.5 et 3.6).  Si vous utilisez `sparkmagic 0.2.3` (clusters version 3.4), remplacez-la par `"should_heartbeat": true`.|

    Vous pouvez voir un exemple complet de fichier dans cet [exemple config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Les pulsations sont envoyées pour vous assurer que les sessions ne sont pas divulguées. Lorsqu’un ordinateur se met en veille ou s’arrête, la pulsation n’est pas envoyée, causant ainsi le nettoyage de la session. Pour les clusters v3.4, si vous souhaitez désactiver ce comportement, vous pouvez définir la configuration Livy `livy.server.interactive.heartbeat.timeout` à `0` à partir de l’interface utilisateur Ambari. Pour les clusters v3.5, si vous ne définissez pas la configuration 3.5 ci-dessus, la session ne sera pas supprimée.

5. Démarrez Jupyter. Utilisez la commande suivante depuis l’invite de commandes :

    ```cmd
    jupyter notebook
    ```

6. Vérifiez que vous pouvez utiliser Spark magic avec les noyaux. Procédez comme suit.

    a. Créer un nouveau bloc-notes. En haut à droite, sélectionnez **New**. Vous devez voir le noyau par défaut **Python 2** ou **Python 3** et les noyaux que vous avez installés. Les valeurs réelles peuvent varier en fonction de vos choix d’installation.  Sélectionnez **PySpark**.

    ![Noyaux disponibles dans un classeur Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Noyaux dans un classeur Jupyter")

    > [!IMPORTANT]  
    > Après avoir sélectionné **New**, passez en revue le code dans votre interpréteur de commandes pour rechercher les erreurs éventuelles.  Si vous voyez l’erreur `TypeError: __init__() got an unexpected keyword argument 'io_loop'`, cela est probablement dû à un problème connu avec certaines versions de Tornado.  Dans ce cas, arrêtez le noyau, puis passez à une version antérieure de Tornado en utilisant la commande suivante : `pip install tornado==4.5.3`.

    b. Exécutez l’extrait de code suivant :

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Votre connexion au cluster HDInsight sera testée, si vous parvenez à récupérer la sortie.

    Si vous souhaitez mettre à jour la configuration du notebook pour vous connecter à un autre cluster, mettez à jour le fichier config.json avec les nouvelles valeurs, comme indiqué à l’étape 3 ci-dessus.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Pourquoi dois-je installer Jupyter sur mon ordinateur ?

Il peut y avoir plusieurs raisons d’installer Jupyter sur son ordinateur et de le connecter à un cluster Apache Spark sur HDInsight.

* Même si les blocs-notes Jupyter sont déjà disponibles sur le cluster Spark dans Azure HDInsight, l’installation de Jupyter sur votre ordinateur vous permet de créer vos blocs-notes localement, de tester votre application sur un cluster en cours d’exécution, puis de charger les blocs-notes sur le cluster. Vous pouvez charger les blocs-notes dans le cluster à l’aide du bloc-notes Jupyter en cours d’exécution ou du cluster, ou en les enregistrant dans le dossier /HdiNotebooks du compte de stockage associé au cluster. Pour plus d’informations sur le stockage des blocs-notes, consultez [Where are Jupyter notebooks stored](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)(Où sont stockés les blocs-notes Jupyter) ?
* Les blocs-notes étant disponibles localement, vous pouvez vous connecter à différents clusters Spark selon les besoins de votre application.
* Vous pouvez utiliser GitHub pour implémenter un système de contrôle de code source et disposer du contrôle de version pour les blocs-notes. Vous pouvez également disposer d’un environnement de collaboration où plusieurs utilisateurs peuvent travailler avec le même bloc-notes.
* Vous pouvez travailler avec les blocs-notes localement sans avoir un cluster activé. Il vous suffit d’un cluster avec lequel tester vos blocs-notes, sans avoir à gérer manuellement vos blocs-notes ou à disposer d’un environnement de développement.
* Il peut être plus facile de configurer votre propre environnement de développement local que de configurer l’installation de Jupyter sur le cluster.  Vous pouvez tirer parti de tous les logiciels que vous avez installés localement sans configurer un ou plusieurs clusters distants.

> [!WARNING]  
> Lorsque Jupyter est installé sur votre ordinateur local, plusieurs utilisateurs peuvent exécuter le même bloc-notes sur le même cluster Spark en même temps. Dans ce cas, plusieurs sessions Livy sont créées. Si vous rencontrez un problème et que vous souhaitez effectuer un débogage, il sera difficile de savoir quelle session Livy appartient à quel utilisateur.  

## <a name="next-steps"></a>Étapes suivantes

* [Présentation : Apache Spark sur Azure HDInsight](apache-spark-overview.md)
* [Apache Spark avec BI : effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide des données des systèmes HVAC](apache-spark-ipython-notebook-machine-learning.md)
