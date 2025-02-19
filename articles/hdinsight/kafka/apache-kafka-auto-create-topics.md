---
title: Activer la création automatique de rubriques dans Apache Kafka - Azure HDInsight
description: Découvrez comment configurer Apache Kafka sur HDInsight pour créer des rubriques automatiquement. Vous pouvez configurer Kafka en définissant auto.create.topics.enable sur true par le biais d’Ambari ou pendant la création du cluster par le biais de PowerShell ou de modèles Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 393087f4d5c5e7a52fd2dd10d20362a045a0075b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122666"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Comment configurer Apache Kafka sur HDInsight pour créer des rubriques automatiquement

Par défaut, [Apache Kafka](https://kafka.apache.org/) sur HDInsight n’active pas la création automatique de rubriques. Vous pouvez activer la création automatique de rubriques pour les clusters existants à l’aide d’[Apache Ambari](https://ambari.apache.org/). Vous pouvez également activer la création automatique de rubriques quand vous créez un cluster Kafka à l’aide d’un modèle Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interface utilisateur web d’Apache Ambari

Pour activer la création automatique de rubriques sur un cluster existant par le biais de l’interface utilisateur web d’Ambari, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le cluster Kafka.

2. Dans la __vue d’ensemble du cluster__, sélectionnez __Tableau de bord du cluster__.

    ![Image du portail avec le tableau de bord du cluster sélectionné](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Puis choisissez __Tableau de bord du cluster HDInsight__. Lorsque vous y êtes invité, authentifiez-vous à l’aide des informations d’identification de connexion (admin) pour le cluster.

    ![Image de l’entrée du tableau de bord du cluster HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Sélectionnez le service Kafka dans la liste à gauche de la page.

    ![Apache Ambari - Onglet avec la liste des services](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. Sélectionnez Configs au milieu de la page.

    ![Apache Ambari - Onglet Configs du service](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. Dans le champ Filter (Filtrer), entrez la valeur `auto.create`.

    ![Apache Ambari - Champ Filtre de recherche](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Cette option filtre la liste des propriétés et affiche le paramètre `auto.create.topics.enable`.

6. Définissez la valeur `auto.create.topics.enable` sur `true`, puis sélectionnez Save (Enregistrer). Ajoutez une note, puis resélectionnez Save (Enregistrer).

    ![Image de l’entrée auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Sélectionnez le service Kafka, choisissez __Restart__ (Redémarrer), puis __Restart all affected__ (Redémarrer tous les éléments affectés). Lorsque vous y êtes invité, sélectionnez __Confirm Restart All__ (Confirmer le redémarrage).

    ![Apache Ambari - Redémarrer tous les éléments affectés](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Vous pouvez également définir les valeurs Ambari par le biais de l’API REST d’Ambari. Cette méthode est généralement plus difficile, car vous devez effectuer plusieurs appels REST pour récupérer la configuration actuelle, la modifier, etc. Pour plus d’informations, consultez le document [Gérer des clusters HDInsight à l’aide de l’API REST d’Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="resource-manager-templates"></a>Modèles Resource Manager

Quand vous créez un cluster Kafka à l’aide d’un modèle Azure Resource Manager, vous pouvez définir directement `auto.create.topics.enable` en l’ajoutant dans un `kafka-broker`. L’extrait de code JSON suivant montre comment définir cette valeur sur `true` :

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à activer la création automatique de rubriques pour Apache Kafka sur HDInsight. Pour en savoir plus sur l’utilisation de Kafka, consultez les liens suivants :

* [Analyser les journaux d’activité Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Répliquer des données d’un cluster Kafka à un autre](apache-kafka-mirroring.md)
