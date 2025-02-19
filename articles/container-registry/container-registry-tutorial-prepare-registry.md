---
title: Didacticiel - Créer un registre Docker géorépliqué dans Azure
description: Créez un registre de conteneurs Azure, configurez la géoréplication, préparez une image Docker et déployez-la dans le registre. Première partie d’une série en trois parties.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 87746bd39e624699612bf5221258ad757cd462b3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309584"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Didacticiel : Préparer un registre de conteneurs Azure géorépliqué

Un registre de conteneurs Azure est un registre Docker privé déployé dans Azure que vous pouvez conserver dans un réseau proche de vos déploiements. Dans le cadre de cet ensemble de trois articles de didacticiel, vous apprendrez à utiliser la géoréplication pour déployer une application web ASP.NET Core exécutée dans un conteneur Linux dans deux instances [Web App pour conteneurs](../app-service/containers/index.yml). Vous découvrirez comment Azure déploie automatiquement l’image dans chaque instance Web App à partir du référentiel géorépliqué le plus proche.

Ce didacticiel, première partie d’une série qui en compte trois, explique comment :

> [!div class="checklist"]
> * Créer un registre de conteneurs Azure géorépliqué.
> * Cloner le code source de l’application à partir de GitHub.
> * Générer une image conteneur Docker à partir de la source de l’application.
> * Envoyer l’image conteneur à votre registre.

Dans les didacticiels suivants, vous déploierez le conteneur de votre registre privé vers une application web exécutée dans deux régions Azure. Vous mettrez à jour le code dans l’application et mettrez à jour les deux instances Web App avec une seule commande `docker push` de transmission au registre.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel requiert une installation locale d’Azure CLI (version 2.0.31 ou ultérieure). Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

Vous devez maîtriser les principaux concepts Docker tels que les conteneurs, les images de conteneur et les commandes Docker CLI de base. Pour apprendre les principes de base des conteneurs, consultez [Bien démarrer avec Docker]( https://docs.docker.com/get-started/).

Pour suivre ce didacticiel, vous avez besoin d’une installation locale de Docker. Docker fournit des instructions d’installation pour les systèmes [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ce didacticiel. Par conséquent, nous recommandons une installation locale de l’interface Azure CLI et de l’environnement de développement Docker.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Connectez-vous au [Portail Azure](https://portal.azure.com).

Sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.

![Création d’un registre de conteneur dans le portail Azure][tut-portal-01]

Configurez votre nouveau registre avec les paramètres suivants :

* **Nom du registre** : créez un nom de registre qui est globalement unique dans Azure et contient entre 5 et 50 caractères alphanumériques
* **Groupe de ressources** : **Créer un nouveau** > `myResourceGroup`
* **Emplacement** : `West US`
* **Utilisateur administrateur** : `Enable` (requis pour que Web App pour conteneurs puisse extraire des images)
* **Référence** : `Premium` (requise pour la géoréplication)

Sélectionnez **Créer** pour déployer l’instance ACR.

![Création d’un registre de conteneur dans le portail Azure][tut-portal-02]

Dans le reste de ce didacticiel, nous utilisons `<acrName>` en lieu et place du **nom du registre** de conteneurs.

> [!TIP]
> Comme les registres de conteneurs Azure sont généralement des ressources durables qui sont utilisées dans plusieurs hôtes de conteneur, nous vous recommandons de créer votre registre dans son propre groupe de ressources. Quand vous configurez des registres et des Webhooks géorépliqués, ces ressources supplémentaires sont placées dans le même groupe de ressources.
>

## <a name="configure-geo-replication"></a>Configuration de la géo-réplication

Maintenant que vous disposez d’un registre Premium, vous pouvez configurer la géoréplication. Votre application web, que vous configurerez dans le didacticiel suivant de manière qu’elle s’exécute dans deux régions, peut ensuite extraire ses images conteneur du registre le plus proche.

Accédez à votre nouveau registre de conteneurs dans le portail Azure, puis sélectionnez **Réplications** sous **SERVICES** :

![Option Réplications du registre de conteneurs dans le portail Azure][tut-portal-03]

Une carte où les régions Azure disponibles pour la géoréplication sont représentées par des hexagones verts s’affiche :

 ![Carte des régions dans le portail Azure][tut-map-01]

Répliquez votre registre dans la région USA Est en sélectionnant l’hexagone vert correspondant, puis sélectionnez **Créer** sous **Créer une réplication** :

 ![Boîte de dialogue Créer une réplication dans le portail Azure][tut-portal-04]

Une fois la réplication terminée, le portail indique l’état *Prêt* pour les deux régions. Utilisez le bouton **Actualiser** pour actualiser l’état de la réplication. La création et la synchronisation des réplicas peuvent prendre environ une minute.

![Boîte de dialogue État de la réplication dans le portail Azure][tut-portal-05]

## <a name="container-registry-login"></a>Connexion au registre de conteneurs

Maintenant que vous avez configuré la géoréplication, vous allez générer une image conteneur et l’envoyer à votre registre. Vous devez vous connecter à votre instance ACR avant de lui envoyer des images.

Utilisez la commande [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) pour vous authentifier et mettre en cache les informations d’identification de votre registre. Remplacez `<acrName>` par le nom du registre créé précédemment.

```azurecli
az acr login --name <acrName>
```

Une fois l’opération terminée, la commande renvoie `Login Succeeded`.

## <a name="get-application-code"></a>Obtenir le code d’application

L’exemple de ce didacticiel inclut une petite application web générée avec [ASP.NET Core][aspnet-core]. L’application fournit une page HTML qui affiche la région à partir de laquelle l’image a été déployée par Azure Container Registry.

![Application du didacticiel affichée dans le navigateur][tut-app-01]

Utilisez git pour télécharger l’exemple dans un répertoire local et utilisez la commande `cd` pour accéder au répertoire :

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Si vous n’avez pas installé `git`, vous pouvez [télécharger l’archive ZIP][acr-helloworld-zip] directement à partir de GitHub.

## <a name="update-dockerfile"></a>Mettre à jour le fichier Dockerfile

Le fichier Dockerfile inclus dans l’exemple montre comment le conteneur est généré. La génération du conteneur démarre à partir d’une image [aspnetcore][dockerhub-aspnetcore] officielle. Les fichiers d’application sont ensuite copiés dans le conteneur, les dépendances sont installées, la sortie est compilée à l’aide de l’image [aspnetcore build][dockerhub-aspnetcore-build] officielle et enfin, une image aspnetcore optimisée est générée.

Le fichier [Dockerfile][dockerfile] se trouve à l’emplacement `./AcrHelloworld/Dockerfile` dans la source clonée.

```Dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

L’application dans *acr-helloworld* tente de déterminer la région à partir de laquelle son conteneur a été déployé en demandant au service DNS des informations sur le serveur de connexion du registre. Vous devez spécifier le nom de domaine complet (FQDN) du serveur de connexion de votre registre dans la variable d’environnement `DOCKER_REGISTRY` du fichier Dockerfile.

Tout d’abord, obtenez le nom de domaine complet du serveur de connexion du registre à l’aide de la commande `az acr show`. Remplacez `<acrName>` par le nom du registre créé lors des étapes précédentes.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Sortie :

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Ensuite, mettez à jour la ligne `ENV DOCKER_REGISTRY` avec le nom de domaine complet du serveur de connexion de votre registre. Cet exemple reflète l’exemple de nom de registre, *uniqueregistryname* :

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Générer l’image conteneur

Maintenant que vous avez mis à jour le fichier Dockerfile avec le nom de domaine complet du serveur de connexion de votre registre, vous pouvez utiliser `docker build` pour créer l’image conteneur. Exécutez la commande suivante pour générer l’image et l’identifier à l’aide de l’URL de votre registre privé, en remplaçant une fois encore `<acrName>` par le nom de votre registre :

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Plusieurs lignes de sortie sont affichées pendant la génération de l’image Docker (seule une partie est montrée ici) :

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Utilisez `docker images` pour afficher l’image générée et référencée :

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Envoyer l’image à Azure Container Registry

Utilisez la commande `docker push` pour envoyer l’image *acr-helloworld* à votre registre. Remplacez `<acrName>` par le nom de votre registre.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Comme vous avez configuré votre registre pour la géoréplication, votre image est automatiquement répliquée dans les deux régions *USA Ouest* et *USA Est* avec cette simple commande `docker push`.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un registre de conteneurs géorépliqué privé, généré une image conteneur, puis envoyé cette image à votre registre.

Passez au didacticiel suivant pour déployer votre conteneur dans plusieurs instances Web App pour conteneurs en utilisant la géoréplication pour fournir les images localement.

> [!div class="nextstepaction"]
> [Déployer une application web à partir d’Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile