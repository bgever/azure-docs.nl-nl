---
title: Azure Functions bewaken
description: Informatie over het gebruik van Azure Application Insights met Azure Functions voor het bewaken van de functie wordt uitgevoerd.
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: Azure-functies, functies, gebeurtenisverwerking, webhooks, dynamisch berekenen, architectuur zonder server
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 355cb2cef52b5dfecddae228d0cc24a069d3b695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-functions"></a>Azure Functions bewaken

## <a name="overview"></a>Overzicht 

[Azure Functions](functions-overview.md) biedt ingebouwde integratie met [Azure Application Insights](../application-insights/app-insights-overview.md) voor bewaking van functies. In dit artikel laat zien hoe de functies voor het verzenden van telemetriegegevens naar Application Insights configureren.

![Application Insights Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Functies heeft ook ingebouwde bewaking die geen gebruik maakt van Application Insights. Application Insights wordt aangeraden omdat het biedt meer gegevens en betere manieren om de gegevens te analyseren. Zie voor informatie over de ingebouwde bewaking, de [laatste sectie van dit artikel](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Application Insights-integratie inschakelen

Voor een functie-app om gegevens te verzenden naar Application Insights moet weten de instrumentatiesleutel van een Application Insights-exemplaar. Er zijn twee manieren waarop u deze verbinding in de [Azure-portal](https://portal.azure.com):

* [Maken van een verbonden exemplaar van de Application Insights wanneer u de functie-app maakt](#new-function-app).
* [Een Application Insights-instantie verbinden met een bestaande app in de functie](#existing-function-app).
 
### <a name="new-function-app"></a>Nieuwe functie-app

Application Insights op de functie-App inschakelen **maken** pagina:

1. Stel de **Application Insights** overschakelen **op**.

2. Selecteer een **Application Insights locatie**.

   ![Application Insights inschakelen tijdens het maken van een functie-app](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Bestaande functie-app

Een instrumentatiesleutel ophalen en opslaan in een functie-app:

1. De Application Insights-exemplaar maken. Toepassingstype ingesteld op **algemene**.

   ![Application Insights-exemplaar, typt u algemene maken](media/functions-monitoring/ai-general.png)

2. Kopieer de instrumentatiesleutel van de **Essentials** pagina van de Application Insights-exemplaar. Beweeg de muisaanwijzer over het einde van de weergegeven sleutelwaarde ophalen van een **Klik hier om te kopiëren** knop.

   ![De Application Insights-instrumentatiesleutel kopiëren](media/functions-monitoring/copy-ai-key.png)

1. In de functie-app **toepassingsinstellingen** pagina [een app-instelling toevoegen](functions-how-to-use-azure-function-app-settings.md#settings) APPINSIGHTS_INSTRUMENTATIONKEY met de naam en de instrumentatiesleutel plakken.

   ![Instrumentatiesleutel toevoegen aan de app-instellingen](media/functions-monitoring/add-ai-key.png)

1. Klik op **Opslaan**.

## <a name="view-telemetry-data"></a>Telemetrie-gegevens weergeven

Om te navigeren naar Application Insights van een functie-app in de portal, selecteer de **Application Insights** koppeling op een van de functie app **overzicht** pagina.

Zie voor meer informatie over het gebruik van Application Insights de [Application Insights documentatie](https://docs.microsoft.com/azure/application-insights/). Deze sectie vindt enkele voorbeelden van gegevens weergeven in Application Insights. Als u al bekend met Application Insights bent, kunt u gaan rechtstreeks naar [de secties over het configureren en aanpassen van de telemetriegegevens](#configure-categories-and-log-levels).

In [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md), kunt u grafieken maken en waarschuwingen op basis van metrische gegevens zoals as-nummer van de functie aanroepen, uitvoeringstijd en slagingspercentage.

![Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Op de [fouten](../application-insights/app-insights-asp-net-exceptions.md) tabblad kunt u diagrammen maken en waarschuwingen op basis van functie fouten en server uitzonderingen. De **bewerkingsnaam** is de naam van de functie. Fouten in de afhankelijkheden worden niet weergegeven, tenzij u implementeert [aangepaste telemetrie](#custom-telemetry-in-c-functions) voor afhankelijkheden.

![fouten](media/functions-monitoring/failures.png)

Op de [prestaties](../application-insights/app-insights-performance-counters.md) tabblad kunt u prestatieproblemen analyseren.

![Prestaties](media/functions-monitoring/performance.png)

De **Servers** tabblad toont brongebruik en doorvoer per server. Deze gegevens kan nuttig zijn voor foutopsporing in scenario's waarin functies zijn bogging uw onderliggende resources. Servers worden aangeduid als *rolinstanties Cloud*. 

![Servers](media/functions-monitoring/servers.png)

De [livestream metrische gegevens](../application-insights/app-insights-live-stream.md) tabblad metrische gegevens worden weergegeven wanneer deze is gemaakt in realtime.

![Livestream](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Telemetrie querygegevens

[Application Insights Analytics](../application-insights/app-insights-analytics.md) hebt u toegang tot alle telemetriegegevens in de vorm van tabellen in een database. Analytics biedt een querytaal voor uitpakken en het bewerken van gegevens.

![Selecteer Analytics](media/functions-monitoring/select-analytics.png)

![Analytics-voorbeeld](media/functions-monitoring/analytics-traces.png)

Hier volgt een voorbeeld van een query. Deze toont de distributie van aantal aanvragen per worker in de afgelopen 30 minuten.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

De tabellen die beschikbaar zijn, worden weergegeven in de **Schema** tabblad van het linkerdeelvenster. Hier vindt u gegevens die zijn gegenereerd door de functie aanroepen in de volgende tabellen:

* **traceringen** -logboeken gemaakt door de runtime en functiecode.
* **aanvragen** -één voor elke functie-aanroep.
* **uitzonderingen** - eventuele uitzonderingen die door de runtime.
* **customMetrics** -aantal geslaagde en mislukte aanroepen, slagingspercentage, duur.
* **customEvents** -gebeurtenissen bijgehouden door de runtime, bijvoorbeeld: HTTP-aanvragen dat een functie activeren.
* **performanceCounters** -informatie over de prestaties van de servers die op de functies worden uitgevoerd.

De andere tabellen zijn voor beschikbaarheidstests en clientbrowser/telemetrie. U kunt aangepaste telemetrie voor gegevens toevoegen aan deze kunt implementeren.

Binnen elke tabel, is enkele van de functies-specifieke gegevens een `customDimensions` veld.  Bijvoorbeeld de volgende query haalt alle traces waarvoor logboekniveau `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

De runtime biedt `customDimensions.LogLevel` en `customDimensions.Category`. U kunt extra velden in logboeken die u in uw functiecode opgeven. Zie [gestructureerd logboekregistratie](#structured-logging) verderop in dit artikel.

## <a name="configure-categories-and-log-levels"></a>Configureren van de categorieën en meld u niveaus

U kunt Application Insights gebruiken zonder aangepaste configuratie, maar de standaardconfiguratie kan leiden tot grote hoeveelheden gegevens. Als u een Visual Studio-Azure-abonnement, kunt u mogelijk de initiaal van uw gegevens voor App Insights raakt. De rest van dit artikel laat zien hoe configureren en aanpassen van de gegevens die uw functies naar Application Insights verzendt.

### <a name="categories"></a>Categorieën

Het logboek van Azure Functions bevat een *categorie* voor elk logboek. De categorie geeft aan welk deel van de runtimecode of uw functiecode geschreven in het logboek. 

De runtime van Functions maakt logboeken die een categorie die begint met 'Host' zijn. Bijvoorbeeld, de 'functie gestart,' 'functie uitvoeren' en 'de functie is voltooid' logboeken tot de categorie 'Host.Executor'. 

Als u Logboeken in uw functiecode schrijft, is de categorie 'De functie'.

### <a name="log-levels"></a>Logboekniveaus

Het logboek van Azure functions bevat ook een *Meld niveau* met elke logboek. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) is een inventarisatie en de code integer geeft relatieve belang:

|LogLevel    |Code|
|------------|---|
|Tracering       | 0 |
|Fouten opsporen       | 1 |
|Informatie | 2 |
|Waarschuwing     | 3 |
|Fout       | 4 |
|Kritieke    | 5 |
|Geen        | 6 |

Meld u niveau `None` in de volgende sectie wordt uitgelegd. 

### <a name="configure-logging-in-hostjson"></a>Logboekregistratie in host.json configureren

De *host.json* bestand wordt geconfigureerd hoeveel logboekregistratie een functie-app naar Application Insights verzendt. Voor elke categorie, geeft u aan de minimale logboekniveau te verzenden. Hier volgt een voorbeeld:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

In dit voorbeeld stelt u de volgende regels:

1. Voor logboeken met categorie 'Host.Results' of 'De functie' alleen verzenden `Error` niveau en hoger naar Application Insights. De logboeken voor `Information` niveau en hieronder worden genegeerd.
2. Voor de logboeken met categorie Host. Aggregator, verzenden alleen `Information` niveau en hoger naar Application Insights. De logboeken voor `Debug` niveau en hieronder worden genegeerd.
3. Voor alle andere logboeken verzenden alleen `Information` niveau en hoger naar Application Insights.

De categoriewaarde in *host.json* bepaalt de logboekregistratie voor alle categorieën die met dezelfde waarde beginnen. Bijvoorbeeld, "" in Host *host.json* bepaalt de logboekregistratie voor 'Host.General', 'Host.Executor', 'Host.Results', enzovoort.

Als *host.json* bevat meerdere categorieën die met dezelfde tekenreeks beginnen langer die eerst worden vergeleken. Stel bijvoorbeeld dat u wilt dat alle gegevens van de runtime, behalve 'Host.Aggregator' aan te melden bij `Error` niveau tijdens 'Host.Aggregator' logboeken op `Information` niveau:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Als u wilt onderdrukken alle logboeken voor een categorie, kunt u logboekniveau `None`. Er worden geen logboeken zijn geschreven met die categorie en er is geen bovenliggende logboekniveau.

De volgende secties worden de belangrijkste categorieën van logboeken die de runtime worden gemaakt. 

### <a name="category-hostresults"></a>Categorie Host.Results

Deze logboeken wordt weergegeven als 'aanvragen' in Application Insights. Ze geven aan slagen of mislukken van een functie.

![Diagram van aanvragen](media/functions-monitoring/requests-chart.png)

Al deze logboeken worden geschreven op `Information` niveau, dus als u filteren op `Warning` of hoger, ziet u niet een van deze gegevens.

### <a name="category-hostaggregator"></a>Categorie Host.Aggregator

Deze logboeken bieden aantallen en gemiddelden van functie aanroepen via een [configureerbare](#configure-the-aggregator) periode van tijd. De standaardperiode is 30 seconden of 1000 resultaten, afhankelijk van wat zich het eerste voordoet. 

De logboeken weergegeven als 'customMetrics' in Application Insights. Voorbeelden zijn nummer wordt uitgevoerd, slagingspercentage en duur.

![customMetrics query](media/functions-monitoring/custom-metrics-query.png)

Al deze logboeken worden geschreven op `Information` niveau, dus als u filteren op `Warning` of hoger, ziet u niet een van deze gegevens.

### <a name="other-categories"></a>Andere categorieën

Alle logboeken voor categorieën dan degene die al wordt vermeld weergeven als 'traceringen' in Application Insights.

![traceringen query](media/functions-monitoring/analytics-traces.png)

Alle logboeken met categorieën die met 'Host beginnen' worden geschreven door de runtime van Functions. De 'Functie gestart' en 'Functie voltooid' logboeken hebben categorie 'Host.Executor'. Voor een geslaagde wordt uitgevoerd, zijn deze logboeken `Information` level; uitzonderingen zijn vastgelegd bij `Error` niveau. De runtime maakt ook `Warning` niveau Logboeken, bijvoorbeeld: verzonden naar de wachtrij verontreinigde berichten in de wachtrij.

Logboeken geschreven door de functiecode kunnen tot de categorie 'De functie' en elk logboekniveau.

## <a name="configure-the-aggregator"></a>De aggregator configureren

Zoals beschreven in de vorige sectie, de runtime gegevens worden verzameld over functies die gedurende een periode. De standaardperiode is 30 seconden of 1000 wordt uitgevoerd, afhankelijk van wat het eerst wordt bereikt. U kunt deze instelling in de *host.json* bestand.  Hier volgt een voorbeeld:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Steekproeven configureren

Application Insights is een [steekproeven](../application-insights/app-insights-sampling.md) functie die u beschermen kunt tegen het opstellen van te veel telemetriegegevens op tijdstippen van piekbelasting. Wanneer het aantal items telemetrie groter is dan een opgegeven snelheid, begint de Application Insights voor het negeren van willekeurig aantal binnenkomende items. U kunt configureren steekproeven in *host.json*.  Hier volgt een voorbeeld:

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>Logboeken geschreven in C#-functies

In de functiecode die worden weergegeven als de traceringen in Application Insights kunt u Logboeken schrijven.

### <a name="ilogger"></a>ILogger

Gebruik een [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) parameter in uw functies in plaats van een `TraceWriter` parameter. Logboeken die zijn gemaakt met behulp van `TraceWriter` gaat u naar Application Insights, maar `ILogger` kunt u doen [gestructureerd logboekregistratie](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Met een `ILogger` object u aanroepen `Log<level>` [uitbreidingsmethoden op ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) logboeken maken. Bijvoorbeeld de volgende code schrijft `Information` logboeken met categorie 'De functie'.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Gestructureerde logboekregistratie

De volgorde van de tijdelijke aanduidingen, niet hun namen bepaalt welke parameters worden gebruikt in het logboekbericht. Stel bijvoorbeeld dat u hebt de volgende code:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Als u dezelfde bericht tekenreeks houden en de volgorde van de parameters, hebben de resulterende berichttekst de waarden in op de verkeerde plaatsen.

Tijdelijke aanduidingen worden op deze manier behandeld, zodat u gestructureerde logboekregistratie kunt doen. Application Insights slaat de parameter naam / waarde-paren naast de bericht-tekenreeks. Het resultaat is dat de Berichtargumenten velden die u kunt een query op.

Als uw methodeaanroep berichtenlogboek op het vorige voorbeeld lijkt, kan u bijvoorbeeld het veld query `customDimensions.prop__rowKey`. Het voorvoegsel wordt toegevoegd om ervoor te zorgen dat er geen conflicten tussen die de runtime worden toegevoegd en de functiecode voegt velden zijn.

U kunt ook een query op de oorspronkelijke reeks van bericht door te verwijzen naar het veld `customDimensions.prop__{OriginalFormat}`.  

Hier volgt een voorbeeld JSON-weergave van `customDimensions` gegevens:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>Logboekregistratie van aangepaste metrische gegevens  

In C# scriptfuncties, kunt u de `LogMetric` extensiemethode op `ILogger` maken van aangepaste metrische gegevens in Application Insights. Hier volgt een voorbeeld methodeaanroep:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Deze code is een alternatief voor het aanroepen van `TrackMetric` met [Application Insights-API voor .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Schrijven Logboeken in JavaScript-functies

Gebruik in Node.js-functies, `context.log` schrijven Logboeken. Gestructureerde logboekregistratie is niet ingeschakeld.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Logboekregistratie van aangepaste metrische gegevens  

In een Node.js-functies, kunt u de `context.log.metric` methode voor het maken van aangepaste metrische gegevens in Application Insights. Hier volgt een voorbeeld methodeaanroep:

```javascript
context.log.metric("TestMetric", 1234); 
```

Deze code is een alternatief voor het aanroepen van `trackMetric` met [de Node.js-SDK voor Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Aangepaste telemetrie in C#-functies

U kunt de [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet-pakket aangepaste telemetrie om gegevens te verzenden naar Application Insights.

Hier volgt een voorbeeld van C#-code die gebruikmaakt van de [aangepaste telemetrie-API](../application-insights/app-insights-api-custom-events-metrics.md). Het voorbeeld is voor een class-bibliotheek voor .NET, maar de Application Insights-code is hetzelfde voor C# script.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetry = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;

            telemetry.Context.Operation.Id = context.InvocationId.ToString();
            telemetry.Context.Operation.Name = "cs-http";
            if (!String.IsNullOrEmpty(name))
            {
                telemetry.Context.User.Id = name;
            }
            telemetry.TrackEvent("Function called");
            telemetry.TrackMetric("Test Metric", DateTime.Now.Millisecond);
            telemetry.TrackDependency("Test Dependency", 
                "swapi.co/api/planets/1/", 
                start, DateTime.UtcNow - start, true);

            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
    }
}
```

Niet oproepen `TrackRequest` of `StartOperation<RequestTelemetry>`omdat ziet u dubbele aanvragen voor een functie-aanroep.  De runtime van Functions aanvragen automatisch worden bijgehouden.

Stel `telemetry.Context.Operation.Id` -ID telkens wanneer de functie wordt gestart voor het aanroepen. Hierdoor kunnen alle telemetrie-items voor een bepaalde functie-aanroep correleren.

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

## <a name="custom-telemetry-in-javascript-functions"></a>Aangepaste telemetrie in JavaScript-functies

De [Application Insights-SDK voor Node.js](https://www.npmjs.com/package/applicationinsights) is momenteel in een bètaversie. Hier volgt een aantal voorbeeldcode met aangepaste telemetrie naar Application Insights verzendt:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

De `tagOverrides` parametersets `operation_Id` naar de functie aanroep-ID. Deze instelling kunt u correlaties zichtbaar maken tussen alle van de automatisch gegenereerde en aangepaste telemetrie voor een bepaalde functie-aanroep.

## <a name="known-issues"></a>Bekende problemen

### <a name="dependencies"></a>Afhankelijkheden

Afhankelijkheden automatisch niet weergeven, maar u kunt aangepaste code schrijven voor afhankelijkheden weergeven. De voorbeeldcode in de [C# aangepaste telemetrie sectie](#custom-telemetry-in-c-functions) ziet u hoe. De voorbeeldcode resulteert in een *toepassingstoewijzing* in Application Insights dat ziet er als volgt:

![Overzicht van de toepassing](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Problemen rapporteren

Voor het rapporteren van een probleem met Application Insights-integratie in functies of een suggestie of de aanvraag, [maken van een probleem in GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Bewaking zonder Application Insights

Beter Application Insights voor controlefuncties omdat het biedt meer gegevens en betere manieren om de gegevens te analyseren. Maar u kunt ook zoeken Telemetrie en logboekgegevens opgeslagen in de Azure portal-pagina's voor een functie-app. 

Selecteer de **Monitor** tabblad voor een functie en u een lijst van de functies die ophalen. Selecteer een functie wordt uitgevoerd om te controleren van de duur van de invoergegevens, fouten en bijbehorende logboekbestanden.

> [!IMPORTANT]
> Wanneer u de [verbruik die als host fungeert voor plan](functions-overview.md#pricing) voor Azure Functions, de **bewaking** tegel in de functie-App worden geen gegevens weergegeven. Dit is omdat het platform dynamisch schaalbaar en beheert de compute-exemplaren voor u. Deze metrische gegevens zijn niet op een plan verbruik zinvolle.

### <a name="real-time-monitoring"></a>Realtime-controle

Realtime-controle is beschikbaar door te klikken op **stream live gebeurtenis** op de functie **Monitor** tabblad. De stream live gebeurtenis wordt weergegeven in een grafiek in een nieuw tabblad in de browser

> [!NOTE]
> Er is een bekend probleem dat ertoe leiden uw gegevens dat kan niet kunnen worden ingevuld. U wilt het browsertabblad met de stream live gebeurtenis sluiten en klik vervolgens op **stream live gebeurtenis** opnieuw toe te staan dat uw stream gebeurtenisgegevens correct te vullen. 

Deze statistieken zijn realtime maar de werkelijke grafieken van de uitvoering van gegevens mogelijk van de latentie van ongeveer 10 seconden.

### <a name="monitor-log-files-from-a-command-line"></a>Logboekbestanden van de monitor vanaf een opdrachtregel

U kunt logboekbestanden op een opdrachtregel-sessie op een lokaal werkstation met de Azure opdrachtregelinterface (CLI) 1.0 of PowerShell streamen.

### <a name="monitor-function-app-log-files-with-the-azure-cli-10"></a>De functie app-logboekbestanden met de Azure CLI 1.0 bewaken

Aan de slag [installeren van de Azure CLI 1.0](../cli-install-nodejs.md) en [aanmelden bij Azure](../xplat-cli-connect.md).

Gebruik de volgende opdrachten klassieke Service Management-modus inschakelen, kiest u uw abonnement en stream logboekbestanden:

```
azure config mode asm
azure account list
azure account set <subscriptionNameOrId>
azure site log tail -v <function app name>
```

### <a name="monitor-function-app-log-files-with-powershell"></a>Controleer de functie app-logboekbestanden met PowerShell

Aan de slag [Azure PowerShell installeren en configureren](/powershell/azure/overview).

Gebruik de volgende opdrachten naar uw Azure-account toevoegen, kiest u uw abonnement en stream logboekbestanden:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail
```

Zie voor meer informatie [hoe: logboeken voor web-apps Stream](../app-service/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Application Insights](https://docs.microsoft.com/azure/application-insights/)

> [!div class="nextstepaction"]
> [Meer informatie over het framework voor logboekregistratie die gebruikmaakt van functies](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)
