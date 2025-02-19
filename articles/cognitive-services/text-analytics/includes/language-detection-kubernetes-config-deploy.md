---
title: Étapes de configuration et de déploiement Kubernetes du conteneur Détection de langue
titleSuffix: Azure Cognitive Services
description: Étapes de configuration et de déploiement Kubernetes du conteneur Détection de langue
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: e3051a72a115e711a99ecd68756967e2cef0cc04
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130056"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>Déployer le conteneur Détection de langue vers un cluster AKS

1. Ouvrir Azure CLI et se connecter à Azure.

    ```azurecli
    az login
    ```

1. Connectez-vous au cluster AKS. Remplacez `your-cluster-name` et `your-resource-group` par les valeurs appropriées.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Une fois cette commande exécutée, elle signale un message similaire au message suivant :

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Si plusieurs abonnements sont disponibles sur votre compte Azure et que la commande `az aks get-credentials` renvoie une erreur, le problème peut être que vous n’utilisez pas l’abonnement approprié. Définissez le contexte de votre session Azure CLI pour utiliser le même abonnement avec lequel vous avez créé les ressources, puis réessayez.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Ouvrez l’éditeur de texte de votre choix. L’exemple suivant utilise Visual Studio Code.

    ```azurecli
    code .
    ```

1. Dans l’éditeur de texte, créez un nouveau fichier nommé *language.yaml* et collez-y le fichier YAML suivant. Assurez-vous de remplacer les valeurs `billing/value` et `apikey/value` par vos propres informations.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. Enregistrez le fichier et fermez l’éditeur de texte.
1. Exécutez la commande Kubernetes `apply` avec le fichier *language.yaml* comme cible :

    ```console
    kuberctl apply -f language.yaml
    ```

    Une fois la configuration de déploiement correctement appliquée par la commande, un message similaire à la sortie suivante s’affiche :

    ```console
    deployment.apps "language" created
    service "language" created
    ```
1. Vérifiez que le pod a été déployé :

    ```console
    kubectl get pods
    ```

    Vous obtiendrez ainsi l’état d’exécution du pod :

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Vérifiez que le service est disponible et obtenez l’adresse IP.

    ```console
    kubectl get services
    ```

    La sortie de l’état d’exécution du service *language* dans le pod :

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
