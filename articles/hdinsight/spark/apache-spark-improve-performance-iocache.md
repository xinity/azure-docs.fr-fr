---
title: Performances des charges de travail Apache Spark avec le cache d’E/S Azure HDInsight (préversion)
description: Découvrez-en plus sur Azure HDInsight IO Cache et comment l’utiliser pour améliorer les performances d’Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: ecb393ea1f64897f17ce73170da1673886ef8916
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266181"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Améliorer les performances des charges de travail Apache Spark à l’aide d’Azure HDInsight IO Cache

IO Cache est un service de mise en cache de données pour Azure HDInsight qui améliore les performances des travaux Apache Spark. IO Cache fonctionne également avec des charges de travail [Apache TEZ](https://tez.apache.org/) et [Apache Hive](https://hive.apache.org/), qui peuvent être exécutées sur des clusters [Apache Spark](https://spark.apache.org/). IO Cache utilise un composant de mise en cache open source appelé RubiX. RubiX est un cache de disque local conçu pour une utilisation avec les moteurs d’analytique du Big Data qui accèdent aux données à partir de systèmes de stockage cloud. RubiX est un système de mise en cache unique, car il utilise des disques SSD au lieu de réserver de la mémoire d’exploitation pour la mise en cache. Le service IO Cache lance et gère les serveurs de métadonnées RubiX sur chaque nœud Worker du cluster. Il configure également tous les services du cluster pour une utilisation transparente du cache RubiX.

La plupart des disques SSD fournissent plus de 1 Go par seconde de bande passante. Cette bande passante, complétée par le cache de fichiers en mémoire du système d’exploitation, fournit suffisamment de bande passante pour charger des moteurs de traitement de calcul Big Data, tels qu’Apache Spark. La mémoire d’exploitation reste disponible pour qu’Apache Spark traite des tâches fortement dépendantes de la mémoire, comme les lectures aléatoires. L’utilisation exclusive de la mémoire d’exploitation permet à Apache Spark d’atteindre une utilisation optimale des ressources.  

> [!Note]  
> IO Cache utilise actuellement RubiX comme composant de mise en cache, mais cela pourrait changer dans les futures versions du service. Utilisez les interfaces IO Cache et n’utilisez aucune dépendance directement dans l’implémentation de RubiX.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Avantages d’Azure HDInsight IO Cache

L’utilisation d’IO Cache augmente les performances des travaux qui lisent les données depuis le stockage Blob Azure.

Vous n’avez pas besoin d’apporter des modifications à vos travaux Spark pour voir les performances s’améliorer avec l’utilisation d’IO Cache. Lorsque le service IO Cache est désactivé, ce code Spark lit les données à distance à partir du stockage Blob Azure : `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Lorsque le service IO Cache est activé, la même ligne de code met la lecture en cache via IO Cache. Lors des lectures suivantes, les données sont lues localement à partir de disque SSD. Les nœuds Worker d’un cluster HDInsight sont équipés de disques SSD dédiés connectés localement. HDInsight IO Cache utilise ces disques SSD locaux pour la mise en cache, ce qui réduit la latence à un niveau minime et optimise la bande passante.

## <a name="getting-started"></a>Prise en main

Azure HDInsight IO Cache est désactivé par défaut dans la préversion. IO Cache est disponible dans les clusters Spark Azure HDInsight 3.6 et +, qui exécutent Apache Spark 2.3.  Pour activer le service IO Cache, procédez comme suit :

1. Sélectionnez votre cluster HDInsight dans le [portail Azure](https://portal.azure.com).

1. Dans la page **Vue d’ensemble** (qui s’ouvre par défaut lorsque vous sélectionnez le cluster), sélectionnez **Accueil Ambari** sous **Tableaux de bord de cluster**.

1. Sélectionnez le service **IO Cache** sur la gauche.

1. Sélectionnez **Actions** et **Activer**.

    ![Activation du service Cache d’E/S dans Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Activation du service Cache d’E/S dans Ambari")

1. Confirmez le redémarrage de tous les services affectés sur le cluster.

>[!NOTE]  
> Même si la barre de progression indique que le service est activé, IO Cache n’est pas réellement activé jusqu’au redémarrage des autres services affectés.

## <a name="troubleshooting"></a>Résolution de problèmes
  
Vous pourrez rencontrer des erreurs d’espace disque lors de l’exécution des travaux Spark après l’activation du service IO Cache. Ces erreurs se produisent parce que Spark utilise également le stockage de disque local pour stocker des données pendant les opérations de lectures aléatoires. Spark peut manquer d’espace de disque SSD une fois IO Cache activé, et l’espace dédié au stockage Spark est réduit. La quantité d’espace utilisée par le service IO Cache s’élève par défaut à la moitié de l’espace de disque SSD total. L’utilisation de l’espace disque du service IO Cache peut être configurée dans Ambari. Si vous rencontrez des erreurs d’espace disque, réduisez la quantité d’espace de disque SSD utilisée pour le service IO Cache et redémarrez-le. Pour modifier l’espace défini pour IO Cache, procédez comme suit :

1. Dans Apache Ambari, sélectionnez le service **HDFS** sur la gauche.

1. Sélectionnez les onglets **Configurations** et **Avancé**.

    ![Modifier la configuration avancée HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Modifier la configuration avancée HDFS")

1. Faites défiler la page vers le bas et développez la zone **Configuration core-site personnalisée**.

1. Localisez la propriété **hadoop.cache.data.fullness.percentage**.

1. Modifiez la valeur définie dans la zone.

    ![Pourcentage de remplissage du cache d’E/S](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Pourcentage de remplissage du cache d’E/S")

1. Sélectionnez **Enregistrer** dans le coin supérieur droit.

1. Sélectionnez **Redémarrer** > **Redémarrer tous les éléments affectés**.

    ![Apache Ambari - Redémarrer tous les éléments affectés](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Redémarrer tous les éléments affectés")

1. Sélectionnez **Confirmer le redémarrage**.

Si cela ne fonctionne pas, désactivez IO Cache.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le Cache d’E/S, notamment les tests de performances, consultez ce billet de blog : [Les tâches Apache Spark bénéficient d’une vitesse neuf fois plus rapide avec HDInsight IO Cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
