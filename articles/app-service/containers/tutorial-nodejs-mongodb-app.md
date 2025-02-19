---
title: Node.js (MEAN.js) avec MongoDB sur Linux - Azure App Service | Microsoft Docs
description: Apprenez comment faire fonctionner une application Node.js dans Azure App Service sur Linux, avec une connexion à une base de données Cosmos DB, via une chaîne de connexion MongoDB. MEAN.js est utilisé dans le tutoriel.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: cf145e04ca0e0ddf336521e72f6dc230dc8fc86b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024958"
---
# <a name="build-a-nodejs-and-mongodb-app-in-azure-app-service-on-linux"></a>Générer une application Node.js et MongoDB dans Azure App Service sur Linux

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Linux. Pour effectuer un déploiement dans App Service sur _Windows_, consultez [Générer une application Node.js et MongoDB dans Azure](../app-service-web-tutorial-nodejs-mongodb-app.md).
>

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce tutoriel montre comment créer une application Node.js, la connecter localement à une base de données MongoDB, puis la déployer dans une base de données dans l’API Azure Cosmos DB pour MongoDB. Quand vous aurez terminé, vous disposerez d’une application MEAN (MongoDB, Express, AngularJS et Node.js) exécutée dans App Service sur Linux. Pour plus de simplicité, l’exemple d’application utilise [l’infrastructure de développement web MEAN.js](https://meanjs.org/).

![Application MEAN.js exécutée dans Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données à l’aide de l’API Azure Cosmos DB pour MongoDB
> * Connecter une application Node.js à MongoDB
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

1. [Installez Git](https://git-scm.com/)
2. [Installez Node.js version 6.0 ou ultérieure et NPM](https://nodejs.org/)
3. [Installer Gulp.js](https://gulpjs.com/) (requis par [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
4. [Installez et exécutez MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)

## <a name="test-local-mongodb"></a>Tester la base de données MongoDB locale

Ouvrez la fenêtre du terminal et `cd` dans le répertoire `bin` de votre installation MongoDB. Vous pouvez utilisez cette fenêtre de terminal pour exécuter toutes les commandes de ce didacticiel.

Exécutez `mongo` dans le terminal pour vous connecter à votre serveur MongoDB local.

```bash
mongo
```

Si la connexion est établie, cela signifie que votre base de données MongoDB est déjà en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données MongoDB locale est démarrée en suivant les étapes de la procédure [d’installation de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). Souvent, MongoDB est installé, mais vous devez néanmoins le démarrer en exécutant `mongod`.

Lorsque vous avez fini de tester votre base de données MongoDB, tapez `Ctrl+C` dans le terminal.

## <a name="create-local-nodejs-app"></a>Créer une application Node.js locale

Cette étape consiste à configurer le projet Node.js local.

### <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Dans la fenêtre de terminal, `cd` vers un répertoire de travail.

Exécutez la commande suivante pour cloner l’exemple de référentiel :

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Cet exemple de référentiel contient une copie du référentiel [MEAN.js](https://github.com/meanjs/mean). Il est modifié pour s’exécuter sous App Service (pour plus d’informations, consultez le fichier [Lisez-moi](https://github.com/Azure-Samples/meanjs/blob/master/README.md) du référentiel MEAN.js).

### <a name="run-the-application"></a>Exécution de l'application

Exécutez la commande suivante pour installer les packages requis et démarrer l’application.

```bash
cd meanjs
npm install
npm start
```

Ignorez l’avertissement config.domain. Lorsque l’application est entièrement chargée, vous obtenez un message similaire à celui-ci :

```txt
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Dans un navigateur, accédez à `http://localhost:3000`. Cliquez sur **S’inscrire** dans le menu supérieur et créez un utilisateur test. 

L’exemple d’application MEAN.js stocke les données utilisateur dans la base de données. Si vous parvenez à créer un utilisateur et à vous connecter, votre application écrit les données dans la base de données MongoDB locale.

![MEAN.js se connecte correctement à MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Sélectionnez **Admin > Manage Articles** (Administrateur > Gérer les articles) pour ajouter des articles.

Pour arrêter Node.js à tout moment, appuyez sur `Ctrl+C` dans le terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Créer une base de données MongoDB de production

Au cours de cette étape, vous allez créer un compte de base de données à l’aide de l’API Azure Cosmos DB pour MongoDB. Lorsque votre application est déployée sur Azure, elle utilise cette base de données cloud.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>Création d’un compte Cosmos DB

Dans Cloud Shell, créez un compte Cosmos DB à l’aide de la commande [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create).

Dans la commande suivante, remplacez l’espace réservé *\<cosmosdb-name>* par un nom unique Cosmos DB. Ce nom est utilisé en tant que point de terminaison Cosmos DB, `https://<cosmosdb-name>.documents.azure.com/`. Pour cette raison, le nom doit être unique sur l’ensemble des comptes Cosmos DB dans Azure. Le nom ne peut contenir que des minuscules, des chiffres, le tiret -) et doit compter entre 3 et 50 caractères.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Le paramètre *--kind MongoDB* prend en charge les connexions clientes MongoDB.

Une fois le compte Cosmos DB créé, Azure CLI affiche des informations similaires à celles de l’exemple suivant :

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "failoverPolicies":
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-configured-with-azure-cosmos-dbs-api-for-mongodb"></a>Connecter en production l’application configurée avec l’API Azure Cosmos DB pour MongoDB

Pendant cette étape, vous connectez votre exemple d’application MEAN.js à la base de données Cosmos DB que vous venez de créer, en utilisant une chaîne de connexion MongoDB.

### <a name="retrieve-the-database-key"></a>Récupérer la clé de la base de données

Pour se connecter à la base de données Cosmos DB, vous avez besoin de la clé de la base de données. Dans Cloud Shell, utilisez la commande [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) pour récupérer la clé primaire.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

L’interface de ligne de commande Azure montre des informations semblables à ce qui suit :

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copiez la valeur de `primaryMasterKey`. Vous aurez besoin de ces informations dans l’étape suivante.

<a name="devconfig"></a>

### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurer la chaîne de connexion dans votre application Node.js

Dans votre référentiel MEAN.js local, dans le dossier _config/env/_ , créez un fichier nommé _local-production.js_. _.gitignore_ est configuré pour conserver ce fichier en dehors du référentiel.

Copiez-y le code ci-après. Veillez à remplacer les deux espaces réservés *\<cosmosdb-name>* par le nom de votre base de données Cosmos DB, et remplacez l’espace réservé *\<primary-master-key>* par la clé que vous avez copiée à l’étape précédente.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

L’option `ssl=true` est requise, car [Cosmos DB nécessite SSL](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements).

Enregistrez vos modifications.

### <a name="test-the-application-in-production-mode"></a>Tester l’application en mode de production

Dans une fenêtre de terminal local, exécutez la commande suivante pour réduire et regrouper des scripts pour l’environnement de production. Ce processus génère les fichiers nécessaires à l’environnement de production.

```bash
gulp prod
```

Dans une fenêtre de terminal local, exécutez la commande suivante pour utiliser la chaîne de connexion que vous avez configurée dans _config/env/local-production.js_. Ignorez l’erreur de certificat et l’avertissement config.domain.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` définit la variable d’environnement qui indique à Node.js de s’exécuter dans l’environnement de production.  `node server.js` démarre le serveur Node.js avec `server.js` à la racine du référentiel. Voici comment est chargée votre application Node.js dans Azure.

Lorsque l’application est chargée, assurez-vous qu’elle s’exécute dans l’environnement de production :

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Dans un navigateur, accédez à `http://localhost:8443`. Cliquez sur **S’inscrire** dans le menu supérieur et créez un utilisateur test. Si vous parvenez à créer un utilisateur et à vous connecter, votre application écrit les données dans la base de données Cosmo DB dans Azure.

Dans le terminal, arrêtez Node.js en tapant `Ctrl+C`.

## <a name="deploy-app-to-azure"></a>Déployer des applications dans Azure

Dans cette étape, vous déployez votre application Node.js dans Azure App Service.

### <a name="configure-local-git-deployment"></a>Configurer le déploiement Git local

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>

### <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurer une variable d’environnement

Par défaut, le projet MEAN.js conserve _config/env/local-production.js_ hors du référentiel Git. Ainsi, pour votre application Azure, vous utilisez des paramètres d’application pour définir votre chaîne de connexion MongoDB.

Pour définir les paramètres de l’application, utilisez la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell.

L’exemple suivant configure un paramètre d’application `MONGODB_URI` dans votre application Azure. Remplacez les espaces réservés *\<app-name>* , *\<cosmosdb-name>* , et *\<primary-master-key>* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

Dans le code Node.js, vous [accédez à ce paramètre d’application](configure-language-nodejs.md#access-environment-variables) avec `process.env.MONGODB_URI`, comme vous accéderiez à n’importe quelle variable d’environnement.

Dans votre référentiel MEAN.js local, ouvrez _config/env/production.js_ (et non pas _config/env/local-production.js_), qui a une configuration propre à l’environnement de production. L’application MEAN.js par défaut est déjà configurée pour utiliser la variable d’environnement `MONGODB_URI` que vous avez créée.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

Vous remarquerez peut-être que le processus de déploiement exécute [Gulp](https://gulpjs.com/) après `npm install`. App Service n’exécute pas les tâches Gulp ou Grunt pendant le déploiement ; cet exemple de référentiel possède donc deux fichiers supplémentaires dans son répertoire racine pour l’activer :

- _.deployment_ : ce fichier indique à App Service d’exécuter `bash deploy.sh` en tant que script de déploiement personnalisé.
- _deploy.sh_ : le script de déploiement personnalisé. Si vous examinez le fichier, vous verrez qu’il exécute `gulp prod` après `npm install` et `bower install`.

Vous pouvez utiliser cette approche pour ajouter une étape à votre déploiement Git. Si vous redémarrez votre application Azure à un moment donné, App Service ne réexécute pas ces tâches d’automatisation. Pour plus d’informations, consultez [Exécuter Grunt/Bower/Gulp](configure-language-nodejs.md#run-gruntbowergulp).

### <a name="browse-to-the-azure-app"></a>Accéder à l’application Azure

Accédez à l’application déployée à l’aide de votre navigateur web.

```bash
http://<app-name>.azurewebsites.net
```

Cliquez sur **S’inscrire** dans le menu supérieur et créez un utilisateur fictif.

Si vous réussissez que l’application se connecte automatiquement à l’utilisateur créé, cela signifie que votre application MEAN.js dans Azure dispose d’une connectivité à l’API Azure Cosmos DB pour MongoDB.

![Application MEAN.js exécutée dans Azure App Service](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Sélectionnez **Admin > Manage Articles** (Administrateur > Gérer les articles) pour ajouter des articles.

**Félicitations !** Vous exécutez une application Node.js orientée données dans Azure App Service sur Linux.

## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, vous allez changer le modèle de données `article` et publier vos modifications dans Azure.

### <a name="update-the-data-model"></a>Mettre à jour le modèle de données

Dans votre référentiel MEAN.js local, ouvrez _modules/articles/server/models/article.server.model.js_.

Dans `ArticleSchema`, ajoutez un type `String` appelé `comment`. Lorsque vous aurez terminé, votre code de schéma doit ressembler à ceci :

```javascript
let ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Mettre à jour le code d’articles

Mettez à jour le reste de votre code `articles` afin d’utiliser `comment`.

Vous avez cinq fichiers à modifier : le contrôleur de serveur et les quatre vues clientes. 

Ouvrez _modules/articles/server/controllers/articles.server.controller.js_.

Dans la fonction `update`, ajoutez une affectation de `article.comment`. Le code suivant illustre la fonction `update` achevée :

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Ouvrez _modules/articles/client/views/view-article.client.view.html_.

Juste au-dessus de la balise de fermeture `</section>`, ajoutez la ligne suivante pour afficher `comment` avec le reste des données de l’article :

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Ouvrez _modules/articles/client/views/list-articles.client.view.html_.

Juste au-dessus de la balise de fermeture `</a>`, ajoutez la ligne suivante pour afficher `comment` avec le reste des données de l’article :

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Ouvrez _modules/articles/client/views/admin/list-articles.client.view.html_.

À l’intérieur de l’élément `<div class="list-group">`, juste au-dessus de la balise de fermeture `</a>`, ajoutez la ligne suivante pour afficher `comment` avec le reste des données de l’article :

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Ouvrez _modules/articles/client/views/admin/form-article.client.view.html_.

Recherchez l’élément `<div class="form-group">` qui contient le bouton Envoyer, qui ressemble à ceci :

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Juste au-dessus de cette balise, ajoutez un autre élément `<div class="form-group">` permettant aux utilisateurs de modifier le champ `comment`. Votre nouvel élément doit avoir l’aspect suivant :

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

Enregistrez toutes vos modifications.

Dans la fenêtre de terminal local, retestez vos modifications en mode de production.

```bash
gulp prod
NODE_ENV=production node server.js
```

Accédez à `http://localhost:8443` dans un navigateur et assurez-vous que vous êtes connecté.

Sélectionnez **Admin > Manage Articles** (Administrateur > Gérer les articles), puis ajoutez un article en cliquant sur le bouton **+** .

Vous voyez maintenant la nouvelle zone de texte `Comment`.

![Champ de commentaire ajouté aux Articles](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

Dans le terminal, arrêtez Node.js en tapant `Ctrl+C`.

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Validez vos modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Une fois le `git push` terminé, accédez à votre application Azure et testez la nouvelle fonctionnalité.

![Modifications du modèle et de la base de données publiées dans Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Si vous avez ajouté des articles précédemment, vous pouvez toujours les visualiser. Les données existantes dans votre Cosmos DB ne sont pas perdues. De plus, les mises à jour que vous appliquez au schéma de données n’affectent pas vos données existantes.

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-azure-app"></a>Gérer votre application Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis sur le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données à l’aide de l’API Azure Cosmos DB pour MongoDB
> * Connecter une application Node.js à une base de données
> * Déploiement de l’application dans Azure
> * Mettre à jour le modèle de données et redéployer l’application
> * Diffuser des journaux d’activité à partir d’Azure vers votre terminal
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application.

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer une application Node.js](configure-language-nodejs.md)