---
title: 'Zelfstudie: Azure Active Directory-integratie met Kantega SSO voor Bamboe | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kantega SSO voor Bamboe.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: cc259bb6f9bdb2293b6935e45e2df52b9fee6873
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Zelfstudie: Azure Active Directory-integratie met Kantega SSO voor Bamboe

In deze zelfstudie leert u hoe Kantega SSO voor Bamboe integreren met Azure Active Directory (Azure AD).

Kantega SSO voor Bamboe integreren met Azure AD biedt de volgende voordelen:

- U kunt beheren in Azure AD die toegang tot Kantega SSO voor Bamboe heeft
- U kunt uw gebruikers automatisch ophalen aangemeld bij Kantega SSO voor Bamboe (Single Sign-On) inschakelen met hun Azure AD-accounts
- U kunt uw accounts op één centrale locatie - en de Azure-portal beheren

Als u weten van meer informatie over de integratie van de SaaS-app met Azure AD wilt, Zie [wat is er toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Kantega SSO voor Bamboe, moet u de volgende items:

- Een Azure AD-abonnement
- Een Kantega SSO voor eenmalige aanmelding Bamboe abonnement ingeschakeld

> [!NOTE]
> Test de stappen in deze zelfstudie, raden we niet met behulp van een productieomgeving.

Test de stappen in deze zelfstudie, moet u deze aanbevelingen volgen:

- Gebruik niet uw productieomgeving, tenzij het noodzakelijk is.
- Als u geen een proefabonnement Azure AD-omgeving hebt, kunt u een proefversie van één maand [hier](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving
In deze zelfstudie test u Azure AD eenmalige aanmelding in een testomgeving. Het scenario in deze zelfstudie bestaat uit twee belangrijkste bouwstenen:

1. Kantega SSO voor Bamboe uit de galerie toevoegen
2. Configureren en testen van Azure AD eenmalige aanmelding

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Kantega SSO voor Bamboe uit de galerie toevoegen
Voor het configureren van de integratie van Kantega SSO voor Bamboe in Azure AD, moet u Kantega SSO voor Bamboe uit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Als u wilt toevoegen Kantega SSO voor Bamboe uit de galerie, moet u de volgende stappen uitvoeren:**

1. In de  **[Azure-portal](https://portal.azure.com)**, klik in het linkernavigatievenster op **Azure Active Directory** pictogram. 

    ![Active Directory][1]

2. Navigeer naar **bedrijfstoepassingen**. Ga vervolgens naar **alle toepassingen**.

    ![Toepassingen][2]
    
3. Om de nieuwe toepassing toevoegen, klikt u op **nieuwe toepassing** knop boven aan het dialoogvenster.

    ![Toepassingen][3]

4. Typ in het zoekvak **Kantega SSO voor Bamboe**.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_search.png)

5. Selecteer in het deelvenster resultaten **Kantega SSO voor Bamboe**, en klik vervolgens op **toevoegen** om toe te voegen van de toepassing.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configureren en testen van Azure AD eenmalige aanmelding
In deze sectie kunt u configureren en testen Azure AD eenmalige aanmelding met Kantega SSO voor Bamboe op basis van een testgebruiker 'Britta Simon' genoemd.

Voor eenmalige aanmelding werkt, moet Azure AD weten wat de gebruiker equivalent in Kantega SSO voor Bamboe is aan een gebruiker in Azure AD. Met andere woorden, moet een koppeling relatie tussen een Azure AD-gebruiker en de betreffende gebruiker in Kantega SSO voor Bamboe tot stand worden gebracht.

Wijs in Kantega SSO voor Bamboe, de waarde van de **gebruikersnaam** in Azure AD als de waarde van de **gebruikersnaam** de relatie van de koppeling tot stand brengen.

Om te configureren en testen van Azure AD eenmalige aanmelding met Kantega SSO voor Bamboe, moet u de volgende bouwstenen voltooien:

1. **[Configureren van Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  : als u wilt dat uw gebruikers kunnen deze functie gebruiken.
2. **[Maken van een Azure AD-testgebruiker](#creating-an-azure-ad-test-user)**  - voor het testen van Azure AD eenmalige aanmelding met Britta Simon.
3. **[Maken van een SSO Kantega voor de testgebruiker Bamboe](#creating-a-kantega-sso-for-bamboo-test-user)**  - Kantega SSO voor Bamboe die is gekoppeld aan de Azure AD-weergave van de gebruiker van een exemplaar van Britta Simon bevatten.
4. **[Toewijzen van de Azure AD-testgebruiker](#assigning-the-azure-ad-test-user)**  - Britta Simon gebruik van Azure AD eenmalige aanmelding inschakelen.
5. **[Testen van eenmalige aanmelding](#testing-single-sign-on)**  : om te controleren of de configuratie werkt.

### <a name="configuring-azure-ad-single-sign-on"></a>Eenmalige aanmelding Azure AD configureren

In dit gedeelte Azure AD eenmalige aanmelding inschakelen in de Azure portal en eenmalige aanmelding in uw Kantega SSO voor Bamboe toepassing configureren.

**Voor het configureren van Azure AD eenmalige aanmelding met Kantega SSO voor Bamboe, moet u de volgende stappen uitvoeren:**

1. In de Azure-portal op de **Kantega SSO voor Bamboe** toepassing Integratiepagina, klikt u op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren][4]

2. Op de **eenmalige aanmelding** dialoogvenster Selecteer **modus** als **op basis van SAML aanmelding** voor eenmalige aanmelding inschakelen.
 
    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_samlbase.png)

3. In **IDP** modus gestart op de **Kantega SSO voor Bamboe domein en URL's** sectie de volgende stap uitvoeren:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url1.png)
    
    a. In de **id** textbox, typ een URL met het volgende patroon volgen:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. In de **antwoord-URL** textbox, typ een URL met het volgende patroon volgen:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. In **SP** geïnitieerd modus selectievakje **weergeven geavanceerde instellingen voor URL** en voer de volgende stap:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url2.png)
    
    In de **aanmeldings-URL** textbox, typ een URL met het volgende patroon volgen:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Deze waarden zijn niet echt. Deze waarden bijwerken met de werkelijke id, antwoord-URL en aanmeldings-URL. Deze waarden worden ontvangen tijdens de configuratie van bamboe invoegtoepassing die verderop in de zelfstudie wordt beschreven.

5. Op de **SAML-certificaat voor ondertekening van** sectie, klikt u op **Metadata XML** en sla het bestand met metagegevens op uw computer.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_certificate.png) 

6. Klik op **opslaan** knop.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_400.png)
    
7. In een ander browservenster, meld u aan bij uw Bamboe op de lokale server als beheerder.

8. Beweeg de muisaanwijzer op het tandwiel en klik op de **invoegtoepassingen**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon1.png)

9. Klik onder sectie tabblad invoegtoepassingen op **vinden van nieuwe invoegtoepassingen**. Search **Kantega SSO voor Bamboe (SAML & Kerberos)** en klik op **installeren** knop voor het installeren van de nieuwe SAML-invoegtoepassing.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon2.png)

10. De installatie van de invoegtoepassing wordt gestart.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon21.png)

11. Zodra de installatie voltooid is. Klik op **Sluiten**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon33.png)

12. Klik op **Beheren**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon34.png)
    
13. Klik op **configureren** voor het configureren van de nieuwe invoegtoepassing.    

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon3.png)

14. In de **SAML** sectie. Selecteer **Azure Active Directory (Azure AD)** van de **toevoegen identiteitsprovider** vervolgkeuzelijst.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon4.png)

15. Abonnement als selecteren **Basic**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon5.png)

16. Op de **App-eigenschappen** sectie, voert u de volgende stappen:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon6.png)

    a. Kopieer de **App ID URI** waarde en deze gebruiken als **id, de antwoord-URL en de aanmeldings-URL** op de **Kantega SSO voor Bamboe domein en URL's** sectie in Azure-portal.

    b. Klik op **Volgende**.

17. Op de **metagegevens importeren** sectie, voert u de volgende stappen:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon7.png)

    a. Selecteer **metagegevensbestand op mijn computer**, en de metagegevens-uploadbestand, die u hebt gedownload vanuit Azure-portal.

    b. Klik op **Volgende**.

18. Op de **en SSO** sectie, voert u de volgende stappen:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon8.png)

    a. Naam van de identiteitsprovider in toevoegen **identiteit providernaam** textbox (bijvoorbeeld Azure AD).

    b. Klik op **Volgende**.

19. Controleer of het certificaat voor ondertekening en klikt u op **volgende**.  

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon9.png)

20. Op de **Bamboe gebruikersaccounts** sectie, voert u de volgende stappen:

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon10.png)

    a. Selecteer **gebruikers in de Bamboe interne Directory maken, indien nodig** en voer de juiste naam van de groep voor gebruikers (kan meerdere Nee. van groepen van elkaar gescheiden door komma's).

    b. Klik op **Volgende**.

21. Klik op **Voltooien**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon11.png)

22. Op de **bekend domeinen voor Azure AD** sectie, voert u de volgende stappen: 

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon12.png)

    a. Selecteer **domeinen bekend** in het linkerpaneel van de pagina.

    b. Geef de domeinnaam in de **domeinen bekend** textbox.

    c. Klik op **Opslaan**.

> [!TIP]
> U kunt nu een beknopte versie van deze instructies binnen lezen de [Azure-portal](https://portal.azure.com), terwijl u de app instelt!  Na het toevoegen van deze app uit de **Active Directory > bedrijfstoepassingen** sectie, klikt u op de **Single Sign-On** tabblad en toegang tot de ingesloten documentatie via de **configuratie** sectie onderaan. U kunt meer lezen over de ingesloten documentatie-functie: [embedded-documentatie voor Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken
Het doel van deze sectie is het een testgebruiker maken in de Azure portal Britta Simon aangeroepen.

![Azure AD-gebruiker maken][100]

**Als u wilt een testgebruiker maken in Azure AD, moet u de volgende stappen uitvoeren:**

1. In de **Azure-portal**, klik op het navigatiedeelvenster links **Azure Active Directory** pictogram.

    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_01.png) 

2. Als u wilt weergeven in de lijst met gebruikers, gaat u naar **gebruikers en groepen** en klik op **alle gebruikers**.
    
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_02.png) 

3. Openen van de **gebruiker** dialoogvenster, klikt u op **toevoegen** boven aan het dialoogvenster.
 
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_03.png) 

4. Op de **gebruiker** dialoogvenster pagina, voert u de volgende stappen uit:
 
    ![Een Azure AD-testgebruiker maken](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_04.png) 

    a. In de **naam** textbox type **BrittaSimon**.

    b. In de **gebruikersnaam** textbox type de **e-mailadres** van BrittaSimon.

    c. Selecteer **wachtwoord weergeven** en noteer de waarde van de **wachtwoord**.

    d. Klik op **Create**.
 
### <a name="creating-a-kantega-sso-for-bamboo-test-user"></a>Maken van een SSO Kantega voor Bamboe testgebruiker

Om Azure AD-gebruikers zich aanmelden bij Bamboe, moeten ze worden ingericht in Bamboe. In Kantega SSO voor Bamboe is inrichting een handmatige taak.

**Voor het inrichten van een gebruikersaccount, moet u de volgende stappen uitvoeren:**

1. Meld u aan bij uw Bamboe op de lokale server als beheerder.

2. Beweeg de muisaanwijzer op het tandwiel en klik op de **Gebruikersbeheer**.

    ![Werknemer toevoegen](./media/active-directory-saas-kantegassoforbamboo-tutorial/user1.png) 

3. Klik op **gebruikers**. Onder de **gebruiker toevoegen** sectie, dit stappen uitvoeren:

    ![Werknemer toevoegen](./media/active-directory-saas-kantegassoforbamboo-tutorial/user2.png) 

    a. In de **gebruikersnaam** textbox, typ het e-mailadres van gebruiker, zoals Brittasimon@contoso.com.
    
    b. In de **wachtwoord** textbox, typt u het wachtwoord van gebruiker.

    c. In de **wachtwoord bevestigen** textbox, voer het wachtwoord van gebruiker opnieuw in.
    
    d. In de **volledige naam** textbox, volledige naam van de gebruiker zoals Britta Simon type.
    
    e. In de **e** textbox, typ het e-mailadres van gebruiker, zoals Brittasimon@contoso.com.
    
    f. Klik op **Opslaan**.

### <a name="assigning-the-azure-ad-test-user"></a>Toewijzen van de testgebruiker Azure AD

In deze sectie maakt inschakelen u Britta Simon gebruikt Azure eenmalige aanmelding toegang verlenen aan Kantega SSO voor Bamboe.

![Gebruiker toewijzen][200] 

**Britta Simon om aan te wijzen Kantega SSO voor Bamboe, moet u de volgende stappen uitvoeren:**

1. Open de weergave toepassingen in de Azure-portal en gaat u naar de directoryweergave en gaat u naar **bedrijfstoepassingen** klikt u vervolgens op **alle toepassingen**.

    ![Gebruiker toewijzen][201] 

2. Selecteer in de lijst met toepassingen **Kantega SSO voor Bamboe**.

    ![Eenmalige aanmelding configureren](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_app.png) 

3. Klik in het menu aan de linkerkant op **gebruikers en groepen**.

    ![Gebruiker toewijzen][202] 

4. Klik op **toevoegen** knop. Selecteer vervolgens **gebruikers en groepen** op **toevoegen toewijzing** dialoogvenster.

    ![Gebruiker toewijzen][203]

5. Op **gebruikers en groepen** dialoogvenster Selecteer **Britta Simon** in de lijst gebruikers.

6. Klik op **Selecteer** knop op **gebruikers en groepen** dialoogvenster.

7. Klik op **toewijzen** knop op **toevoegen toewijzing** dialoogvenster.
    
### <a name="testing-single-sign-on"></a>Testen van eenmalige aanmelding

In deze sectie kunt u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster testen.

Als u op de Kantega SSO Bamboe tegel in het deelvenster toegang, u moet ophalen automatisch aangemeld bij uw Kantega SSO voor Bamboe toepassing.
Zie voor meer informatie over het toegangsvenster [Inleiding tot het toegangsvenster](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-Apps met Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Wat is de toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_203.png

