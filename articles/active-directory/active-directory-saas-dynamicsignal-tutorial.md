---
title: 'Självstudier: Azure Active Directory-integrering med dynamiska signalen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dynamiska Signal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 863f7340-b065-4f59-b092-daa67da6f703
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 48ea33c2542abfc318a326556aa0c47f568e3335
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dynamic-signal"></a>Självstudier: Azure Active Directory-integrering med dynamiska Signal

I kursen får lära du att integrera dynamiska Signal med Azure Active Directory (AD Azure).

Integrera dynamiska Signal med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till dynamisk Signal.
- Du kan aktivera användarna att automatiskt hämta loggat in på Dynamic Signal (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med dynamiska Signal, behöver du följande:

- En Azure AD-prenumeration
- En dynamisk Signal enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till dynamiska signalen från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dynamic-signal-from-the-gallery"></a>Att lägga till dynamiska signalen från galleriet
Du måste lägga till dynamiska signalen från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av dynamisk Signal i Azure AD.

**Utför följande steg för att lägga till dynamiska signalen från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **dynamiska Signal**väljer **dynamiska Signal** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Dynamisk Signal i resultatlistan](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med dynamiska Signal baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i dynamiska Signal till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i dynamiska Signal upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med dynamiska Signal, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare dynamiska Signal](#create-a-dynamic-signal-test-user)**  – du har en motsvarighet för Britta Simon dynamiska signal som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för dynamiska Signal.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med dynamiska Signal:**

1. I Azure-portalen på den **dynamiska Signal** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_samlbase.png)

3. På den **dynamiska Signal domän och URL: er** avsnittet, utför följande steg:
 
    ![Dynamiska Signal domän och URL: er med enkel inloggning information](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.voicestorm.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.voicestorm.com`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.voicestorm.com/User/SsoResponse`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [dynamiska Signal klienten supportteamet](mailto:support@dynamicsignal.com) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_400.png)
    
6. På den **dynamisk Signal konfiguration** klickar du på **konfigurera dynamisk Signal** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Dynamisk Signal konfiguration](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_configure.png) 

7. Konfigurera enkel inloggning på **dynamiska Signal** sida, måste du skicka den hämtade **certifikat (Base64), Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** till [dynamisk Signal supportteamet](mailto:support@dynamicsignal.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-dynamicsignal-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-dynamic-signal-test-user"></a>Skapa en dynamisk Signal testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon dynamiska signal. Dynamisk Signal stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt dynamiska Signal om den inte finns.

>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [dynamiska Signal supportteamet](mailto:support@dynamicsignal.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till dynamisk Signal.

![Tilldela rollen][200] 

**Om du vill tilldela dynamiska Signal Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **dynamiska Signal**.

    ![Dynamisk Signal länken i listan med program](./media/active-directory-saas-dynamicsignal-tutorial/tutorial_dynamicsignal_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen dynamiska Signal på åtkomstpanelen du ska hämta automatiskt loggat in i tillämpningsprogrammet dynamiska Signal.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dynamicsignal-tutorial/tutorial_general_203.png

