---
title: 'Didacticiel : Créer une application de machine learning Spark dans Azure HDInsight'
description: Didacticiel - Instructions détaillées sur la création d’applications de Machine Learning Apache Spark sur des clusters Spark HDInsight à l’aide d’un bloc-notes Jupyter Notebook.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: e77414da964d548b64250bbf98f86bee1529f2ab
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327009"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Didacticiel : Créer une application de machine learning Apache Spark dans Azure HDInsight

Dans ce tutoriel, vous allez apprendre à utiliser [Jupyter Notebook](https://jupyter.org/) afin de créer une application d’apprentissage automatique [Apache Spark](https://spark.apache.org/) pour Azure HDInsight.

[MLib](https://spark.apache.org/docs/latest/ml-guide.html) est une bibliothèque de Machine Learning de Spark constituée d’utilitaires et d’algorithmes d’apprentissage courants, notamment la classification, la régression, le clustering, le filtrage collaboratif, la réduction de dimensionnalité, ainsi que les primitives d’optimisation sous-jacentes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Développer une application d’apprentissage automatique Apache Spark

## <a name="prerequisites"></a>Prérequis

* Un cluster Apache Spark sur HDInsight. Consultez [Créer un cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Connaissances sur l’utilisation des blocs-notes Jupyter Notebook avec Spark sur HDInsight. Pour plus d’informations, consultez [Charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Comprendre le jeu de données

L’application utilise l’exemple de données **HVAC.csv**, qui est disponible par défaut sur tous les clusters. Le fichier se trouve sous `\HdiSamples\HdiSamples\SensorSampleData\hvac`. Les données indiquent la température cible et la température réelle de bâtiments dans lesquels un système HVAC est installé. La colonne **System** représente l’ID système et la colonne **SystemAge** représente le nombre d’années écoulées depuis la mise en place du système HVAC dans le bâtiment. Avec ces données, vous pouvez prédire si un bâtiment sera plus chaud ou plus froid en fonction de la température cible, selon un ID système et l’ancienneté du système.

![Capture instantanée des données utilisées pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Capture instantanée des données utilisées pour l’exemple de Machine Learning Spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Développer une application de Machine Learning Spark à l’aide de Spark MLlib

Dans cette application, vous utilisez un [pipeline ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) Spark pour effectuer une classification de documents. Les pipelines ML fournissent un ensemble d’API de haut niveau basées sur des trames de données qui permettent aux utilisateurs de créer et d’ajuster des pipelines d’apprentissage automatique pratique. Dans le pipeline, vous fractionnez le document en mots, convertissez ces mots en un vecteur de fonctionnalité numérique avant de créer un modèle de prédiction utilisant les étiquettes et vecteurs de fonctionnalité. Procédez comme suit pour créer l’application.

1. Créez un bloc-notes Jupyter à l’aide du noyau PySpark. Pour obtenir des instructions, consultez [Créer un bloc-notes Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

1. Importez les types requis pour ce scénario. Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

1. Chargez les données (hvac.csv), analysez-les et utilisez-les pour effectuer l’apprentissage du modèle.

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    Dans l’extrait de code, vous définissez une fonction qui compare la température réelle avec la température cible. Si la température actuelle est supérieure, le bâtiment est chaud, ce qui est indiqué par la valeur **1,0**. Sinon, le bâtiment est froid, ce qui est indiqué par la valeur **0.0**.

1. Configurez le pipeline Spark ML qui est composé de trois étapes : Tokenizer, HashingTF et LogisticRegression.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Pour plus d’informations sur le pipeline et son fonctionnement, consultez le [pipeline de Machine Learning Apache Spark](https://spark.apache.org/docs/latest/ml-pipeline.html).

1. Ajustez le pipeline au document d’apprentissage.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Vérifiez le document d’apprentissage pour contrôler votre progression dans l’application.

    ```PySpark
    training.show()
    ```

    Le résultat ressemble à ce qui suit :

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Comparaison de la sortie et du fichier CSV brut. Par exemple, la première ligne du fichier CSV contient les données suivantes :

    ![Capture instantanée des données de sortie pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Capture instantanée des données de sortie pour l’exemple de Machine Learning Spark")

    Notez que la température réelle est inférieure à la température cible, suggérant que le bâtiment est froid. De ce fait, dans le résultat, l’**étiquette** de la première ligne a pour valeur **0,0**,, ce qui signifie que le bâtiment n’est pas chaud.

1. Préparez un jeu de données sur lequel exécuter le modèle d’apprentissage. Pour ce faire, vous transmettez un ID système et l’ancienneté du système (indiqués par **SystemInfo** dans le résultat de l’apprentissage). Le modèle prédira si le bâtiment ayant cet ID système et cette ancienneté de système sera plus chaud (indiqué par la valeur 1,0) ou plus froid (indiqué par la valeur 0,0).

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

1. Pour finir, effectuez des prédictions sur les données de test.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Le résultat ressemble à ce qui suit :

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Dans la première ligne de la prédiction, vous pouvez voir que pour un système HVAC avec l’ID 20 et une ancienneté de 25 ans, le bâtiment est chaud (**prediction=1.0**). La première valeur de DenseVector (0,49999) correspond à la prédiction 0.0, et la seconde valeur (0,5001) correspond à la prédiction 1.0. Dans le résultat, même si la seconde valeur n’est que légèrement supérieure, le modèle indique **prediction=1,0**.

1. Arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, sélectionnez **Fermer et arrêter**. Cela a pour effet d’arrêter et de fermer le bloc-notes.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Utiliser une bibliothèque scikit-learn Anaconda pour le Machine Learning Spark

Les clusters Apache Spark sur HDInsight incluent des bibliothèques Anaconda, notamment la bibliothèque **scikit-learn** pour le Machine Learning. Cette bibliothèque contient également différents jeux de données qui vous permettent de créer des exemples d’application directement à partir d’un bloc-notes Jupyter. Pour voir des exemples d’utilisation de la bibliothèque scikit-learn, consultez [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne comptez pas continuer à utiliser cette application, effectuez les étapes suivantes pour supprimer le cluster que vous avez créé :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Dans la zone **Recherche** située en haut, tapez **HDInsight**.

1. Sous **Services**, sélectionnez **Clusters HDInsight**.

1. Dans la liste des clusters HDInsight qui s’affiche, sélectionnez les points de suspension **...** à côté du cluster que vous avez créé pour ce tutoriel.

1. Sélectionnez **Supprimer**. Sélectionnez **Oui**.

![Portail Azure : supprimer un cluster HDInsight](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "Supprimer un cluster HDInsight")

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez vu comment utiliser Jupyter Notebook afin de créer une application de machine learning Apache Spark pour Azure HDInsight. Passez au didacticiel suivant afin de découvrir comment utiliser IntelliJ IDEA pour les travaux Spark.

> [!div class="nextstepaction"]
> [Créer une application Scala Maven avec IntelliJ](./apache-spark-create-standalone-application.md)
