---
title: Démarrage rapide (Python) pour contrôler un appareil à partir d’Azure IoT Hub | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous exécutez deux exemples d’applications Python. Une application est une application back-end qui peut contrôler à distance des appareils connectés à votre concentrateur. L’autre application simule un appareil connecté à votre concentrateur qui peut être contrôlé à distance.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 13652b287da94adff5bdf2235900734e5908c56f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516658"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Démarrage rapide : Contrôler un appareil connecté à un hub IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub est un service Azure qui vous permet de gérer vos appareils IoT à partir du cloud et d’envoyer de gros volumes de données de télémétrie d’appareils vers le cloud à des fins de stockage et de traitement. Dans ce guide de démarrage rapide, vous utilisez une *méthode directe* pour contrôler un appareil simulé connecté à votre IoT Hub. Vous pouvez utiliser les méthodes directes pour modifier à distance le comportement d’un appareil connecté à votre IoT Hub.

Ce démarrage rapide utilise deux applications Python prédéfinies :

* Une application d’appareil simulé qui répond aux méthodes directes appelées à partir d’une application back-end. Pour recevoir les appels de méthode directe, cette application se connecte à un point de terminaison spécifique à l’appareil sur votre IoT Hub.

* Une application back-end qui appelle les méthodes directes sur l’appareil simulé. Pour appeler une méthode directe sur un appareil, cette application se connecte à un point de terminaison côté service sur votre IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et IoT Device Provisioning Service (DPS) à Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Si ce n’est déjà fait, téléchargez l’exemple de projet Python à partir de https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip et extrayez l’archive ZIP.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Si vous avez terminé le précédent [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md), vous pouvez ignorer cette étape.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Inscrire un appareil

Si vous avez terminé le précédent [Démarrage rapide : Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-python.md), vous pouvez ignorer cette étape.

Un appareil doit être inscrit dans votre hub IoT pour pouvoir se connecter. Dans ce démarrage rapide, vous utilisez Azure Cloud Shell pour inscrire un appareil simulé.

1. Exécutez les commandes suivantes dans Azure Cloud Shell pour créer l’identité d’appareil.

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    **MyPythonDevice** : il s’agit du nom de l’appareil que vous inscrivez. Il est recommandé d’utiliser **MyPythonDevice** comme indiqué. Si vous choisissez un autre nom pour votre appareil, vous devez utiliser ce nom tout au long de cet article et mettre à jour le nom de l’appareil dans les exemples d’application avant de les exécuter.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Exécutez les commandes suivantes dans Azure Cloud Shell pour obtenir la _chaîne de connexion_ à l’appareil que vous venez d’inscrire :

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Notez la chaîne de connexion à l’appareil, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide.

3. Vous avez également besoin d’une _chaîne de connexion de service_ pour activer l’application back-end afin de vous connecter à votre IoT Hub et récupérer les messages. La commande suivante récupère la chaîne de connexion de service correspondant à votre hub IoT :

    **YourIoTHubName** : Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Notez la chaîne de connexion de service, qui ressemble à ce qui suit :

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Vous utiliserez cette valeur plus loin dans ce démarrage rapide. La chaîne de connexion de service est différente de la chaîne de connexion d’appareil que vous avez notée à l’étape précédente.

## <a name="listen-for-direct-method-calls"></a>Écouter les appels de méthode directe

L’application d’appareil simulé se connecte à un point de terminaison spécifique de l’appareil sur votre IoT Hub, envoie les données de télémétrie simulée et écoute les appels de méthode directe provenant de votre concentrateur. Dans ce démarrage rapide, l’appel de méthode directe à partir du concentrateur indique à l’appareil de modifier la fréquence à laquelle il envoie des données de télémétrie. L’appareil simulé renvoie un accusé de réception à votre hub après l’exécution de la méthode directe.

1. Dans une fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Python. Ensuite, accédez au dossier **iot-hub\Quickstarts\simulated-device-2**.

1. Utilisez un éditeur de texte pour ouvrir le fichier **SimulatedDevice.py**.

    Remplacez la valeur de la variable `CONNECTION_STRING` par la chaîne de connexion d’appareil que vous avez notée précédemment. Enregistrez ensuite les changements apportés à **SimulatedDevice.py**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises pour l’application d’appareil simulé :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour démarrer l’application d’appareil simulé :

    ```cmd/sh
    python SimulatedDevice.py
    ```

    La capture d’écran suivante présente la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie à votre IoT Hub :

    ![Exécuter l’appareil simulé](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Appeler la méthode directe

L’application back-end se connecte au point de terminaison côté service sur votre IoT Hub. L’application effectue des appels de méthode directe à un appareil via votre IoT Hub et écoute les accusés de réception. Une application back-end de l’IoT Hub s’exécute généralement dans le cloud.

1. Dans une autre fenêtre de terminal local, accédez au dossier racine de l’exemple de projet Python. Ensuite, accédez au dossier **iot-hub\Quickstarts\back-end-application**.

1. Utilisez un éditeur de texte pour ouvrir le fichier **BackEndApplication.py**.

    Remplacez la valeur de la variable `CONNECTION_STRING` par la chaîne de connexion de service que vous avez notée précédemment. Ensuite, enregistrez vos modifications dans **BackEndApplication.py**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les bibliothèques requises pour l’application d’appareil simulé :

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour exécuter l’application back-end :

    ```cmd/sh
    python BackEndApplication.py
    ```

    La capture d’écran suivante montre la sortie lorsque l’application effectue un appel de méthode directe sur l’appareil et reçoit un accusé de réception :

    ![Exécuter l’application back-end](./media/quickstart-control-device-python/BackEndApplication.png)

    Après avoir exécuté l’application back-end, un message s’affiche dans la fenêtre de la console exécutant l’appareil simulé, et la fréquence à laquelle il envoie les messages change :

    ![Changement dans le client simulé](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appelé une méthode directe sur un appareil à partir d’une application back-end et répondu à l’appel de méthode directe dans une application d’appareil simulé.

Pour savoir comment acheminer les messages appareil-à-cloud vers différentes destinations dans le cloud, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Didacticiel : acheminer les données de télémétrie vers différents points de terminaison pour traitement](tutorial-routing.md)