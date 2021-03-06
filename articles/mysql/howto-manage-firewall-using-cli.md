---
title: Maken en beheren van Azure-Database voor firewallregels MySQL met Azure CLI | Microsoft Docs
description: In dit artikel wordt beschreven hoe maken en beheren van Azure-Database voor firewallregels MySQL via Azure CLI-opdrachtregel.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 66d192287eeaaaa82c0f61f8aa13b8bf7bf8cd47
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/26/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Maken en beheren van Azure-Database voor firewallregels MySQL met behulp van de Azure CLI
Firewallregels op serverniveau kunnen beheerders toegang tot een Azure-Database voor de MySQL-Server beheren vanaf een specifiek IP-adres of een bereik met IP-adressen. U handige Azure CLI-opdrachten gebruikt, kunt u maken, bijwerken, verwijderen, de lijst en firewallregels voor het beheren van uw server weergegeven. Zie voor een overzicht van Azure-Database voor MySQL firewalls [Azure Database voor de MySQL-firewallregels voor server](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Vereisten
* [Installeer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Installeer de Azure Python SDK voor PostgreSQL en MySQL-Services.
* Installeer de Azure CLI-onderdeel voor PostgreSQL en MySQL-services.
* Maak een Azure-Database voor de MySQL-server.

## <a name="firewall-rule-commands"></a>Firewall-regel opdrachten:
De **az mysql server-firewallregel** opdracht wordt gebruikt met Azure CLI voor het maken, verwijderen, lijst, weergeven en bijwerken van firewallregels.

Opdrachten:
- **Maak**: een firewallregel op Azure MySQL-server maken.
- **verwijderen**: verwijderen van een firewallregel voor Azure MySQL-server.
- **lijst**: lijst van de firewallregels voor Azure MySQL-server.
- **weergeven**: de details van een server op Azure MySQL firewallregel weergeven.
- **bijwerken**: een firewallregel voor Azure MySQL-server bijwerken.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Aanmelden bij Azure en de lijst met uw Azure-Database voor MySQL-Servers
Azure CLI veilig verbinding met uw Azure-account met behulp van de **az aanmelding** opdracht.

1. Voer de volgende opdracht vanaf de opdrachtregel:
```azurecli
az login
```
Met deze opdracht wordt de uitvoer een code te gebruiken in de volgende stap.

2. Een webbrowser gebruiken om de pagina te openen [https://aka.ms/devicelogin](https://aka.ms/devicelogin), en voer de code.

3. Meld u via het venster aan met uw Azure-referenties.

4. Nadat uw aanmelding is geautoriseerd, wordt een lijst met abonnementen in de console afgedrukt. Kopieer de ID van het gewenste abonnement voor het instellen van het huidige abonnement gebruiken.
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. Lijst van de Azure-Databases voor een MySQL-servers voor uw abonnement en de resource-groep als u niet zeker van de namen bent.

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   Let op het kenmerk name in de aanbieding, u de MySQL-server om te werken moet op opgeven. Indien nodig, Controleer de gegevens voor die server en het gebruik van het kenmerk name om te controleren of dat deze juist is:

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lijst met firewallregels voor Azure-Database voor de MySQL-Server 
Met de naam van de server en de naam van de resourcegroep, lijst van de bestaande firewallregels voor server op de server. U ziet dat het kenmerk server name is opgegeven in de **--server** switch en niet in de **--naam** overschakelen.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
De uitvoer bevat de regels, indien aanwezig, in JSON (standaard formatteren). U kunt de **--uitvoertabel** overschakelen naar de resultaten in een beter leesbare tabelindeling.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Een firewallregel maken in Azure-Database voor de MySQL-Server
Met de naam van de Azure MySQL-server en de naam van de resourcegroep, een nieuwe firewallregel maken op de server. Geef een naam voor de regel, evenals de begin-IP en eindigen IP (voor het bieden van toegang tot een bereik met IP-adressen) voor de regel.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Om toegang te verlenen voor een enkel IP-adres, bieden de hetzelfde IP-adres als de eerste IP- en eind-IP, zoals in dit voorbeeld.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
Als dit lukt bevat de uitvoer van de opdracht de details van de firewallregel die u hebt gemaakt, in JSON-indeling (standaard). Als er een storing, bevat de uitvoer van de tekst van het foutbericht in plaats daarvan.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Een firewallregel op Azure-Database voor de MySQL-server bijwerken 
Met de naam van de Azure MySQL-server en de naam van de resourcegroep, een bestaande firewallregel op de server worden bijgewerkt. Geef de naam van de bestaande firewallregel als invoer, evenals het begin IP- en end IP-kenmerken om bij te werken.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Als dit lukt bevat de uitvoer van de opdracht de details van de firewallregel die u hebt bijgewerkt, in JSON-indeling (standaard). Als er een storing, bevat de uitvoer van de tekst van het foutbericht in plaats daarvan.

> [!NOTE]
> Als de firewallregel die niet bestaat, wordt de regel wordt gemaakt door de opdracht update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Firewall regeldetails weergeven in Azure-Database voor de MySQL-Server
Met de naam van de Azure MySQL-server en de naam van de resourcegroep, de bestaande firewall regeldetails weergegeven van de server. Geef de naam van de bestaande firewallregel als invoer.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Als dit lukt bevat de uitvoer van de opdracht de details van de firewallregel die u hebt opgegeven, in JSON-indeling (standaard). Als er een storing, bevat de uitvoer van de tekst van het foutbericht in plaats daarvan.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Een firewallregel op Azure-Database voor de MySQL-Server verwijderen
Met de naam van de Azure MySQL-server en de naam van de resourcegroep, een bestaande firewallregel verwijderen van de server. Geef de naam van de bestaande firewallregel.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
Als dit lukt wordt er geen uitvoer. Bij storingen tekst van het foutbericht wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen
- Meer inzicht te geven over [Azure Database voor firewallregels voor MySQL Server](./concepts-firewall-rules.md).
- [Maken en beheren van Azure-Database voor firewallregels MySQL met de Azure portal](./howto-manage-firewall-using-portal.md).
