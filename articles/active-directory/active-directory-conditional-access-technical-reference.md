---
title: Azure Active Directory voorwaardelijke toegang technische naslaginformatie | Microsoft Docs
description: "Informatie over het gebruik van voorwaardelijk toegangsbeheer in Azure Active Directory. Geef de voorwaarden voor gebruikers te verifiëren en beheren van toegang tot uw toepassing. Wanneer de opgegeven voorwaarden wordt voldaan, worden gebruikers zijn geverifieerd en toegang tot uw toepassing."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/19/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: c9e44a696010541ab153597bb1ab5d556c2e7b65
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory voorwaardelijke toegang technische naslaginformatie

U kunt [voorwaardelijke toegang van Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) af te stemmen hoe geautoriseerde gebruikers toegang uw resources tot hebben.  

Dit onderwerp bevat informatie over ondersteuning voor de volgende configuratieopties voor beleid voor voorwaardelijke toegang: 

- Toewijzingen voor cloud-toepassingen

- Apparaat platform voorwaarde 

- Voorwaarde voor client-toepassingen

- Vereisten voor toepassingsimplementatie goedgekeurde client



## <a name="cloud-apps-assignments"></a>Toewijzingen voor cloud-apps

Wanneer u een beleid voor voorwaardelijke toegang configureert, moet u [Selecteer de cloud-apps die gebruikmaken van uw beleid](active-directory-conditional-access-azure-portal.md#who). 

![Selecteer de cloud-apps voor uw beleid](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft cloud-toepassingen

U kunt beleid voor voorwaardelijke toegang toewijzen aan de volgende cloud-apps van Microsoft:

- Azure Information Protection - [meer informatie](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (inclusief OneDrive voor bedrijven)

- Microsoft Power BI 

- Microsoft Visual Studio teamservices

- Microsoft Teams


### <a name="other-applications"></a>Andere toepassingen 

Naast de Microsoft cloud-apps kunt u een voorwaardelijk toegangsbeleid toewijzen aan de volgende typen van cloud-apps:

- Azure AD-toepassingen

- Vooraf geïntegreerde federatieve toepassing software als een dienst (SaaS)

- Toepassingen die gebruikmaken van wachtwoord eenmalige aanmelding (SSO)

- Line-of-business-toepassingen

- Toepassingen die gebruikmaken van Azure AD-toepassingsproxy


## <a name="device-platform-condition"></a>Apparaat platform voorwaarde

U kunt de voorwaarde voor het platform van apparaat voor het koppelen van het beleid voor het besturingssysteem op een client te configureren in een beleid voor voorwaardelijke toegang. Voorwaardelijke toegang van Azure AD ondersteunt de volgende apparaatplatforms:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Het toegangsbeleid naar de client OS koppelen](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Voorwaarde voor client-apps 

Wanneer u een beleid voor voorwaardelijke toegang configureert, kunt u [client-apps selecteert](active-directory-conditional-access-azure-portal.md#client-apps) voor de client app-voorwaarde. De client-apps probleem verlenen of toegang blokkeren als er een toegangspoging wordt uitgevoerd vanaf de volgende typen van de client-apps instellen:

- Browser
- Mobiele apps en bureaublad-apps

![Toegang beheren voor client-apps](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Ondersteunde browsers 

Toegang tot de browser beheren met behulp van de **Browser** optie in uw beleid voor voorwaardelijke toegang. Alleen wanneer de toegang wordt geprobeerd met een ondersteunde browser, wordt toegang verleend. Bij een toegangspoging is aangevraagd door een niet-ondersteunde browser, wordt de poging wordt geblokkeerd.

![Toegang beheren voor ondersteunde browsers](./media/active-directory-conditional-access-technical-reference/05.png)

In uw beleid voor voorwaardelijke toegang, zijn de volgende browsers worden ondersteund: 


| OS                     | Browsers                            | Ondersteuning     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, rand Chrome     | ![Selecteren][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome           | ![Selecteren][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Selecteren][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Selecteren][1] |
| Android                | Chrome, Intune Managed Browser      | ![Selecteren][1] |
| Windows Phone          | Internet Explorer rand             | ![Selecteren][1] |
| Windows Server 2016    | Internet Explorer rand             | ![Selecteren][1] |
| Windows Server 2016    | Chrome                              | Binnenkort beschikbaar |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Selecteren][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Selecteren][1] |
| macOS                  | Chrome, Safari                      | ![Selecteren][1] |


> [!NOTE]
> Voor ondersteuning van Chrome, moet u Windows 10 auteurs Update (versie 1703) of hoger.<br>
> U kunt installeren [deze extensie](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Ondersteunde mobiele toepassingen en bureaublad-clients

Client en app-toegang beheren met behulp van de **mobiele apps en bureaublad-clients** optie in uw beleid voor voorwaardelijke toegang. Alleen wanneer de toegang wordt geprobeerd met een ondersteunde mobiele app of bureaubladclient toegang verleend. Bij een toegangspoging is aangevraagd door een niet-ondersteunde app of de client, wordt de poging wordt geblokkeerd.

![Toegang beheren voor ondersteunde mobiele apps of bureaubladclients](./media/active-directory-conditional-access-technical-reference/06.png)

De volgende apps voor mobiele en bureaublad-clients ondersteuning voor voorwaardelijke toegang voor Office 365 en andere toepassingen Azure AD-connected-service:


| Client-apps| Target-Service| Platform |
| --- | --- | --- |
| Azure RemoteApp| Azure RemoteApp-service| Windows 10, Windows 8.1, Windows 7, iOS, Android en Mac OS X|
| Dynamics CRM-app| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS en Android|
| Agenda-mail/mensen app, Outlook 2016 Outlook 2013 (met moderne verificatie)| Office 365 Exchange Online| Windows 10|
| Beleid voor apps MFA en locatie. Apparaten op basis van beleid worden niet ondersteund.| Alle services van de app mijn Apps| Android en iOS|
| Services van Microsoft-Teams - Hiermee bepaalt u alle services die ondersteuning bieden voor Microsoft-Teams en alle bijbehorende Client-Apps - Windows-bureaublad, iOS, Android, WP en webclient| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS, Android en Mac OS|
| Apps van Office 2016, Office 2013 (met moderne verificatie), OneDrive synchroniseren client (Zie [notities](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Apps van Office 2016, Office Universal-apps, Office 2013 (met moderne verificatie), OneDrive sync-client (Zie [notities](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), Office-groepen ondersteuning voor de toekomst zijn gepland, ondersteuning voor SharePoint-app is gepland voor de toekomst| Office 365 SharePoint Online| Windows 10|
| Office 2016 voor Mac OS (Word, Excel, PowerPoint, OneNote alleen). OneDrive voor bedrijven ondersteuning voor de toekomst worden gepland| Office 365 SharePoint Online| Mac OS X|
| Office mobile-apps| Office 365 SharePoint Online| voor iOS, Android|
| De app Yammer Office| Office 365 Yammer| Windows 10, iOS, Android|
| Outlook 2016 (Office voor Mac OS)| Office 365 Exchange Online| Mac OS X|
| Outlook 2016, Outlook 2013 (met moderne verificatie), Skype voor bedrijven (met moderne verificatie)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Mobiele app voor Outlook| Office 365 Exchange Online| iOS|
| Power BI-app. De Power BI-app voor Android biedt momenteel geen ondersteuning voor voorwaardelijke toegang op basis van apparaten.| Power BI-service| Windows 10, Windows 8.1, Windows 7 en iOS|
| Skype voor bedrijven| Office 365 Exchange Online| Android-, IOS|
| Visual Studio Team Services-app| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS en Android|




## <a name="approved-client-app-requirement"></a>Goedgekeurde app vereiste 

Clientverbindingen beheren met behulp van de **goedgekeurde app vereist** optie in uw beleid voor voorwaardelijke toegang. Alleen wanneer er een verbindingspoging wordt gedaan door een goedgekeurde clientapp, wordt toegang verleend.

![Toegang beheren voor goedgekeurde client-apps](./media/active-directory-conditional-access-technical-reference/21.png)

De volgende ClientApps kunnen worden gebruikt met de vereiste goedgekeurde client-toepassing:


- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype voor bedrijven
- Microsoft Teams
- Microsoft Visio
- Microsoft Word



**Opmerkingen**

- De goedgekeurde apps ondersteuning voor de Intune mobile application management-functie.

- De **goedgekeurde app vereist** vereiste:

    - Biedt alleen ondersteuning voor het iOS- en Android voor [apparaat platform voorwaarde](#device-platforms-condition).

    - Biedt geen ondersteuning voor de **Browser** optie voor de [client apps voorwaarde](#supported-browsers).
    
    - Vervangt de **mobiele apps en bureaublad-clients** optie voor de [client apps voorwaarde](#supported-mobile-apps-and-desktop-clients) wanneer deze optie is geselecteerd.


## <a name="next-steps"></a>Volgende stappen

- Zie voor een overzicht van voorwaardelijke toegang [voorwaardelijke toegang in Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Als u klaar bent voor het configureren van beleidsregels voor voorwaardelijke toegang in uw omgeving, raadpleegt u de [aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


