---
title: Résolution de problèmes
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: 87aa96614b6aec4843723233a77d0a1dc1b66453
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300362"
---
# <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Ce guide contient des informations sur les problèmes courants que vous êtes susceptible de rencontrer en utilisant Azure Dev Spaces.

Si vous rencontrez un problème avec Azure Dev Spaces, créez un [problème dans le dépôt GitHub Azure Dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Avant de commencer

Pour résoudre les problèmes plus efficacement, vous pouvez créer des journaux d’activité plus détaillés.

Pour l’extension Visual Studio, affectez 1 à la variable d’environnement `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED`. N’oubliez pas de redémarrer Visual Studio pour appliquer la variable d’environnement. Une fois activés, les journaux d’activité détaillés sont écrits dans votre répertoire `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Dans l’interface CLI, vous pouvez générer plus d’informations pendant l’exécution de la commande en utilisant le commutateur `--verbose`. Vous pouvez également parcourir des journaux d’activité plus détaillés dans `%TEMP%\Azure Dev Spaces`. Sur un Mac, vous pouvez trouver le répertoire TEMP en exécutant `echo $TMPDIR` à partir d’une fenêtre de terminal. Sur un ordinateur Linux, le répertoire TEMP est généralement `/tmp`.

Azure Dev Spaces fonctionne mieux lors du débogage d’une seule instance ou d’un seul pod. Le fichier `azds.yaml` contient un paramètre, *replicaCount*, qui indique le nombre de pods que Kubernetes exécute pour votre service. Si vous changez le paramètre *replicaCount* pour configurer votre application afin d’exécuter plusieurs pods pour un service donné, le débogueur s’attache au premier pod, quand l’ordre est alphabétique. Le débogueur s’attache à un autre pod une fois que le pod d’origine est recyclé, ce qui peut provoquer un comportement inattendu.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Problèmes courants lors de l’activation d’Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erreur « Échec de la création du contrôleur Azure Dev Spaces »

Vous pouvez voir cette erreur lorsqu’il y a un problème de création du contrôleur. Dans le cas d’une erreur temporaire, supprimez et recréez le contrôleur pour la corriger.

Vous pouvez également essayer de supprimer le contrôleur :

```bash
azds remove -g <resource group name> -n <cluster name>
```

Utilisez l’interface CLI Azure Dev Spaces pour supprimer un contrôleur. La suppression d’un contrôleur n’est pas possible à partir de Visual Studio. Vous ne pouvez pas non plus supprimer un contrôleur à partir d’Azure Cloud Shell étant donné que vous ne pouvez pas installer la CLI Azure Dev Spaces dans Azure Cloud Shell.

Si vous n’avez pas installé la CLI Azure Dev Spaces, installez-la à l’aide de la commande suivante avant de supprimer le contrôleur :

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

La recréation du contrôleur est possible à partir de CLI ou de Visual Studio. Pour obtenir des exemples, consultez les guides de démarrage rapide [Développement en équipe](quickstart-team-development.md) ou [Développer avec .NET Core](quickstart-netcore-visualstudio.md).

### <a name="controller-create-failing-because-of-controller-name-length"></a>Échec de la création du contrôleur en raison de la longueur du nom de contrôleur

Le nom d’un contrôleur Azure Dev Spaces ne peut pas comporter plus de 31 caractères. Si le nom de votre contrôleur dépasse cette limite de caractères, quand vous activez Dev Spaces sur un cluster AKS ou créez un contrôleur, vous recevez une erreur. Par exemple :

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Pour résoudre ce problème, créez un contrôleur en choisissant un autre nom. Par exemple :

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Échec de l’activation de Dev Spaces quand des pools de nœuds Windows sont ajoutés à un cluster AKS

Actuellement, Azure Dev Spaces peut uniquement être exécuté sur des pods et nœuds Linux. Lorsque vous disposez d’un cluster AKS avec un pool de nœuds Windows, vous devez vous assurer que les pods Azure Dev Spaces sont planifiés uniquement sur des nœuds Linux. Si un pod Azure Dev Spaces est planifié pour s’exécuter sur un nœud Windows, ce pod ne démarre pas et l’activation de Dev Spaces échoue.

Pour résoudre ce problème, [ajoutez une teinte](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) à votre cluster AKS pour vous assurer que les pods Linux ne sont pas planifiés pour être exécutés sur un nœud Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erreur « Aucun nœud Linux non teinté trouvé à l’état Prêt sur le cluster. Il doit exister au moins un nœud Linux non teinté à l’état Prêt pour déployer des pods dans l’espace de noms « azds ». »

Azure Dev Spaces ne peut pas créer de contrôleur sur votre cluster AKS, car il n’a pas trouvé de nœud non teinté à l’état *Prêt* sur lequel planifier des pods. Azure Dev Spaces requiert au moins un nœud Linux à l’état *Prêt* permettant la planification des pods sans spécifier de tolérances.

Pour résoudre ce problème, [mettez à jour la configuration de teinte](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) sur votre cluster AKS pour vous assurer qu’au moins un nœud Linux permet la planification de pods sans spécifier de tolérances. Assurez-vous qu’au moins un nœud Linux permettant la planification des pods sans spécifier de tolérances est à l’état *Prêt*. Si votre nœud prend beaucoup de temps à atteindre l’état *Prêt*, vous pouvez essayer de redémarrer le nœud.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erreur « Interface CLI Azure Dev Spaces pas correctement installée » lors de l’exécution de `az aks use-dev-spaces`

Une mise à jour de l’interface CLI Azure Dev Spaces a modifié le chemin d’accès de l’installation. Si vous utilisez une version de l’interface Azure CLI antérieure à 2.0.63, cette erreur peut s’afficher. Pour afficher votre version de l’interface CLI, utilisez `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

L’installation peut réussir malgré le message d’erreur lors de l’exécution de `az aks use-dev-spaces` avec une version d’Azure CLI antérieure à 2.0.63. Vous pouvez continuer à utiliser `azds` sans aucun problème.

Pour résoudre ce problème, mettez à jour votre installation de l’interface [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vers 2.0.63 ou une version ultérieure. Cette mise à jour permettra de résoudre le message d’erreur que vous recevez lors de l’exécution de `az aks use-dev-spaces`. Vous pouvez également continuer à utiliser votre version actuelle de l’interface Azure CLI et de l’interface CLI Azure Dev Spaces.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problèmes courants lors de la préparation de votre projet pour Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Avertissement « Impossible de générer Dockerfile en raison d’un langage non pris en charge »
Azure Dev Spaces fournit une prise en charge native de C# et Node.js. Quand vous exécutez `azds prep` dans un répertoire contenant du code écrit dans l’un de ces langages, Azure Dev Spaces crée automatiquement le fichier Dockerfile approprié pour vous.

Vous pouvez toujours utiliser Azure Dev Spaces avec du code écrit dans d’autres langages, mais vous devez alors créer manuellement le fichier Dockerfile avant d’exécuter `azds up` pour la première fois.

Si votre application est écrite dans un langage qu’Azure Dev Spaces ne prend pas nativement en charge, vous devez fournir un fichier Dockerfile approprié pour générer une image conteneur qui exécute votre code. Docker fournit la [liste des bonnes pratiques pour écrire des fichiers Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) et des [informations de référence sur Dockerfile](https://docs.docker.com/engine/reference/builder/) qui peuvent vous aider à écrire un fichier Dockerfile qui répond à vos besoins.

Une fois que vous avez le fichier Dockerfile approprié, vous exécutez `azds up` pour exécuter votre application dans Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problèmes courants lors du démarrage ou de l’arrêt des services avec Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Erreur « Fichier de configuration introuvable »

Lors de l’exécution de `azds up`, cette erreur peut s’afficher. `azds up` et `azds prep` doivent être exécutés à partir du répertoire racine du projet que vous voulez exécuter dans votre espace de développement.

Pour résoudre ce problème :
1. Remplacez le répertoire actuel par le dossier racine contenant le code de votre service. 
1. Si le dossier du code ne contient pas de fichier _azds.yaml_, exécutez `azds prep` pour générer des ressources Docker, Kubernetes et Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Expiration du délai à l’étape « En attente de la build de l’image conteneur... » avec des nœuds virtuels AKS

Ce délai d’attente se produit quand vous essayez d’utiliser Dev Spaces afin d’exécuter un service configuré pour s’exécuter sur un [nœud virtuel AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Actuellement, Dev Spaces ne gère pas les services de build ou de débogage sur des nœuds virtuels.

Si vous exécutez `azds up` avec le commutateur `--verbose` ou si vous activez la journalisation détaillée dans Visual Studio, vous obtenez des informations supplémentaires :

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

La commande ci-dessus indique que le pod du service a été assigné à *virtual-node-aci-linux*, qui est un nœud virtuel.

Pour résoudre ce problème, modifiez le graphique Helm du service de manière à supprimer toutes les valeurs *nodeSelector* ou *tolerations* qui permettent au service de s’exécuter sur un nœud virtuel. Ces valeurs sont généralement définies dans le fichier `values.yaml` du graphique.

Vous pouvez quand même utiliser un cluster AKS avec la fonctionnalité des nœuds virtuels activée, si le service que vous voulez générer ou déboguer par le biais de Dev Spaces s’exécute sur un nœud de machine virtuelle. L’exécution d’un service avec Dev Spaces sur un nœud de machine virtuelle est la configuration par défaut.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Erreur « Aucun pod Tiller prêt n’a été trouvé » lors du lancement de Dev Spaces

Cette erreur se produit lorsque le client Helm ne peut plus communiquer avec le pod Tiller exécuté dans le cluster.

Pour résoudre ce problème, redémarrez les nœuds de l’agent dans votre cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Erreur « Échec de la mise en production du service azds-\<identificateur\>-\<nomespace\>-\<nomservice\> : '\<nomservice\>' existe déjà » ou « Refus de l’accès pull pour \<nomservice\> ; le référentiel n’existe pas ou nécessite 'docker login' »

Ces erreurs peuvent se produire si vous exécutez une combinaison de commandes Helm directes (comme `helm install`, `helm upgrade` ou `helm delete`) et de commandes Dev Spaces (comme `azds up` et `azds down`) dans le même espace de développement. Ces erreurs sont dues au fait que Dev Spaces utilise sa propre instance Tiller, qui est en conflit avec votre instance Tiller exécutée dans le même espace de développement.

Il est possible d’utiliser à la fois des commandes Helm et des commandes Dev Spaces dans le même cluster AKS, mais chaque espace de noms où Dev Spaces est activé doit utiliser l’une ou l’autre de ces commandes.

Par exemple, supposons que vous utilisez une commande Helm pour exécuter votre application entière dans un espace de développement parent. Vous pouvez créer des espaces de développement enfants à partir de ce parent, utiliser ensuite Dev Spaces pour exécuter individuellement les services dans ces espaces de développement enfants, puis tester tous les services ensemble. Quand vous êtes prêt à enregistrer vos modifications, utilisez une commande Helm pour déployer le code mis à jour dans l’espace de développement parent. N’utilisez pas `azds up` pour exécuter le service mis à jour dans l’espace de développement parent, car cela provoquerait un conflit avec le service initialement exécuté à l’aide d’une commande Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Le fichier Dockerfile existant n’est pas utilisé pour générer un conteneur

Azure Dev Spaces peut être configuré pour pointer vers un fichier _Dockerfile_ spécifique de votre projet. S’il s’avère qu’Azure Dev Spaces n’utilise pas le fichier _Dockerfile_ attendu pour générer vos conteneurs, vous devrez peut-être indiquer explicitement à Azure Dev Spaces quel fichier Dockerfile utiliser. 

Pour résoudre ce problème, ouvrez le fichier _azds.yaml_ généré par Azure Dev Spaces dans votre projet. Modifiez la directive *configurations: develop: build: dockerfile* de sorte qu’elle pointe vers le fichier Dockerfile à utiliser. Par exemple :

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erreur « non autorisé : authentification requise » quand vous essayez d’utiliser une image Docker à partir d’un registre privé

Vous utilisez une image Docker à partir d’un registre privé qui nécessite une authentification.

Pour résoudre ce problème, vous pouvez autoriser Dev Spaces à authentifier et extraire des images à partir de ce registre privé à l’aide d’[imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Pour utiliser imagePullSecrets, [créez un secret Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) dans l’espace de noms où vous utilisez l’image. Fournissez ensuite le secret en tant qu’imagePullSecret dans `azds.yaml`.

Voici un exemple de spécification d’imagePullSecrets dans `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> La définition d’imagePullSecrets dans `azds.yaml` remplace les imagePullSecrets spécifié dans `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Erreur « Impossible de démarrer le service. »

Vous pouvez voir cette erreur lorsque votre code de service ne parvient pas à démarrer. La cause se trouve souvent dans le code utilisateur. Pour obtenir des informations supplémentaires sur le diagnostic, activez la journalisation détaillée lors du démarrage de votre service.

Dans la ligne de commande, utilisez l’élément `--verbose` pour activer la journalisation détaillée. Vous pouvez également spécifier un format de sortie à l’aide de `--output`. Par exemple :

```cmd
azds up --verbose --output json
```

Dans Visual Studio :

1. Ouvrez **Outils > Options** et, sous **Projets et solutions**, choisissez **Générer et exécuter**.
2. Remplacez les paramètres de **Commentaires relatifs à la sortie de build du projet MSBuild** par **Détails** ou **Diagnostic**.

    ![Capture d’écran de la boîte de dialogue Outils Options](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Réexécution d’un service après la recréation d’un contrôleur

Vous recevez un message d’erreur *Impossible de démarrer le service*  lorsque vous essayez de réexécuter un service après avoir supprimé, puis recréé le contrôleur Azure Dev Spaces associé à ce cluster. Dans ce cas, la sortie détaillée contient le texte suivant :

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Cette erreur est due au fait que la suppression du contrôleur Dev Spaces ne supprime pas les services précédemment installés par ce contrôleur. La recréation du contrôleur suivie de la tentative d’exécution des services à l’aide du nouveau contrôleur se solde par un échec car les anciens services sont toujours en place.

Pour remédier à ce problème, utilisez la commande `kubectl delete` afin de supprimer manuellement les anciens services de votre cluster, puis réexécutez Dev Spaces pour installer les nouveaux services.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Erreur « Impossible de démarrer le service. » lors de l’utilisation de fichiers Dockerfile à plusieurs étapes

Vous recevez un message d’erreur *Impossible de démarrer le service* lors de l’utilisation d’un fichier Dockerfile multiphase. Dans ce cas, la sortie détaillée contient le texte suivant :

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Cette erreur est due au fait que les nœuds AKS exécutent une version antérieure de Docker qui ne prend pas en charge les builds à plusieurs étapes. Pour éviter les builds multiphases, réécrivez votre fichier Dockerfile.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Problèmes courants liés à l’utilisation de Visual Studio et de Visual Studio Code avec Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Erreur « Les outils et configurations nécessaires sont manquants »

Cette erreur peut se produire lors du lancement de VS Code : « [Azure Dev Spaces] Les outils et configurations nécessaires pour générer et déboguer '[nom du projet]' sont manquants ».
L’erreur signifie que azds.exe ne se trouve pas dans la variable d’environnement PATH, comme indiqué dans VS Code.

Essayez de lancer VS Code à partir d’une invite de commandes dans laquelle la variable d’environnement PATH est correctement définie.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Message d’erreur « Required tools to build and debug ’nom_projet’ are out of date » (« Les outils nécessaires à la compilation et au débogage de ’nom_projet’ ne sont plus à jour »).

Cette erreur se produit dans Visual Studio Code si vous avez une version plus récente de l’extension VS Code pour Azure Dev Spaces et une version plus ancienne de l’interface de ligne de commande Azure Dev Spaces.

Téléchargez et installez la dernière version de l’interface CLI Azure Dev Spaces :

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Error: « Impossible de trouver l’extension du débogueur pour le type: coreclr »

Cette erreur peut s’afficher lors de l’exécution du débogueur Visual Studio Code. L’extension VS Code pour C# n’est probablement pas installée sur votre ordinateur de développement. L’extension C# inclut la prise en charge du débogage pour .NET Core (CoreCLR).

Pour résoudre ce problème, installez [l’extension VS Code pour C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Erreur « Le type de débogage configuré 'coreclr' n’est pas pris en charge »

Cette erreur peut s’afficher lors de l’exécution du débogueur Visual Studio Code. L’extension VS Code pour Azure Dev Spaces n’est probablement pas installée sur votre ordinateur de développement.

Pour résoudre ce problème, installez l’[extension VS Code pour Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erreur « Valeur 'cwd' '/src' non valide. Le système ne peut pas trouver le fichier spécifié. » ou «launch: program ’/src/ [chemin d’accès au fichier binaire du projet]’ n’existe pas »

Cette erreur peut s’afficher lors de l’exécution du débogueur Visual Studio Code. Par défaut, l’extension VS Code utilise `src` comme répertoire de travail du projet sur le conteneur. Si vous avez mis à jour votre `Dockerfile` pour spécifier un répertoire de travail différent, vous pouvez voir cette erreur.

Pour résoudre ce problème, mettez à jour le fichier `launch.json` dans le sous-répertoire `.vscode` de votre dossier de projet. Changez la directive `configurations->cwd` pour pointer vers le même répertoire que le `WORKDIR` défini dans le `Dockerfile` de votre projet. Vous devrez peut-être aussi mettre à jour la directive `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erreur : « Le programme canal 'azds' s’est arrêté de façon inattendue avec le code 126. »

Cette erreur peut s’afficher lors de l’exécution du débogueur Visual Studio Code.

Pour résoudre ce problème, fermez et rouvrez Visual Studio Code. Redémarrez le débogueur.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erreur « Échec de la surveillance interne : regardez ENOSPC » lors de l’attachement d’un débogueur à une application Node.js

Cette erreur s’affiche lorsque le nœud qui exécute le pod avec l’application Node.js à laquelle vous tentez d’attacher un débogueur a dépassé la valeur *fs.inotify.max_user_watches*. Dans certains cas, [la valeur par défaut *fs.inotify.max_user_watches* est trop petite pour gérer l’attachement direct d’un débogueur à un pod](https://github.com/Azure/AKS/issues/772).

Une solution de contournement temporaire pour ce problème consiste à augmenter la valeur de *fs.inotify.max_user_watches* sur chaque nœud du cluster, puis à redémarrer ce nœud pour appliquer les changements.

## <a name="other-common-issues"></a>Autres problèmes courants

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>L’erreur « azds » n’est pas reconnue en tant que programme exécutable, fichier de commandes ou commande interne ou externe

Vous risquez de voir cette erreur si `azds.exe` n’est pas installé ou configuré correctement.

Pour résoudre ce problème :

1. Vérifiez l’emplacement %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI pour `azds.exe`. S’il y figure, ajoutez cet emplacement à la variable d’environnement PATH.
2. Si `azds.exe` n’est pas installé, exécutez la commande suivante :

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Erreur d’autorisation « Microsoft.DevSpaces/register/action »

Vous avez besoin d’un accès *Propriétaire* ou *Contributeur* dans votre abonnement Azure pour gérer Azure Dev Spaces. Si vous tentez de gérer Dev Spaces alors que vous n’avez pas d’accès *Propriétaire* ou *Contributeur* à l’abonnement Azure associé, une erreur d’autorisation peut s’afficher. Par exemple :

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Pour résoudre ce problème, à l’aide d’un compte bénéficiant d’un accès *Propriétaire* ou *Contributeur* à l’abonnement Azure, inscrivez manuellement l’espace de noms `Microsoft.DevSpaces` :

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Les nouveaux pods ne démarrent pas

L’initialiseur Kubernetes ne peut pas appliquer le PodSpec pour les nouveaux pods en raison des changements d’autorisation RBAC apportés au rôle *administrateur de cluster* dans le cluster. Le nouveau pod a peut-être également un PodSpec non valide ; par exemple, le compte de service associé au pod n’existe plus. Pour voir les pods qui sont *En attente* en raison du problème d’initialiseur, utilisez la commande `kubectl get pods` :

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ce problème peut impacter les pods dans *tous les espaces de noms* sur le cluster, y compris les espaces de noms où Azure Dev Spaces n’est pas activé.

Pour résoudre ce problème, [mettez à jour la CLI Dev Spaces vers la dernière version](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools), puis supprimez *azds InitializerConfiguration* dans le contrôleur Azure Dev Spaces :

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Une fois que vous avez supprimé *azds InitializerConfiguration* du contrôleur Azure Dev Spaces, utilisez `kubectl delete` pour supprimer tous les pods ayant l’état *En attente*. Après cela, redéployez vos pods.

Si les nouveaux pods sont toujours bloqués dans l’état *En attente* après un redéploiement, utilisez `kubectl delete` pour supprimer tous les pods *En attente*. Quand vous avez supprimé tous les pods en attente, supprimez le contrôleur dans le cluster, puis réinstallez-le :

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Après avoir réinstallé votre contrôleur, redéployez vos pods.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Autorisations RBAC incorrectes pour appeler les API et le contrôleur Dev Spaces

L’utilisateur qui tente d’accéder au contrôleur Azure Dev Spaces doit avoir un accès en lecture sur le *kubeconfig* administrateur dans le cluster AKS. Par exemple, cette autorisation est disponible dans le [rôle administrateur intégré de cluster du service Azure Kubernetes](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Pour accéder au contrôleur Azure Dev Spaces, l’utilisateur doit également avoir le rôle RBAC *Contributeur* ou *Propriétaire* sur le contrôleur. Pour plus d’informations sur la mise à jour des autorisations utilisateur dans un cluster AKS, consultez [cet article](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Pour mettre à jour le rôle RBAC d’un utilisateur sur le contrôleur :

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Accédez au groupe de ressources contenant le contrôleur, qui est généralement le même que votre cluster AKS.
1. Cochez la case *Afficher les types masqués*.
1. Cliquez sur le contrôleur.
1. Ouvrez le volet *Contrôle d’accès (IAM)* .
1. Cliquez sur l’onglet *Attributions de rôles*.
1. Cliquez sur *Ajouter*, puis sur *Ajouter une attribution de rôle*.
    * Dans *Rôle*, sélectionnez *Contributeur* ou *Propriétaire*.
    * Pour *Attribuer l’accès à*, sélectionnez *Utilisateur, groupe ou principal du service Azure AD*.
    * Dans *Sélectionner*, recherchez l’utilisateur auquel vous souhaitez accorder des autorisations.
1. Cliquez sur *Enregistrer*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Échec de la résolution de nom DNS pour une URL publique associée à un service Dev Spaces

Vous pouvez configurer un point de terminaison d’URL publique pour votre service en spécifiant le commutateur `--public` dans la commande `azds prep` ou en cochant la case `Publicly Accessible` dans Visual Studio. Le nom DNS public est automatiquement inscrit lorsque vous exécutez votre service dans Dev Spaces. Si ce nom DNS n’est pas inscrit, vous voyez un message d’erreur *Impossible d’afficher la page* ou *Impossible d’accéder au site* dans votre navigateur web lors de la connexion à l’URL publique.

Pour résoudre ce problème :

* Vérifiez l’état de toutes les URL associées à vos services Dev Spaces :

  ```console
  azds list-uris
  ```

* Si une URL présente l’état *En attente*, cela signifie que Dev Spaces attend toujours que l’inscription DNS se termine. L’inscription peut parfois prendre quelques minutes. Dev Spaces ouvre également un tunnel localhost pour chaque service, vous pouvez utiliser pendant l’attente d’inscription DNS.
* Si une URL reste à l’état *En attente* pendant plus de 5 minutes, cela peut indiquer un problème lié au pod DNS externe créant le point de terminaison public ou au pod du contrôleur d’entrée nginx assurant l’acquisition du point de terminaison public. Utilisez les commandes suivantes pour supprimer ces pods et permettre à AKS de les recréer automatiquement :
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erreur « erreur de connexion en amont ou déconnexion/réinitialisation avant les en-têtes »

Vous pouvez obtenir cette erreur en tentant d’accéder à votre service. Par exemple, quand vous accédez à l’URL du service dans un navigateur. Cette erreur signifie que le port du conteneur n’est pas disponible. Elle peut être due aux raisons suivantes :

* Le conteneur est toujours en cours de génération et de déploiement. Ce problème peut survenir si vous exécutez `azds up` ou démarrez le débogueur, puis que vous tentez d’accéder au conteneur avant que celui-ci ait été correctement déployé.
* La configuration du port n’est pas cohérente dans le fichier _Dockerfile_, le chart Helm et le code serveur chargé d’ouvrir un port.

Pour résoudre ce problème :

1. Si le conteneur est en cours de génération et/ou de déploiement, vous pouvez patienter 2 à 3 secondes avant d’essayer à nouveau d’accéder au service. 
1. Vérifiez la configuration du port. Les numéros de port spécifiés doivent être **identiques** dans toutes les ressources suivantes :
    * **Dockerfile :** Spécifié par l’instruction `EXPOSE`.
    * **[Graphique Helm](https://docs.helm.sh) :** Spécifié par les valeurs `externalPort` et `internalPort` d’un service (souvent situées dans un fichier `values.yml`),
    * Ports ouverts dans le code d’application, par exemple dans Node.js : `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Le type ou le nom d’espace de noms « MyLibrary » est introuvable

Un projet de bibliothèque que vous utilisez est introuvable. Avec Dev Spaces, le contexte de build est au niveau du projet/service par défaut.  

Pour résoudre ce problème :

1. Modifiez le fichier `azds.yaml` de manière à définir le contexte de build au niveau de la solution.
2. Modifiez les fichiers `Dockerfile` et `Dockerfile.develop` de sorte qu’ils fassent référence aux fichiers du projet, par exemple `.csproj`, qui fait référence au nouveau contexte de build.
3. Ajoutez un `.dockerignore` dans le même répertoire que le fichier `.sln`.
4. Ajoutez à `.dockerignore` les entrées nécessaires.

Vous trouverez un exemple [ici](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>La mise à l’échelle automatique du pod horizontal ne fonctionne pas dans un espace de développement

Lorsque vous exécutez un service dans un espace de développement, le pod de ce service est [injecté avec des conteneurs supplémentaires pour l’instrumentation](how-dev-spaces-works.md#prepare-your-aks-cluster), et tous les conteneurs d’un pod doivent avoir des limites et des requêtes de ressources définies pour la mise à l’échelle automatique de pod horizontale.

Pour résoudre ce problème, appliquez une limite et une requête de ressource aux conteneurs Dev Spaces injectés. Les requêtes et les limites de ressources peuvent être appliquées pour le conteneur injecté (devspaces-proxy) en ajoutant l’annotation `azds.io/proxy-resources` à la spécification de votre pod. La valeur doit être définie sur un objet JSON représentant la section des ressources de la spécification du conteneur pour le proxy.

Voici un exemple d’annotation de ressources proxy qui doit être appliquée aux spécifications de votre pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```