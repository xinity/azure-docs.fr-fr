---
title: Migrer les applications du Service de cache géré vers Redis - Azure | Microsoft Docs
description: Découvrez comment migrer les applications du Service de cache géré et d’In-Role Cache vers le Cache Azure pour Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 05638e17c2f41806a5c8aa3e0c3020eae82bdb60
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315967"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrer un Service de cache géré vers le Cache Azure pour Redis
Vous pouvez migrer les applications qui utilisent le Service de cache géré Azure vers le Cache Azure pour Redis sans modifier outre mesure votre application. Le degré de modification dépend des fonctionnalités du Service de cache géré utilisé par votre application de mise en cache. Bien que légèrement différentes, les API présentent des similitudes. Vous pouvez ainsi réutiliser, avec un minimum de modifications, une grande partie de votre code existant qui utilise le Service de cache géré pour accéder à un cache. Cet article montre comment effectuer la configuration requise et modifier les applications pour migrer vos applications de Service de cache géré vers le Cache Azure pour Redis. Elle explique également comment utiliser certaines fonctionnalités du Cache Azure pour Redis pour implémenter les fonctionnalités d’un cache du Service de cache géré.

>[!NOTE]
>Le service de cache géré et le In-Role Cache ont été [supprimés](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) le 30 novembre 2016. Si vous disposez de déploiements pour le In-Role Cache que vous souhaitez faire migrer vers le Cache Azure pour Redis, vous pouvez suivre les étapes de cet article.

## <a name="migration-steps"></a>Étapes de la migration
Vous devez exécuter les étapes suivantes pour migrer une application du Service de cache géré afin d’utiliser le Cache Azure pour Redis.

* Mappage des fonctionnalités du Service de cache géré au Cache Azure pour Redis
* Sélection d’une offre de cache
* Création d’un cache
* Configuration des clients de cache
  * Suppression de la configuration du Service de cache géré
  * Configuration d’un client de cache à l’aide du package NuGet Package StackExchange.Redis
* Migration du code du Service de cache géré
  * Connexion au cache à l’aide de la classe ConnectionMultiplexer
  * Accès aux types de données primitifs dans le cache
  * Utilisation des objets .NET dans le cache
* Migration de l’état de session ASP.NET et des caches de sortie vers le Cache Azure pour Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mappage des fonctionnalités du Service de cache géré au Cache Azure pour Redis
Bien que similaires, le Service de cache géré Azure et le Cache Azure pour Redis implémentent certaines de leurs fonctionnalités de différentes façons. Cette section décrit quelques-unes de leurs différences et explique comment implémenter les fonctionnalités du Service de cache géré dans le Cache Azure pour Redis.

| Fonctionnalité du Service de cache géré | Prise en charge du Service de cache géré | Prise en charge du cache Azure pour Redis |
| --- | --- | --- |
| Caches nommés |Un cache par défaut est configuré et, dans le cache des offres Standard et Premium, vous pouvez, si vous le souhaitez, configurer jusqu’à neuf caches nommés supplémentaires. |Le cache Azure pour Redis dispose d’un nombre configurable de bases de données (16 par défaut) pouvant être utilisées pour implémenter une fonctionnalité semblable aux caches nommés. Pour plus d’informations, consultez les sections [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Que sont les bases de données Redis ?) et [Configuration du serveur Redis par défaut](cache-configure.md#default-redis-server-configuration). |
| Haute disponibilité |Fournit une haute disponibilité pour les éléments du cache dans le cadre des offres de cache Standard et Premium. En cas de perte liée à une défaillance, les copies de sauvegarde des éléments du cache demeurent disponibles. Les écritures dans le cache secondaire sont effectuées de façon synchrone. |La fonction de haute disponibilité est incluse dans les offres de cache Standard et Premium, qui reposent sur une configuration à deux nœuds (nœud principal/réplica) où chaque partition d’un cache Premium dispose d’une paire nœud principal/réplica. Les écritures sur le réplica sont effectuées de façon asynchrone. Pour plus d’informations, consultez [Tarification du Cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/). |
| Notifications |Permet aux clients de recevoir des notifications asynchrones lorsque diverses opérations de cache sont exécutées sur un cache nommé. |Les applications clientes peuvent utiliser Redis pub/sub ou les [notifications de Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) pour obtenir une fonctionnalité similaire aux notifications. |
| Cache local |Stocke une copie des objets mis en cache en local sur le client pour un accès extrêmement rapide. |Les applications clientes doivent implémenter cette fonctionnalité à l’aide d’un dictionnaire ou d’une structure de données similaires. |
| Stratégie d’éviction |Aucune ou LRU. La stratégie LRU est utilisée par défaut. |Le Cache Azure pour Redis prend en charge les stratégies d’éviction suivante : volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl, noeviction. La stratégie volatile-lru est utilisée par défaut. Pour plus d’informations, consultez la section [Configuration du serveur Redis par défaut](cache-configure.md#default-redis-server-configuration). |
| Stratégie d’expiration |La stratégie d’expiration par défaut est « Absolue » et l’intervalle d’expiration par défaut est de 10 minutes. Les stratégies « Fenêtre coulissante » et « Jamais » sont également disponibles. |Par défaut, les éléments du cache n’expirent pas, mais il est possible de configurer un délai d’expiration par écriture en utilisant les surcharges de jeu de cache. |
| Régions et balisage |Les régions sont des sous-groupes d’éléments mis en cache. Elles prennent également en charge l’annotation des éléments mis en cache avec des chaînes descriptives supplémentaires appelées « balises ». Les régions offrent la possibilité d’effectuer des opérations de recherche sur n’importe quel élément balisé de la région concernée. Tous les éléments d’une région se trouvent dans un seul nœud du cluster de cache. |Un cache Azure pour Redis se compose d’un seul nœud (à moins que le cluster Redis ne soit activé), ce qui signifie que le concept de régions du Service de Cache géré ne s’applique pas. Redis prend en charge la recherche et les opérations génériques lors de la récupération des clés de manière à incorporer des balises descriptives dans les noms de clé dans le but de récupérer les éléments ultérieurement. Pour obtenir un exemple d’implémentation d’une solution de balisage à l’aide de Redis, voir la page relative à l’ [implémentation de balisage de cache avec Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Sérialisation |Le Service de cache géré prend en charge NetDataContractSerializer, BinaryFormatter, ainsi que l’utilisation de sérialiseurs personnalisés. La valeur par défaut est NetDataContractSerializer. |L’application cliente doit sérialiser des objets .NET avant de les placer dans le cache ; le choix du sérialiseur appartient en revanche au développeur de l’application cliente. Pour plus d’informations et pour obtenir un exemple de code, consultez la section [Utilisation d’objets .NET dans le cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Émulateur de cache |Le cache géré fournit un émulateur de cache local. |Le cache Azure pour Redis n’a pas d’émulateur, mais vous pouvez [exécuter la génération MSOpenTech de redis-server.exe localement](cache-faq.md#cache-emulator) pour offrir une expérience d’émulateur. |

## <a name="choose-a-cache-offering"></a>Sélection d’une offre de cache
Les niveaux suivants sont proposés pour le cache Azure pour Redis :

* **De base** , avec un seul nœud. Plusieurs tailles jusqu'à 53 Go.
* **Standard** : avec deux nœuds, principal et réplica. Plusieurs tailles jusqu'à 53 Go. Un contrat SLA de 99,9 %.
* **Premium** - Deux nœuds (principal / réplica) contenant jusqu’à 10 partitions. Plusieurs tailles de 6 Go à 1,2 To. Toutes les fonctionnalités du niveau Standard et d’autres, y compris la prise en charge du [cluster Redis](cache-how-to-premium-clustering.md), la [persistance Redis](cache-how-to-premium-persistence.md) et le [réseau virtuel Azure](cache-how-to-premium-vnet.md). Un contrat SLA de 99,9 %.

Chaque option diffère en termes de fonctionnalités et de tarification. Les fonctionnalités sont décrites plus loin dans ce guide ; pour plus d'informations sur la tarification, consultez la page [Tarification - Cache](https://azure.microsoft.com/pricing/details/cache/).

L’idéal pour commencer la migration consiste à sélectionner une taille correspondant à celle de votre ancien cache de Service de cache géré, puis à l’adapter en fonction des exigences de votre application. Pour plus d’informations sur le choix de l’offre Cache Azure pour Redis adaptée à vos besoins, consultez [Que propose le cache Azure pour Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Création d’un cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configuration des clients de cache
Une fois le cache créé et configuré, vous devez supprimer la configuration du Service de cache géré, puis ajouter la configuration et les références du Cache Azure pour Redis, afin que les clients du cache puissent y accéder.

* Suppression de la configuration du Service de cache géré
* Configuration d’un client de cache à l’aide du package NuGet Package StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Suppression de la configuration du Service de cache géré
Pour pouvoir configurer les applications clientes pour le Cache Azure pour Redis, vous devez au préalable supprimer la configuration et les références d’assembly existantes du Service de cache géré en désinstallant le package NuGet du Service de cache géré.

Pour désinstaller ce package, cliquez avec le bouton droit sur le projet client dans l’**Explorateur de solutions** et choisissez **Gérer les packages NuGet**. Sélectionnez le nœud **Packages installés**, puis entrez W**indowsAzure.Caching** dans la zone de recherche des packages installés. Sélectionnez **Cache**  **Microsoft Azure** (ou **Mise en cache** **Microsoft Azure** en fonction de la version du package NuGet), cliquez sur **Désinstaller**, puis sur **Fermer**.

![Désinstaller le package NuGet du Service de cache géré Azure](./media/cache-migrate-to-redis/IC757666.jpg)

La désinstallation du package NuGet du Service de cache géré a pour effet de supprimer les assemblys du Service de cache géré ainsi que les entrées du Service de cache géré dans le fichier app.config ou web.config de l’application cliente. Comme certains paramètres personnalisés peuvent ne pas être supprimés lors de la désinstallation du package NuGet, vous devez ouvrir web.config ou app.config et vérifier que les éléments suivants sont supprimés.

Assurez-vous que l’entrée `dataCacheClients` est supprimée de l’élément `configSections`. Ne supprimez pas l’intégralité de l’élément `configSections` ; supprimez simplement l’entrée `dataCacheClients`, le cas échéant.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Vérifiez que la section `dataCacheClients` est supprimée. La section `dataCacheClients` doit avoir un aspect proche de l’exemple ci-dessous.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Une fois que vous avez supprimé la configuration du Service de cache géré, vous pouvez configurer le client de cache comme indiqué dans la section suivante.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configuration d’un client de cache à l’aide du package NuGet Package StackExchange.Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migration du code du Service de cache géré
L’API pour le client de cache StackExchange.Azure pour Redis est similaire au Service de cache géré. Cette section en décrit les différences.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Connexion au cache à l’aide de la classe ConnectionMultiplexer
Dans le Service de cache géré, les connexions au cache étaient gérées par les classes `DataCacheFactory` et `DataCache`. Dans le cache Azure pour Redis, ces connexions sont gérées par la classe `ConnectionMultiplexer`.

Ajoutez la déclaration suivante au début de chaque fichier à partir duquel vous souhaitez accéder au cache.

```csharp
using StackExchange.Redis
```

Si cet espace de noms ne se résout pas, vérifiez que vous avez bien ajouté le package NuGet StackExchange.Redis comme décrit dans la section [Démarrage rapide: Utiliser le cache Azure pour Redis avec une application .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Notez que le client StackExchange.Redis requiert l’installation de .NET Framework version 4 ou ultérieure.
> 
> 

Pour vous connecter à une instance de Cache Azure pour Redis, appelez la méthode statique `ConnectionMultiplexer.Connect` et passez le point de terminaison et la clé. Pour partager une instance `ConnectionMultiplexer` dans votre application, vous pouvez utiliser une propriété statique qui renvoie une instance connectée, comme dans l’exemple suivant. Cette approche fournit une méthode thread-safe permettant d’initialiser une seule instance de `ConnectionMultiplexer` connectée. Dans cet exemple, `abortConnect` est défini sur false, ce qui signifie que l’appel réussira même si aucune connexion au cache n’est établie. Une fonctionnalité clé de `ConnectionMultiplexer` est qu’il restaure automatiquement la connectivité au cache une fois que le problème réseau ou d’autres causes sont résolus.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Le point de terminaison, les clés et les ports du cache peuvent être obtenus dans le panneau **Cache Azure pour Redis** de votre instance de cache. Pour plus d’informations, consultez [Propriétés du Cache Azure pour Redis](cache-configure.md#properties).

Une fois la connexion établie, renvoyez une référence à la base de données du Cache Azure pour Redis en appelant la méthode `ConnectionMultiplexer.GetDatabase`. L’objet renvoyé à partir de la méthode `GetDatabase` est un objet direct léger qui n’a pas besoin d’être stocké.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

Le client StackExchange.Redis utilise les types `RedisKey` et `RedisValue` pour accéder aux éléments et les stocker dans le cache. Ces types correspondent aux types de langages les plus primitifs, y compris les chaînes, et sont rarement utilisés directement. Les chaînes Redis représentent le type de valeur Redis le plus élémentaire et peuvent contenir de nombreux types de données, notamment des flux de données binaires sérialisés. Bien que vous ne puissiez pas utiliser directement le type, vous utiliserez des méthodes contenant la propriété `String` dans le nom. Pour les types de données les plus primitifs, vous stockez et vous récupérez les éléments du cache avec les méthodes `StringSet` et `StringGet`, sauf si vous stockez des collections ou d’autres types de données Redis dans le cache. 

`StringSet` et `StringGet` sont très similaires aux méthodes `Put` et `Get` du service de cache géré. Leur principale différence réside dans le fait que vous devez d’abord sérialiser un objet .NET avant de le placer dans le cache et de le récupérer. 

Lors de l’appel de `StringGet`, si l’objet existe, il est renvoyé ; sinon, la valeur Null est renvoyée. Dans ce cas, vous pouvez récupérer la valeur auprès de la source de données de votre choix et la stocker dans le cache pour un usage ultérieur. Ce modèle est appelé modèle de type cache-aside.

Pour spécifier l’expiration d’un élément du cache, utilisez le paramètre `TimeSpan` de `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Le Cache Azure pour Redis est compatible aussi bien avec les objets .NET qu’avec les types de données primitifs, mais avant qu’un objet .NET puisse être mis en cache, il doit être sérialisé. La sérialisation est de la responsabilité du développeur d’applications, qui a toute latitude pour choisir le sérialiseur. Pour plus d’informations et pour obtenir un exemple de code, consultez la section [Utilisation d’objets .NET dans le cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migration de l’état de session ASP.NET et des caches de sortie vers le Cache Azure pour Redis
Le Cache Azure pour Redis dispose de fournisseurs pour l’état de session ASP.NET et pour la mise en cache de sortie de pages. Pour migrer une application qui utilise les versions du Service de cache géré de ces fournisseurs, vous devez d’abord supprimer les sections de votre fichier web.config avant de configurer les versions Cache Azure pour Redis des fournisseurs. Pour obtenir des instructions sur l’utilisation des fournisseurs ASP.NET du Cache Azure pour Redis, consultez [Fournisseur d’état de session ASP.NET pour le Cache Azure pour Redis](cache-aspnet-session-state-provider.md) et [Fournisseur de caches de sortie ASP.NET pour le Cache Azure pour Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Étapes suivantes
Explorez la [documentation du Cache Azure pour Redis](https://azure.microsoft.com/documentation/services/cache/) pour accéder à des didacticiels, des exemples, des vidéos et de nombreuses autres ressources.

