---
title: "Självstudier: Azure Active Directory-integrering med Encompass | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Encompass."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3b985248-056f-4d0f-9ce4-c017b38f5b50
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: f7fd72cb934a2fd5d84ba3124d0636319d1c7ddf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-encompass"></a>Självstudier: Azure Active Directory-integrering med Encompass

I kursen får lära du att integrera Encompass med Azure Active Directory (AD Azure).

Integrera Encompass med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Encompass.
- Du kan aktivera användarna att automatiskt hämta loggat in på Encompass (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Encompass, behöver du följande:

- En Azure AD-prenumeration
- En Encompass enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Encompass från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-encompass-from-the-gallery"></a>Att lägga till Encompass från galleriet
Du måste lägga till Encompass från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Encompass i Azure AD.

**Utför följande steg för att lägga till Encompass från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Encompass**väljer **Encompass** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Omfatta i resultatlistan](./media/active-directory-saas-encompass-tutorial/tutorial_encompass_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Encompass baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Encompass motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Encompass upprättas.

I Encompass, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Encompass, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Encompass](#create-a-encompass-test-user)**  – du har en motsvarighet för Britta Simon i Encompass som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Encompass program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Encompass:**

1. I Azure-portalen på den **Encompass** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-encompass-tutorial/tutorial_encompass_samlbase.png)

3. På den **omfattar domän och URL: er** avsnittet, utför följande steg:

    ![Omfatta domän URL: er och enkel inloggning information](./media/active-directory-saas-encompass-tutorial/tutorial_encompass_url.png)

    a. I den **identifierare** textruta ge kunden specifikt värde.

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<subdomain>.voxmobile.com/voxportal/ws/saml/consume`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Encompass supportteamet](http://www.voxmobile.com/contact/) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-encompass-tutorial/tutorial_encompass_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-encompass-tutorial/tutorial_general_400.png)

6. På den **omfattar konfiguration** klickar du på **konfigurera omfattar** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Omfatta konfiguration](./media/active-directory-saas-encompass-tutorial/tutorial_encompass_configure.png) 

7. Konfigurera enkel inloggning på **Encompass** sida, måste du skicka den hämtade **Certificate(Base64), Sign-Out Webbadressen och webbadressen SAML enkel inloggning Service** till [Encompass supportteamet](http://www.voxmobile.com/contact/). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-encompass-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-encompass-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-encompass-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-encompass-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-encompass-test-user"></a>Skapa en testanvändare Encompass

I det här avsnittet skapar du en användare som kallas Britta Simon i Encompass. Arbeta med [Encompass supportteamet](http://www.voxmobile.com/contact/) att lägga till användare i Encompass-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Encompass.

![Tilldela rollen][200] 

**Om du vill tilldela Encompass Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Encompass**.

    ![Länken Encompass i listan med program](./media/active-directory-saas-encompass-tutorial/tutorial_encompass_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Encompass på åtkomstpanelen du bör få automatiskt loggat in på ditt Encompass program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-encompass-tutorial/tutorial_general_203.png

