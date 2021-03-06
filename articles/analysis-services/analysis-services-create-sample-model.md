---
title: Lägg till en exempel-tabellmodell för Azure Analysis Services-server | Microsoft Docs
description: Lär dig hur du lägger till en exempel-modell i Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>Självstudier: Lägga till en exempel-modell

I kursen får du lägga till en modell för Adventure Works exempel till servern. Exempel modellen är en fullständig version av de Adventure Works Internet försäljning (1200) datamodeller kursen. En exempel-modell är användbart för att testa modellen management, anslutning med verktyg och program och datafrågor modellen.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här kursen behöver du:

- En Azure Analysis Services-server
- Administratörsbehörighet för servern

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>Skapa en exempel-modell

1. I server **översikt**, klickar du på **ny modell**.

    ![Skapa en exempel-modell](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. I **ny modell** > **väljer en datakälla**, kontrollera **exempeldata** är markerad och klicka sedan på **Lägg till**.

    ![Välj exempeldata](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. I **översikt**, kontrollera den `adventureworks` exemplet skapas.

    ![Välj exempeldata](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Rensa resurser

Exempel modellen använder cacheminne resurser. Om du inte använder modellen exempel för testning, bör du ta bort den från din server.

> [!NOTE]
> Här beskrivs hur du tar bort en modell från en server med hjälp av SSMS. Du kan också ta bort en modell med förhandsversionen av designer Web-funktionen.

1. I SSMS > **Object Explorer**, klickar du på **Anslut** > **Analysis Services**.

2. I **Anslut till Server**, klistra in servernamnet, sedan i **autentisering**, Välj **Active Directory - Universal med stöd för MFA**, ange ditt användarnamn och klicka sedan på **Ansluta**.

    ![Logga in](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. I **Object Explorer**, högerklicka på den `adventureworks` exempel databasen och klicka sedan på **ta bort**.

    ![Ta bort exempeldatabasen](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Nästa steg 

[Ansluta i Power BI Desktop](analysis-services-connect-pbi.md)   
[Hantera databasroller och användare](analysis-services-database-users.md)


