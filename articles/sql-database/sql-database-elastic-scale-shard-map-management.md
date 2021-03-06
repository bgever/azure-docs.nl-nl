---
title: Scale-out een Azure SQL database | Microsoft Docs
description: Het gebruik van de ShardMapManager, clientbibliotheek voor elastische database
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 0e9d647a-9ba9-4875-aa22-662d01283439
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 604690325fd755dcf5c997cc281fe9e5825c51a4
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2017
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Databases met de shard-toewijzing manager uitbreiden
Gebruik gemakkelijk als u wilt uitbreiden databases op SQL Azure, een manager shard-toewijzing. De shard-toewijzing manager is een speciale database waarin gegevens over alle shards (databases) in een set shard globale toewijzing worden bijgehouden. De metagegevens kan een toepassing verbinding maken met de juiste database op basis van de waarde van de **sharding sleutel**. Bovendien elke shard in de set bevat kaarten die bijhouden van de lokale shard-gegevens (ook wel **shardlets**). 

![Beheer van shard-kaart](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Begrijpen hoe deze toewijzingen worden opgebouwd is essentieel voor beheer van shard-toewijzing. Dit wordt gedaan met behulp van de [ShardMapManager klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)vindt u in de [clientbibliotheek voor elastische Database](sql-database-elastic-database-client-library.md) voor het beheren van shard-kaarten.  

## <a name="shard-maps-and-shard-mappings"></a>Shard-kaarten en shard-toewijzingen
Voor elke shard, moet u het type van shard-toewijzing te maken. De keuze is afhankelijk van de database-architectuur: 

1. Één tenant per database  
2. Meerdere tenants per database (twee typen):
   1. Toewijzing van de lijst
   2. Bereik toewijzing

Voor een model voor één tenant, maakt u een **lijst toewijzing** shard-toewijzing. Het model voor één tenant wijst één database per tenant. Dit is een doeltreffend model voor SaaS-ontwikkelaars aangezien vereenvoudigt het beheer.

![Toewijzing van de lijst][1]

Het model multitenant verschillende tenants toegewezen aan een individuele database (en u kunt groepen van tenants verdelen over meerdere databases). Dit model gebruiken wanneer u verwacht dat elke tenant kleine hoeveelheden gegevens nodig hebben. In dit model we een scala aan tenants toewijzen aan een database met **bereik toewijzing**. 

![Bereik toewijzing][2]

Of u kunt implementeren met een multitenant database model met een *lijst toewijzing* meerdere tenants toewijzen aan een individuele database. Bijvoorbeeld, DB1 wordt gebruikt voor het opslaan van informatie over de tenant-id 1 en 5 en DB2 slaat gegevens voor de tenant 7- en 10. 

![Muliple tenants in één DB][3] 

### <a name="supported-net-types-for-sharding-keys"></a>Ondersteunde .net-typen voor sharding-sleutels
Elastische Scale ondersteuning voor de volgende .net Framework-typen als sharding sleutels:

* geheel getal
* lang
* GUID
* Byte]  
* Datum/tijd
* TimeSpan
* DateTimeOffset

### <a name="list-and-range-shard-maps"></a>Lijst met en bereik shard-kaarten
Shard maps kunnen worden opgesteld met **lijsten met afzonderlijke sharding waarden sleutel**, of ze kunnen worden opgesteld met **bereiken van sharding waarden sleutel**. 

### <a name="list-shard-maps"></a>Lijst shard maps
**Shards** bevatten **shardlets** en de toewijzing van shardlets naar shards wordt onderhouden door een shard-toewijzing. Een **lijst shard-toewijzing** is een koppeling tussen de afzonderlijke sleutelwaarden die de shardlets identificeren en de databases die als shards fungeren.  **Lijst met toewijzingen** zijn de expliciete en andere sleutel waarden kunnen worden toegewezen aan dezelfde database. Bijvoorbeeld: sleutel 1 wordt toegewezen aan een Database en sleutelwaarden 3 en 6 verwijst naar Database B.

| Sleutel | Shard-locatie |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Bereik shard maps
In een **bereik shard-toewijzing**, het bereik van de sleutel is beschreven door een combinatie van **[lage waarde, hoogwaardige)** waar de *lage waarde* is de minimale sleutel in het bereik en de *hoog Waarde* is de eerste waarde hoger is dan het bereik. 

Bijvoorbeeld: **[0, 100)** bestaat uit alle gehele getallen groter dan of gelijk 0 en kleiner dan 100. Denk eraan dat meerdere adresbereiken kunnen verwijzen naar dezelfde database en niet-aaneengesloten bereiken worden ondersteund (bijvoorbeeld [100,200) en [400,600) beide verwijzen naar de C-Database in het onderstaande voorbeeld.)

| Sleutel | Shard-locatie |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Elk van de bovenstaande tabellen is een conceptueel voorbeeld van een **ShardMap** object. Elke rij is een eenvoudig voorbeeld van een persoon **PointMapping** (voor de lijst shard-toewijzing) of **RangeMapping** (voor de bereik shard-toewijzing) object.

## <a name="shard-map-manager"></a>Beheer van shard-kaart
In de clientbibliotheek is de shard-toewijzing manager een verzameling van shard-kaarten. De gegevens die worden beheerd door een **ShardMapManager** exemplaar wordt opgeslagen op drie locaties: 

1. **Globale Shard-toewijzing (GSM)**: opgeven van een database om te fungeren als de opslagplaats voor alle shard-kaarten en toewijzingen. Speciale tabellen en opgeslagen procedures worden automatisch gemaakt om de gegevens te beheren. Dit is meestal een kleine database en licht geopend en mag niet worden gebruikt voor andere vereisten voor de toepassing. De tabellen in een speciale schema met de naam zijn **__ShardManagement**. 
2. **Lokale Shard-toewijzing (LSM)**: elke database die u opgeeft moet een shard bevat verschillende kleine tabellen en speciale opgeslagen procedures die bevatten en beheren van shard map specifieke informatie over die shard wordt gewijzigd. Deze informatie is redundant met de informatie in de GSM en kan de toepassing in de cache shard kaart om informatie te valideren zonder belasting op de GSM; worden geplaatst de toepassing gebruikt de LSM om te bepalen of een toewijzing in de cache nog steeds geldig is. De tabellen die overeenkomt met de LSM op elke shard bevinden zich ook in het schema **__ShardManagement**.
3. **Toepassingscache**: elke toepassing exemplaar toegang tot een **ShardMapManager** object onderhoudt een lokale cache in het geheugen van de toewijzingen. Routinggegevens die onlangs zijn opgehaald worden opgeslagen. 

## <a name="constructing-a-shardmapmanager"></a>Een ShardMapManager maken
Een **ShardMapManager** -object is gemaakt met behulp van een [factory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx) patroon. De  **[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**  methode heeft referenties (inclusief de servernaam en databasenaam die de GSM) in de vorm van een **ConnectionString** en retourneert een exemplaar van een **ShardMapManager**.  

**Opmerking:** de **ShardMapManager** slechts één keer per app-domein, binnen de van de initialisatiecode voor een toepassing moet worden gemaakt. Maken van een extra exemplaren van ShardMapManager in hetzelfde appdomain resulteert in meer geheugen en CPU-gebruik van de toepassing. Een **ShardMapManager** mag een onbeperkt aantal shard-kaarten. Bij een enkele shard-toewijzing is mogelijk niet voldoende is voor veel toepassingen, zijn er keer wanneer verschillende sets van databases worden gebruikt voor verschillende schema of voor unieke doeleinden; in deze gevallen mogelijk meerdere shard-kaarten beter. 

In deze code wordt een toepassing probeert te openen met een bestaande **ShardMapManager** met de [TryGetSqlShardMapManager methode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx).  Als objecten die een Global **ShardMapManager** (GSM) nog niet bestaat in de database, de clientbibliotheek maakt ze er met de [CreateSqlShardMapManager methode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx).

    // Try to get a reference to the Shard Map Manager 
     // via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 

Als alternatief kunt u Powershell gebruiken voor het maken van een nieuwe Manager van de Shard-toewijzing. Een voorbeeld is beschikbaar [hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Een RangeShardMap of ListShardMap ophalen
Na het maken van een shard kaart manager, kunt u krijgen de [RangeShardMap](https://msdn.microsoft.com/library/azure/dn807318.aspx) of [ListShardMap](https://msdn.microsoft.com/library/azure/dn807370.aspx) met behulp van de [TryGetRangeShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), wordt de [TryGetListShardMap ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx), of de [GetShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) methode.

    /// <summary>
    /// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
    /// </summary>
    public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
    {
        // Try to get a reference to the Shard Map.
        RangeShardMap<T> shardMap;
        bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

        if (shardMapExists)
        {
            ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
        }
        else
        {
            // The Shard Map does not exist, so create it
            shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
            ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
        }

        return shardMap;
    } 

### <a name="shard-map-administration-credentials"></a>Beheerdersrechten shard-kaart
Toepassingen beheren en manipuleren van shard-kaarten wijken af van de implementaties die gebruikmaken van de shard-toewijzingen op route-verbindingen. 

Voor het beheren van shard-toewijzingen (toevoegen of wijzigen shards, shard-kaarten, shard-toewijzingen, enz.) u instantiëren moet de **ShardMapManager** met **referenties die lezen/schrijven-bevoegdheden op zowel de GSM-database en op elk database die als een shard fungeert**. De referenties moeten ervoor zorgen dat voor de tabellen in de GSM en de LSM te schrijven shard kaart informatie wordt ingevoerd of gewijzigd, ook als voor het maken van tabellen LSM op nieuwe shards.  

Zie [referenties gebruikt voor toegang tot de clientbibliotheek voor elastische Database](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Alleen de metagegevens die van invloed op een
Methoden voor het invullen van of het wijzigen van de **ShardMapManager** gegevens de gegevens van de gebruiker opgeslagen in de shards zelf niet wijzigen. Bijvoorbeeld, methoden zoals **CreateShard**, **DeleteShard**, **UpdateMapping**, enzovoort. de shard kaart metagegevens alleen van invloed zijn op. Ze niet verwijdert, toevoegen of wijzigen van de gebruikersgegevens in de shards. In plaats daarvan deze methoden zijn ontworpen om te worden gebruikt in combinatie met afzonderlijke u bewerkingen uitvoeren om te maken of verwijderen werkelijke databases of die rijen uit één shard naar de andere verplaatsen een shard-omgeving opnieuw verdelen.  (De **gesplitste samenvoegen** hulpprogramma dat wordt geleverd met hulpprogramma's voor elastische database wordt gebruikgemaakt van deze API's samen met het organiseren van de daadwerkelijke gegevensverplaatsing tussen shards.) Zie [schalen met het hulpprogramma voor elastische Database gesplitste samenvoegen](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="populating-a-shard-map-example"></a>Voorbeeld van een shard-toewijzing in te vullen
Een voorbeeld van de volgorde van de bewerkingen voor het vullen van een specifieke shard-toewijzing wordt hieronder weergegeven. De code voert de volgende stappen uit: 

1. Een nieuwe shard-toewijzing wordt gemaakt binnen een manager shard-toewijzing. 
2. De metagegevens voor twee verschillende shards wordt toegevoegd aan de shard-toewijzing. 
3. Een aantal belangrijke bereik toewijzingen worden toegevoegd en de algehele inhoud van de shard-toewijzing wordt weergegeven. 

De code is geschreven, zodat de methode kan opnieuw worden gestart als er een fout optreedt. Elke aanvraag wordt gecontroleerd of een shard of toewijzing al bestaat, voordat u probeert te maken. De code wordt ervan uitgegaan dat de naam van databases **sample_shard_0**, **sample_shard_1** en **sample_shard_2** al zijn gemaakt op de server waarnaar wordt verwezen door de tekenreeks **shardServer**. 

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
                                     shardServer, 
                                     "sample_shard_0"), 
                                     out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
                                            shardServer, 
                                            "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
                                    shardServer, 
                                    "sample_shard_1"), 
                                    out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
                                             shardServer, 
                                            "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                          new RangeMappingCreationInfo<long>
                          (new Range<long>(0, 50), 
                          shard0, 
                          MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
                         shard1, 
                         MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
                         new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
                         shard0, 
                         MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 

Als alternatief kunt u PowerShell-scripts als u hetzelfde resultaat. Sommige van de voorbeeld-PowerShell-voorbeelden zijn beschikbaar [hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).     

Zodra de shard-kaarten zijn ingevuld, worden data access-toepassingen gemaakt of worden aangepast voor gebruik met de toewijzingen. In te vullen of bewerken van de toewijzingen niet pas weer moet plaatsvinden **kaart indeling** moet wijzigen.  

## <a name="data-dependent-routing"></a>Gegevensafhankelijke routering
De shard-toewijzing manager zal meest worden gebruikt in toepassingen waarvoor databaseverbindingen de bewerkingen van de app-specifieke gegevens uit te voeren. Deze verbindingen moeten worden gekoppeld aan de juiste database. Dit staat bekend als **gegevensafhankelijke routering**. Exemplaar maken van een object shard kaart manager van de fabriek met referenties die alleen-lezen toegang op de database GSM hebben voor deze toepassingen. Afzonderlijke aanvragen voor latere verbindingen Geef referenties op die nodig zijn om verbinding te maken met de juiste shard-database.

Houd er rekening mee dat deze toepassingen (met behulp van **ShardMapManager** geopend met alleen-lezen referenties) geen wijzigingen aanbrengen in de kaarten of toewijzingen. Maak voor die behoeften beheerdersrechten-specifieke toepassingen of PowerShell-scripts die hogere bevoegdheden referenties opgeven, zoals eerder besproken. Zie [referenties gebruikt voor toegang tot de clientbibliotheek voor elastische Database](sql-database-elastic-scale-manage-credentials.md).

Zie voor meer informatie [gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Wijzigen van een shard-toewijzing
Een shard-toewijzing kan op verschillende manieren worden gewijzigd. Alle van de volgende manieren de metagegevens met een beschrijving van de shards en hun toewijzingen wijzigen maar ze gegevens binnen de shards niet fysiek aanpassen, of komen ze maken of verwijderen van de werkelijke databases.  Sommige van de bewerkingen in de shard-toewijzing die hieronder worden beschreven moet mogelijk worden gecoördineerd met beheeracties die gegevens fysiek verplaatsen of die toevoegen en verwijderen van de databases die fungeren als shards.

Deze methoden samenwerken als de bouwstenen beschikbaar voor het wijzigen van de algemene distributie van gegevens in uw databaseomgeving shard.  

* Toevoegen of verwijderen van shards: Gebruik  **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)**  en  **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)**  van de [Shardmap klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx). 
  
    De server en database voor de shard doel moeten al bestaan voor deze bewerkingen uit te voeren. Deze methoden geen invloed heeft op de databases zelf, alleen op metagegevens in de shard-toewijzing.
* Maken of verwijderen van punten of bereiken die zijn toegewezen aan de shards: Gebruik  **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**,  **[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)**  van de [ Klasse RangeShardMapping](https://msdn.microsoft.com/library/azure/dn807318.aspx), en  **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)**  van de [ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx)
  
    Aan de dezelfde shard kunnen veel verschillende punten of bereiken worden toegewezen. Deze methoden zijn alleen van invloed op de metagegevens - ze hebben geen invloed op alle gegevens die al aanwezig in shards. Als gegevens moeten worden verwijderd uit de database om het in overeenstemming zijn met **DeleteMapping** bewerkingen, moet u deze bewerkingen uitvoeren afzonderlijk, maar in combinatie met behulp van deze methoden.  
* Bestaande bereiken splitsen in twee of aangrenzende bereiken samenvoegen: Gebruik  **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)**  en  **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)**.  
  
    Let op te splitsen en samenvoegen van bewerkingen **veranderen niet de shard waaraan sleutelwaarden die zijn toegewezen**. Een splitsing een bestaand bereik opgesplitst in twee delen, maar blijft beide zoals toegewezen aan de dezelfde shard. Een samenvoeging is van invloed op twee aangrenzende bereiken die al zijn toegewezen aan de dezelfde shard, ze samenvoegen tot een enkel bereik.  De verplaatsing van punten of adresbereiken zelf tussen shards moet worden gecoördineerd door middel van **UpdateMapping** in combinatie met de daadwerkelijke gegevensverplaatsing.  U kunt de **gesplitste/Merge** service die deel uitmaken van de elastische database-hulpprogramma's voor het coördineren van shard kaart wijzigingen met de verplaatsing van gegevens wanneer verkeer nodig is. 
* Opnieuw toewijzen (of verplaatsen) afzonderlijke punten of bereiken die verschillende shards: Gebruik  **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**.  
  
    Omdat gegevens worden verplaatst van één shard naar een andere moeten mogelijk om het in overeenstemming zijn met **UpdateMapping** bewerkingen, moet u die verkeer uitvoeren afzonderlijk, maar in combinatie met behulp van deze methoden.
* Te ondernemen toewijzingen online als offline: Gebruik  **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)**  en  **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)**  om te bepalen van de online status van een toewijzing. 
  
    Bepaalde bewerkingen op de shard-toewijzingen zijn alleen toegestaan als een toewijzing in een 'offline' staat, met inbegrip van **UpdateMapping** en **DeleteMapping**. Als een toewijzing offline is, wordt een gegevens-afhankelijke aanvraag op basis van een sleutel die is opgenomen in de toewijzing een fout geretourneerd. Bovendien wanneer een bereik is eerst offline gehaald, wordt alle verbindingen met de betrokken shard worden automatisch om te voorkomen dat inconsistente of onvolledige resultaten voor query's die zijn gericht tegen bereiken wordt gewijzigd afgebroken. 

Toewijzingen zijn niet-wijzigbaar objecten in .net.  Alle methoden van het bovenstaande die Wijzig toewijzingen ongeldig ook alle verwijzingen naar deze in uw code. Uitvoeren van takenreeksen van bewerkingen die een toewijzing in een statuswijziging te vereenvoudigen retourneren alle methoden die een-toewijzing gewijzigd een nieuwe toewijzing verwijzing zodat bewerkingen kunnen worden gekoppeld. Bijvoorbeeld: voor het verwijderen van een bestaande toewijzing in shardmap sm dat de sleutel 25 bevat, kunt u uitvoeren het volgende: 

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## <a name="adding-a-shard"></a>Toevoegen van een shard
Toepassingen moeten vaak toe te voegen nieuwe shards verwerkt gegevens die wordt verwacht van de nieuwe sleutels of sleutelbereiken voor een shard-toewijzing die al bestaat. Bijvoorbeeld, een toepassing shard door Tenant-ID kan het nodig voor het inrichten van een nieuwe shard voor een nieuwe tenant of gegevens shard maandelijks moet mogelijk een nieuwe shard vóór het begin van elke nieuwe maand wordt ingericht. 

Als het nieuwe bereik sleutelwaarden nog geen deel uitmaakt van een bestaande toewijzing en geen gegevensverplaatsing nodig is, is het zeer eenvoudig is de nieuwe shard toevoegen en de nieuwe sleutel of het bereik op dat shard koppelen. Zie voor meer informatie over het toevoegen van nieuwe shards [toevoegen van een nieuwe shard](sql-database-elastic-scale-add-a-shard.md).

Voor scenario's waarvoor de verplaatsing van gegevens, is het hulpprogramma gesplitste samenvoegen echter vereist voor het indelen van de verplaatsing van gegevens tussen shards in combinatie met de updates nodig shard-toewijzing. Zie voor meer informatie over het gebruik van de gesplitste samenvoegen yool [overzicht van de gesplitste samenvoegen](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
