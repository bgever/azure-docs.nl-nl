---
title: Optimaliseren van doorvoer van VM-netwerk | Microsoft Docs
description: Informatie over het optimaliseren van doorvoer van virtuele machine van Azure-netwerk.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 2f7a65d32f662d7e265e58c5fe7d9dea81a4e63c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Netwerkdoorvoer voor Azure virtual machines optimaliseren

Azure virtuele machines (VM) hebben netwerk standaardinstellingen die verder kunnen worden geoptimaliseerd voor netwerkdoorvoer. In dit artikel wordt beschreven hoe netwerkdoorvoer optimaliseren voor Microsoft Azure Windows en Linux-virtuele machines, inclusief belangrijke distributies zoals Ubuntu en CentOS Red Hat.

## <a name="windows-vm"></a>Windows VM

Als uw virtuele machine van Windows wordt ondersteund met [versnelde netwerken](virtual-network-create-vm-accelerated-networking.md), het inschakelen van deze functie is de optimale configuratie voor de doorvoer. Met behulp van ontvangen Side Scaling (RSS) bereiken hogere maximale doorvoer dan een virtuele machine zonder RSS voor alle andere Windows-VM. RSS mogelijk in een virtuele machine van Windows standaard uitgeschakeld. Voer de volgende stappen uit om te bepalen of RSS is ingeschakeld en in te schakelen als deze uitgeschakeld.

1. Voer de `Get-NetAdapterRss` PowerShell-opdracht om te zien als de RSS is ingeschakeld voor een netwerkadapter. In de volgende voorbeelduitvoer geretourneerd door de `Get-NetAdapterRss`, RSS is niet ingeschakeld.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Voer de volgende opdracht om in te schakelen van RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    De vorige opdracht heeft geen uitvoer. De opdracht de NIC-instellingen, waardoor tijdelijke verbinding wordt verbroken voor ongeveer een minuut gewijzigd. Er verschijnt een dialoogvenster Reconnecting tijdens de verbinding wordt verbroken. Verbinding is doorgaans hersteld nadat de derde poging.
3. Controleer of RSS is ingeschakeld in de virtuele machine door te voeren de `Get-NetAdapterRss` opdracht opnieuw. Als dit lukt, wordt de volgende voorbeelduitvoer wordt geretourneerd:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Virtuele Linux-machine

RSS is altijd ingeschakeld in een Azure Linux VM standaard. Linux-kernels die zijn uitgebracht sinds oktober 2017 nieuwe netwerk optimalisaties opties opnemen waarmee u een Linux-VM voor hogere netwerkdoorvoer.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu voor nieuwe implementaties

De kernel Ubuntu Azure biedt de beste netwerkprestaties op Azure en is sinds de kernel standaard 21 September 2017. Wilt u deze kernel, eerst installeren laatste ondersteunde versie van 16.04-TNS, zoals hieronder wordt beschreven:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Nadat het maken voltooid is, voert u de volgende opdrachten om op te halen van de meest recente updates. Deze stappen werken ook voor virtuele machines die de kernel Ubuntu Azure momenteel wordt uitgevoerd.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

De volgende optionele opdrachtenset zijn mogelijk handig voor bestaande Ubuntu-implementaties die al de Azure-kernel, maar die niet meer bijgewerkt met fouten.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel-upgrade voor een bestaande virtuele machines

De prestaties aanzienlijk doorvoer kan worden bereikt door het upgraden van de kernel Azure Linux. Om te controleren of u deze kernel hebt, Controleer uw kernelversie.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Als uw virtuele machine niet de Azure-kernel heeft, wordt het versienummer meestal begint met '4.4'. In deze gevallen moet u de volgende opdrachten uitvoeren als hoofdmap.
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Om de meest recente optimalisaties, is het raadzaam een virtuele machine maken met de laatste ondersteunde versie door te geven van de volgende parameters:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
Nieuwe en bestaande virtuele machines kunnen profiteren van de meest recente Linux Integration Services (LIS) installeren.
De optimalisatie van doorvoer wordt LIS, vanaf 4.2.2-2, hoewel latere versies verdere verbeteringen bevatten. Voer de volgende opdrachten voor het installeren van de meest recente LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Om de optimalisaties, is het raadzaam een virtuele machine maken met de laatste ondersteunde versie door te geven van de volgende parameters:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
Nieuwe en bestaande virtuele machines kunnen profiteren van de meest recente Linux Integration Services (LIS) installeren.
De optimalisatie van doorvoer wordt LIS, vanaf 4.2. Voer de volgende opdrachten om te downloaden en installeren van LIS:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Meer informatie over Linux Integration Services versie 4.2 voor Hyper-V door de [downloadpagina](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Volgende stappen
* Nu de virtuele machine is geoptimaliseerd, gaat u naar het resultaat met [bandbreedte/doorvoer testen van de virtuele machine van Azure](virtual-network-bandwidth-testing.md) voor uw scenario.
* Klik hier als u meer wilt weten met [Azure Virtual Network Veelgestelde vragen (FAQ)](virtual-networks-faq.md)
