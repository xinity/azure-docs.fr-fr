---
title: Corrélation de télémétrie dans Azure Application Insights | Microsoft Docs
description: Corrélation de télémétrie dans Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: df93405940c02affa224fba2d2e6f07ce5278b15
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755372"
---
# <a name="telemetry-correlation-in-application-insights"></a>Corrélation de télémétrie dans Application Insights

Dans l’environnement des microservices, les tâches associées à chaque opération logique doivent être exécutées dans différents composants du service. Chacun de ces composants peut être supervisé séparément par [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Le composant application web communique avec le composant fournisseur d’authentification pour valider les informations d’identification utilisateur et avec le composant API pour obtenir des données à des fins de visualisation. Le composant API peut interroger les données d’autres services et utiliser les composants fournisseur de cache pour informer le composant facturation de cet appel. Application Insights prend en charge la corrélation de télémétrie distribuée, qui vous permet de détecter le composant responsable des défaillances ou de la dégradation des performances.

Cet article décrit le modèle de données utilisé par Application Insights pour mettre en corrélation les données de télémétrie envoyées par plusieurs composants. Il couvre les protocoles et les techniques de propagation de contexte. Il traite également de l’implémentation des concepts de corrélation sur différentes plateformes et dans différents langages.

## <a name="data-model-for-telemetry-correlation"></a>Modèle de données pour la corrélation de télémétrie

Application Insights définit le [modèle de données](../../azure-monitor/app/data-model.md) pour la corrélation de télémétrie distribuée. Pour associer la télémétrie à l’opération logique, chaque élément de télémétrie comporte un champ de contexte appelé `operation_Id`. Cet identificateur est partagé par chaque élément de télémétrie dans la trace distribuée. Ainsi, même en perdant la télémétrie d’une seule couche, vous pouvez toujours associer la télémétrie communiquée par d’autres composants.

Une opération logique distribuée se compose généralement d’un ensemble d’opérations plus petites, qui sont des requêtes traitées par l’un des composants. Ces opérations sont définies par la [télémétrie des requêtes](../../azure-monitor/app/data-model-request-telemetry.md). Chaque télémétrie de requête a son propre `id` qui l’identifie globalement et de façon unique. Et tous les éléments de télémétrie (tels que les traces et exceptions) associés à cette requête doivent définir l’`id` de la requête comme valeur d’`operation_parentId`.

Chaque opération sortante, telle qu’un appel HTTP vers un autre composant, est représentée par la [télémétrie des dépendances](../../azure-monitor/app/data-model-dependency-telemetry.md). La télémétrie des dépendances définit également son propre `id` globalement unique. La télémétrie des requêtes, lancée par cet appel de dépendances, utilise cet `id` comme `operation_parentId`.

Vous pouvez générer une vue de l’opération logique distribuée en utilisant `operation_Id`, `operation_parentId` et `request.id` avec `dependency.id`. Ces champs définissent également l’ordre de causalité des appels de télémétrie.

Dans un environnement de microservices, les traces des composants peuvent se diriger vers différents éléments de stockage. Chaque composant peut avoir sa propre clé d’instrumentation dans Application Insights. Pour obtenir la télémétrie de l’opération logique, l’expérience utilisateur Application Insights interroge les données de chaque élément de stockage. Quand la quantité d’éléments de stockage est énorme, vous aurez besoin d’une indication quant à l’emplacement où effectuer les recherches. Le modèle de données Application Insights définit deux champs pour résoudre ce problème : `request.source` et `dependency.target`. Le premier champ identifie le composant qui a initié la demande de dépendance, alors que le deuxième identifie le composant qui a retourné la réponse de l’appel de dépendance.

## <a name="example"></a>Exemples

Prenons l’exemple d’une application Stock Prices qui affiche le cours actuel sur le marché d’une action en utilisant une API externe nommée `Stock`. L’application Stock Prices comporte une page nommée `Stock page` que le navigateur web client ouvre à l’aide de `GET /Home/Stock`. L’application interroge l’API `Stock` au moyen d’un appel HTTP `GET /api/stock/value`.

Vous pouvez analyser la télémétrie obtenue en exécutant une requête :

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Dans les résultats, notez que tous les éléments de télémétrie partagent la racine `operation_Id`. Quand un appel Ajax est établi à partir de la page, un nouvel ID unique (`qJSXU`) est affecté à la télémétrie des dépendances, et l’ID de pageView est utilisé en tant que `operation_ParentId`. La requête de serveur utilise ensuite l’ID Ajax en tant que `operation_ParentId`.

| itemType   | Nom                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Quand l’appel `GET /api/stock/value` est établi vers un service externe, vous souhaitez connaître l’identité de ce serveur afin de pouvoir définir le champ `dependency.target` de manière appropriée. Quand le service externe ne prend pas en charge la supervision, `target` prend comme valeur le nom d’hôte du service (par exemple `stock-prices-api.com`). Toutefois, si le service s’identifie en retournant un en-tête HTTP prédéfini, `target` contient l’identité du service, ce qui permet à Application Insights de générer une trace distribuée en interrogeant la télémétrie à partir de ce service.

## <a name="correlation-headers"></a>En-têtes de corrélation

Nous effectuons une transition vers la norme [W3C Trace-Context](https://w3c.github.io/trace-context/), qui définit :

- `traceparent`: représente l’ID d’opération globalement unique et l’identificateur unique de l’appel.
- `tracestate`: représente le contexte de traçage propre au système.

Les dernières versions des SDK Application Insights prennent en charge le protocole Trace-Context. Cependant, vous devrez peut-être le choisir spécifiquement. (Il assurera une compatibilité descendante avec l’ancien protocole de corrélation pris en charge par les SDK ApplicationInsights).

Le [protocole HTTP de corrélation (Request-Id)](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) sera prochainement déprécié. Ce protocole définit deux en-têtes :

- `Request-Id`: représente le GUID de l’appel.
- `Correlation-Context`: représente la collection de paires nom-valeur des propriétés de trace distribuée.

Application Insights définit également l’[extension](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pour le protocole HTTP de corrélation. Il utilise des paires nom-valeur `Request-Context` pour propager la collection de propriétés utilisée par l’appelant ou l’appelé. Le SDK Application Insights utilise cet en-tête pour définir les champs `dependency.target` et `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications ASP.NET classiques
 
  > [!NOTE]
  > Aucune configuration n’est nécessaire à partir de `Microsoft.ApplicationInsights.Web` et `Microsoft.ApplicationInsights.DependencyCollector`. 

La prise en charge du protocole W3C Trace-Context repose sur une compatibilité descendante. La corrélation devrait fonctionner avec les applications instrumentées avec les versions précédentes du SKD (sans prise en charge du protocole W3C). 

Si, pour une raison quelconque, vous souhaitez continuer à utiliser le protocole `Request-Id` hérité, vous pouvez *désactiver* le protocole Trace-Context avec la configuration suivante.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Si vous exécutez une version antérieure du SDK, nous vous recommandons de la mettre à jour ou d’appliquer la configuration suivante pour activer Trace-Context.
Cette fonctionnalité est disponible dans les packages `Microsoft.ApplicationInsights.Web` et `Microsoft.ApplicationInsights.DependencyCollector` à compter de la version 2.8.0-beta1.
Elle est désactivée par défaut. Pour l’activer, modifiez `ApplicationInsights.config` :

- Sous `RequestTrackingTelemetryModule`, ajoutez l’élément `EnableW3CHeadersExtraction` avec la valeur définie sur `true`.
- Sous `DependencyTrackingTelemetryModule`, ajoutez l’élément `EnableW3CHeadersInjection` avec la valeur définie sur `true`.
- Ajouter `W3COperationCorrelationTelemetryInitializer` sous le `TelemetryInitializers` similaire à 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications ASP.NET Core

 > [!NOTE]
  > Aucune configuration n’est nécessaire à partir de la version 2.8.0 de `Microsoft.ApplicationInsights.AspNetCore`.
 
La prise en charge du protocole W3C Trace-Context repose sur une compatibilité descendante. La corrélation devrait fonctionner avec les applications instrumentées avec les versions précédentes du SKD (sans prise en charge du protocole W3C). 

Si, pour une raison quelconque, vous souhaitez continuer à utiliser le protocole `Request-Id` hérité, vous pouvez *désactiver* le protocole Trace-Context avec la configuration suivante.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Si vous exécutez une version antérieure du SDK, nous vous recommandons de la mettre à jour ou d’appliquer la configuration suivante pour activer Trace-Context.

Cette fonctionnalité est dans `Microsoft.ApplicationInsights.AspNetCore` version 2.5.0-beta1 et dans `Microsoft.ApplicationInsights.DependencyCollector` version 2.8.0-beta1.
Elle est désactivée par défaut. Pour l’activer, affectez la valeur `true` à `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Activer la prise en charge du traçage distribué W3C pour les applications Java

- **Configuration d’entrée**

  - Pour les applications Java EE, ajoutez ce qui suit à la balise `<TelemetryModules>` dans le fichier ApplicationInsights.xml :

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Pour les applications Spring Boot, ajoutez les propriétés suivantes :

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuration de sortie**

  Ajoutez le code suivant au fichier AI-Agent.xml :

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Le mode de compatibilité descendante est activé par défaut et le paramètre `enableW3CBackCompat` est facultatif. Utilisez-le uniquement quand vous souhaitez désactiver la compatibilité descendante.
  >
  > Dans l’idéal, vous la désactiveriez quand tous vos services auraient été mis à jour vers une version plus récente des SDK prenant en charge le protocole W3C. Nous vous recommandons vivement de déplacer ces SDK plus récents dès que possible.

> [!IMPORTANT]
> Assurez-vous que les configurations entrante et sortante sont rigoureusement identiques.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Activer la prise en charge du suivi distribué W3C pour les applications web

Cette fonctionnalité se trouve dans `Microsoft.ApplicationInsights.JavaScript`. Elle est désactivée par défaut. Pour l’activer, utilisez la configuration `distributedTracingMode`. AI_AND_W3C est fourni pour la compatibilité descendante avec tous les services instrumentés Application Insights hérités :

- **Configuration NPM (ignorer si vous utilisez la configuration Snippet)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuration Snippet (ignorer si vous utilisez la configuration NPM)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing et Application Insights

La [spécification du modèle de données OpenTracing](https://opentracing.io/) et les modèles de données Application Insights correspondent de la façon suivante :

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` avec `span.kind = server`                  |
| `Dependency`                          | `Span` avec `span.kind = client`                  |
| `Id` de `Request` et `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` de type `ChildOf` (étendue parent)   |

Pour plus d’informations, consultez le [Modèle de données de télémétrie d’Application Insights](../../azure-monitor/app/data-model.md). 

Pour obtenir des définitions des concepts OpenTracing, consultez les pages [specification](https://github.com/opentracing/specification/blob/master/specification.md) et [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) relatives à OpenTracing.

## <a name="telemetry-correlation-in-opencensus-python"></a>Corrélation de télémétrie dans OpenCensus Python

OpenCensus Python respecte les spécifications de modèle de données `OpenTracing` décrites ci-dessus. Il prend également en charge le [contexte de trace W3C](https://w3c.github.io/trace-context/) sans nécessiter de configuration.

### <a name="incoming-request-correlation"></a>Corrélation de requêtes entrantes

OpenCensus Python met en corrélation les en-têtes de contexte de trace W3C des requêtes entrantes aux intervalles générés à partir des requêtes elles-mêmes. OpenCensus effectue cette opération automatiquement en s’intégrant avec les frameworks d’application web populaires tels que `flask`, `django` et `pyramid`. Les en-têtes de contexte de trace W3C doivent simplement être renseignés avec le [format approprié](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), puis envoyés avec la requête. Vous trouverez ci-dessous un exemple d’application `flask` à des fins de démonstration.

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Ceci permet d’exécuter un exemple d’application `flask` sur votre machine locale, en écoutant le port `8080`. Pour corréler le contexte de trace, nous envoyons une requête au point de terminaison. Dans cet exemple, nous pouvons utiliser une commande `curl`.
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
En examinant le [format de l’en-tête de contexte de trace](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), nous obtenons les informations suivantes : `version`: `00`
`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`
`parent-id/span-id`: `00f067aa0ba902b7`
`trace-flags`: `01`

Si nous examinons l’entrée de requête envoyée à Azure Monitor, nous pouvons voir des champs renseignés à l’aide des informations d’en-tête de trace.

![Capture d’écran de télémétrie de requête dans les journaux (Analytics) montrant les champs d’en-tête de trace mis en surbrillance en rouge](./media/opencensus-python/0011-correlation.png)

Le champ `id` est au format `<trace-id>.<span-id>`, où `trace-id` provient de l’en-tête de trace passé dans la requête, et où `span-id` est un tableau de 8 octets généré pour cette étendue. 

Le champ `operation_ParentId` est au format `<trace-id>.<parent-id>`, où `trace-id` et `parent-id` sont extraits de l’en-tête de trace passé dans la requête.

### <a name="logs-correlation"></a>Corrélation des journaux

OpenCensus Python permet la corrélation des journaux par enrichissement des enregistrements de journal avec l’ID de trace, l’ID d’étendue et l’indicateur d’échantillonnage. Pour ce faire, installez l’[intégration de la journalisation](https://pypi.org/project/opencensus-ext-logging/) OpenCensus. Les attributs suivants vont être ajoutés aux `LogRecord`s Python : `traceId`, `spanId` et `traceSampled`. Notez que cela prend effet uniquement pour les journaliseurs créés après l’intégration.
Vous trouverez ci-dessous l’exemple d’application correspondant.

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quand ce code s’exécute, nous obtenons ce qui suit dans la console :
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Notez la présence d’un spanId pour le message de journal situé dans l’intervalle. Il est identique au spanId qui appartient à l’intervalle nommé `hello`.

## <a name="telemetry-correlation-in-net"></a>Corrélation de télémétrie dans .NET

Au fil du temps, .NET a défini plusieurs façons de mettre en corrélation les journaux de diagnostics et de télémétrie :

- `System.Diagnostics.CorrelationManager` permet d’effectuer le suivi de [LogicalOperationStack et ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` et le suivi d’événements pour Windows (ETW) définissent la méthode [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` utilise les [étendues de journaux](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- WCF (Windows Communication Foundation) et HTTP relient la propagation de contexte « actuelle ».

Toutefois, ces méthodes n’activent pas la prise en charge du traçage distribué automatique. `DiagnosticSource` est une méthode permettant de prendre en charge la corrélation automatique entre ordinateurs. Les bibliothèques .NET prennent en charge « Diagnostics Source » et autorisent la propagation automatique entre ordinateurs du contexte de corrélation par le biais du transport, tel que le protocole HTTP.

Le [guide des activités](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) dans `DiagnosticSource` explique les principes de base du suivi des activités.

ASP.NET Core 2.0 prend en charge l’extraction des en-têtes HTTP et le démarrage d’une nouvelle activité.

`System.Net.Http.HttpClient`, à partir de la version 4.1.0, prend en charge l’injection automatique des en-têtes HTTP de corrélation et le suivi de l’appel HTTP en tant qu’activité.

Il existe un nouveau module HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), pour ASP.NET Classic. Ce module implémente la corrélation de télémétrie à l’aide de `DiagnosticSource`. Il démarre une activité en fonction des en-têtes de requête entrante. Il met également en corrélation les données de télémétrie des différentes phases de traitement de requêtes, même pour les cas où chaque phase du traitement IIS (Internet Information Services) s’exécute sur un thread managé différent.

Le SDK Application Insights, à compter de la version 2.4.0-beta1, utilise `DiagnosticSource` et `Activity` pour collecter la télémétrie et l’associer à l’activité en cours.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Corrélation des données de télémétrie dans le Kit de développement logiciel (SDK) Java

Le [SDK Application Insights pour Java](../../azure-monitor/app/java-get-started.md) prend en charge la corrélation automatique des données de télémétrie à partir de la version 2.0.0. Il renseigne automatiquement `operation_id` pour toutes les données de télémétrie (telles que les traces, exceptions et événements personnalisés) émises dans l’étendue d’une requête. Il s’occupe aussi de propager les en-têtes de corrélation (comme décrit plus haut) pour les appels entre les services par le biais du protocole HTTP si l’[agent du SDK Java](../../azure-monitor/app/java-agent.md) est configuré.

> [!NOTE]
> Seuls les appels effectués par le biais d’Apache HTTPClient sont pris en charge pour la fonctionnalité de corrélation. Si vous utilisez Spring RestTemplate ou Feign, tous deux peuvent être utilisés avec Apache HTTPClient en arrière-plan.

La propagation automatique de contexte entre les technologies de messagerie (telles que Kafka, RabbitMQ ou Azure Service Bus) n’est pas prise en charge. Toutefois, il est possible de coder de tels scénarios manuellement à l’aide des API `trackDependency` et `trackRequest`. Dans ces API, une télémétrie des dépendances représente un message étant ajouté à la file d’attente par un producteur, et la requête représente un message en cours de traitement par un consommateur. Dans ce cas, `operation_id` et `operation_parentId` doivent être propagés dans les propriétés du message.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Corrélation des données de télémétrie dans une application Java asynchrone

Pour mettre en corrélation les données de télémétrie dans l’application asynchrone Spring Boot, veuillez suivre [cet article détaillé](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). Il fournit des conseils pour l’instrumentation de l’objet [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) de Spring, ainsi que de l’objet [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html). 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nom de rôle

Dans certains cas, vous souhaiterez peut-être personnaliser la façon dont les noms de composant sont affichés dans la [cartographie d’application](../../azure-monitor/app/app-map.md). Pour ce faire, vous pouvez définir manuellement l’élément `cloud_RoleName` en effectuant l’une des opérations suivantes :

- Si vous utilisez Spring Boot avec le starter SpringBoot Application Insights, le seul changement nécessaire consiste à définir votre nom personnalisé pour l’application dans le fichier application.properties.

  `spring.application.name=<name-of-app>`

  Le starter SpringBoot attribue automatiquement `cloudRoleName` à la valeur que vous entrez pour la propriété `spring.application.name`.

- Si vous utilisez `WebRequestTrackingFilter`, `WebAppNameContextInitializer` définit automatiquement le nom de l’application. Ajoutez ce qui suit à votre fichier de configuration (ApplicationInsights.xml) :

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Si vous utilisez la classe de contexte cloud :

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Étapes suivantes

- Écrivez des [données de télémétrie personnalisées](../../azure-monitor/app/api-custom-events-metrics.md).
- Pour accéder à des scénarios de corrélation avancés dans ASP.NET Core et ASP.NET, consultez l'article consacré au [suivi des opérations personnalisées](custom-operations-tracking.md).
- Découvrez plus en détail la [définition de cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) pour d’autres kits SDK.
- Intégrez tous les composants de votre microservice sur Application Insights. Consultez les [plateformes prises en charge](../../azure-monitor/app/platforms.md).
- Pour connaître les types Application Insights, consultez [Modèle de données](../../azure-monitor/app/data-model.md).
- Découvrez comment [étendre et filtrer la télémétrie](../../azure-monitor/app/api-filtering-sampling.md).
- Consultez les [informations de référence sur la configuration d’Application Insights](configuration-with-applicationinsights-config.md).
