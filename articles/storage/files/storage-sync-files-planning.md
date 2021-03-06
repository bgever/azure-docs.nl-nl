---
title: Planning voor de implementatie van een Azure-bestand Sync (preview) | Microsoft Docs
description: Meer informatie over wat u moet overwegen bij het plannen van de implementatie van een Azure-bestanden.
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 241b744f5c5e89f53addb4d41d732245d76ef9a3
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planning voor de implementatie van een Azure-bestand Sync (preview)
Gebruik Azure bestand Sync (preview) te centraliseren bestandsshares van uw organisatie in Azure-bestanden, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestand-server. Azure File-synchronisatie transformeert Windows Server in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is op Windows Server voor toegang tot uw gegevens lokaal, met inbegrip van SMB en NFS FTPS gebruiken. U kunt zoveel caches als u over de hele wereld nodig hebben.

Dit artikel bevat belangrijke aandachtspunten voor de implementatie van een Azure-bestand Sync. Het is raadzaam dat u ook lezen [Planning voor de implementatie van een Azure-bestanden](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Azure File-Sync-terminologie
Voordat u in de details van de planning voor de implementatie van een Azure-bestand synchronisatie, is het belangrijk dat u de terminologie begrijpt.

### <a name="storage-sync-service"></a>Opslag Sync-Service
De Storage-Sync-Service is op het hoogste niveau Azure-resource voor Azure File-synchronisatie. De resource-opslag-Sync-Service is geen peer van de account opslagbronnen en kan op dezelfde manier worden geïmplementeerd op Azure-resourcegroepen. Een afzonderlijke op het hoogste niveau resource uit de bron van storage-account is nodig omdat de Storage-Sync-Service kan de synchronisatierelaties met meerdere opslagaccounts via meerdere synchronisatie-groepen maken kunt. Een abonnement kan meerdere synchronisatie opslagservice resources geïmplementeerd hebben.

### <a name="sync-group"></a>Groep voor synchronisatie
Een groep voor synchronisatie definieert de synchronisatie-topologie voor een set bestanden. Eindpunten in een groep voor synchronisatie zijn gesynchroniseerd met elkaar. Als u bijvoorbeeld twee verschillende sets van bestanden die u wilt beheren met het synchroniseren van Azure-bestand hebt, zou u twee synchronisatiegroepen maken en verschillende eindpunten toevoegen aan elke groep voor synchronisatie. Een opslag-Sync-Service kan zo veel synchronisatiegroepen behoefte hosten.  

### <a name="registered-server"></a>Geregistreerde Server
De Server geregistreerd-object vertegenwoordigt een vertrouwensrelatie tussen uw server (of een cluster) en de opslag-synchronisatieservice. U kunt zoveel servers naar een opslag-Sync-Service-exemplaar als u wilt registreren. Echter, een server (of een cluster) kan worden geregistreerd met slechts één opslag-synchronisatieservice tegelijk.

### <a name="azure-file-sync-agent"></a>Azure File-Sync-agent
De Azure-bestand Sync-agent is een downloadbare pakket waarmee Windows-Server moet worden gesynchroniseerd met een Azure-bestandsshare. De Azure-bestand Sync-agent heeft drie onderdelen: 
- **FileSyncSvc.exe**: de achtergrond van de Windows-service die verantwoordelijk is voor het bewaken van de wijzigingen op de Server-eindpunten en voor het initiëren van sessies synchroniseren naar Azure.
- **StorageSync.sys**: de Azure-bestand Sync bestandssysteemfilter, die verantwoordelijk is voor lagen bestanden naar de Azure-bestanden (wanneer cloud tiering is ingeschakeld).
- **PowerShell-cmdlets voor beheer**: PowerShell-cmdlets die u gebruikt om te communiceren met de resourceprovider Microsoft.StorageSync Azure. U vindt deze op de volgende (standaard)-locaties:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Servereindpunt
Een eindpunt Server vertegenwoordigt een specifieke locatie op een Server is geregistreerd, bijvoorbeeld een map op een volume van de server of in de hoofdmap van het volume. Meerdere Server-eindpunten kunnen bevinden zich op hetzelfde volume als hun naamruimten (bijvoorbeeld F:\sync1 en F:\sync2) niet overlappen. U kunt lagen beleidsregels cloud afzonderlijk configureren voor elk eindpunt van de Server. Als u de locatie van een server met een bestaande set van bestanden als een eindpunt van de Server aan een groep voor synchronisatie toevoegt, worden deze bestanden worden samengevoegd met andere bestanden die zich al op de andere eindpunten in de groep voor synchronisatie.

### <a name="cloud-endpoint"></a>Cloudeindpunt
Een Cloud-eindpunt is een Azure-bestandsshare die deel uitmaakt van een groep voor synchronisatie. De synchronisatie van de share volledige Azure-bestand en een Azure-bestandsshare is lid van slechts één eindpunt van de Cloud. Een Azure-bestandsshare kan dus een lid van slechts één groep voor synchronisatie. Als u een Azure-bestandsshare met een bestaande set van bestanden als een Cloudeindpunt aan een groep voor synchronisatie toevoegt, worden de bestaande bestanden worden samengevoegd met andere bestanden die zich al op de andere eindpunten in de groep voor synchronisatie.

> [!Important]  
> Azure File-synchronisatie ondersteunt rechtstreeks aanbrengen van wijzigingen in de Azure-bestandsshare. Alle wijzigingen op de Azure-bestandsshare moeten echter eerst door een Azure bestand Sync-taak voor het detecteren van wijziging wordt gedetecteerd. Een wijziging detectie-taak wordt gestart voor een Cloudeindpunt slechts eenmaal per 24 uur. Zie voor meer informatie [Veelgestelde vragen over Azure-bestanden](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Cloudopslaglagen 
Cloud tiering is een optionele functie van Azure bestand Sync waarin zelden gebruikt of gebruikte bestanden kunnen tiers worden verdeeld aan Azure-bestanden. Wanneer een bestand is gelaagd, vervangen het bestandssysteemfilter van Azure File-synchronisatie (StorageSync.sys) door het bestand lokaal een wijzer of een reparsepunt. Het reparsepunt vertegenwoordigt een URL naar het bestand in Azure-bestanden. Een gelaagde bestand heeft het 'offline' kenmerk is ingesteld in NTFS zodat toepassingen van derden gelaagde bestanden kunnen identificeren. Wanneer een gebruiker een gelaagde bestand opent, teruggehaald Azure bestand Sync bestandsgegevens uit Azure Files naadloos zonder dat de gebruiker hoeft te weten dat het bestand niet lokaal is opgeslagen op het systeem. Deze functionaliteit wordt ook wel hiërarchische opslag Management (HSM).

## <a name="azure-file-sync-interoperability"></a>Interoperabiliteit met Azure File-synchronisatie 
Deze sectie bevat informatie over Azure bestand Sync interoperabiliteit met Windows Server-functies en functies en oplossingen van derden.

### <a name="supported-versions-of-windows-server"></a>Ondersteunde versies van Windows Server
De ondersteunde versies van Windows Server door Azure bestand synchronisatie zijn momenteel:

| Versie | Ondersteunde SKU 's | Ondersteunde implementatie-opties |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter en Standard | Volledig (server met een gebruikersinterface) |
| Windows Server 2012 R2 | Datacenter en Standard | Volledig (server met een gebruikersinterface) |

Toekomstige versies van Windows Server worden toegevoegd zodra ze worden vrijgegeven. Eerdere versies van Windows kunnen worden toegevoegd op basis van feedback van gebruikers.

> [!Important]  
> We raden aan om alle servers die u met Azure bestand Sync bijgewerkt met de meest recente updates via Windows Update gebruikt. 

### <a name="file-system-features"></a>Functies
| Functie | Ondersteuning voor status | Opmerkingen |
|---------|----------------|-------|
| Toegangsbeheerlijsten (ACL's) | Volledig ondersteund | Windows ACL's blijven behouden door Azure bestand synchronisatie en worden afgedwongen door de Windows Server op Server-eindpunten. Windows ACL's (nog niet) door de Azure-bestanden ondersteund als de bestanden rechtstreeks in de cloud worden geopend. |
| Vaste koppelingen | Overgeslagen | |
| Symbolische koppelingen | Overgeslagen | |
| Koppelpunten | Gedeeltelijk ondersteund | Koppelpunten mogelijk de hoofdmap van het eindpunt van een Server, maar ze worden overgeslagen als ze zijn opgenomen in de naamruimte van het eindpunt van een Server. |
| Koppelingen | Overgeslagen | |
| Reparsepunten | Overgeslagen | |
| NTFS-compressie | Volledig ondersteund | |
| Verspreide bestanden | Volledig ondersteund | Synchronisatie van verspreide bestanden (worden niet geblokkeerd), maar ze naar de cloud als een volledig bestand synchroniseert. Als de inhoud van het bestand wijzigen in de cloud (of op een andere server), is het bestand niet meer sparse wanneer de wijziging wordt gedownload. |
| Alternatieve gegevensstreams (ADS) | Behouden, maar niet gesynchroniseerd | |

> [!Note]  
> NTFS-volumes worden ondersteund.

### <a name="failover-clustering"></a>Failoverclustering
Windows Server Failover Clustering wordt ondersteund door Azure bestand Sync voor de optie 'Bestandsserver voor algemeen gebruik'-implementatie. Failover Clustering wordt niet ondersteund op 'Scale-Out File Server for application data' (SOFS) of op geclusterde gedeelde Volumes (CSV's).

> [!Note]  
> De Azure-bestand Sync-agent moet worden geïnstalleerd op elk knooppunt in een failovercluster voor synchronisatie correct te laten werken.

### <a name="data-deduplication"></a>Gegevensontdubbeling
Voor volumes waarvoor geen cloud tiering ingeschakeld, ondersteunt Azure bestand Sync Windows Server-Gegevensontdubbeling is ingeschakeld op het volume. Op dit moment wordt niet ondersteund interoperabiliteit tussen Azure bestand synchronisatie met cloud tiering ingeschakeld en Ontdubbeling van gegevens.

### <a name="antivirus-solutions"></a>Anti-virussoftware
Omdat antivirus werkt door te scannen bestanden voor bekende schadelijke code, kan een antivirusprogramma kan ertoe leiden dat het intrekken van gelaagde bestanden. Omdat gelaagde bestanden het kenmerk 'offline' is ingesteld hebben, wordt u aangeraden overleg met de softwareleverancier voor meer informatie over hoe de oplossing configureren voor het lezen van offlinebestanden overslaan. 

De volgende oplossingen bekend zijn bij het overslaan van offlinebestanden ondersteunen:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (Zie "scannen alleen wat u moet' op de pagina 90 van het PDF-bestand)
- [Kaspersky Anti-virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Back-upoplossingen
Back-upoplossingen kunnen leiden tot het intrekken van gelaagde bestanden zoals anti-virussoftware. Wordt u aangeraden een back-upoplossing van cloud naar het back-up van de Azure-bestandsshare in plaats van een lokale back-product.

### <a name="encryption-solutions"></a>Versleuteling oplossingen
Ondersteuning voor versleuteling oplossingen, is afhankelijk van hoe ze worden geïmplementeerd. Azure File-synchronisatie is bekend dat werkt met:

- BitLocker-versleuteling
- Azure Rights Management Services (Azure RMS) (en verouderde Active Directory RMS)

Azure File-synchronisatie bekend is niet werken met:

- NTFS versleuteld File System (EFS)

In het algemeen ondersteunen Azure bestand Sync interoperabiliteit met coderingsoplossingen voor die zich onder het bestandssysteem, zoals BitLocker, en met oplossingen die zijn geïmplementeerd in de bestandsindeling, zoals BitLocker. Er is geen speciale interoperabiliteit heeft aangebracht voor oplossingen die zich boven het bestandssysteem (zoals NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere oplossingen voor beheer van hiërarchische Storage (HSM)
Er zijn geen andere HSM-oplossingen moeten worden gebruikt met het synchroniseren van Azure-bestand.

## <a name="region-availability"></a>Beschikbaarheid in regio’s
Azure File-synchronisatie is alleen beschikbaar in de volgende regio's Preview-versie:

| Regio | Datacenter-locatie |
|--------|---------------------|
| VS - west | Californië, Verenigde Staten |
| West-Europa | Nederland |
| Zuidoost-Azië | Singapore |
| Australië - oost | Nieuwe Limburg, Australië |

Wij ondersteunen alleen met een Azure-bestandsshare die zich in dezelfde regio bevinden als de Storage-Sync-Service synchroniseren in preview.

## <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Volgende stappen
* [Planning voor de implementatie van een Azure-bestanden](storage-files-planning.md)
* [Azure Files implementeren](storage-files-deployment-guide.md)
* [Azure File synchronisatie implementeren](storage-sync-files-deployment-guide.md)
