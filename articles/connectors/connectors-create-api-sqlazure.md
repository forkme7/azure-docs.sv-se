---
title: "Lägg till Azure SQL Database-koppling i dina Logic Apps | Microsoft Docs"
description: "Översikt över Azure SQL Database-anslutningen med REST API-parametrar"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4313ead0c31ab2e72238701d58dc2f321f116fa6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Kom igång med Azure SQL Database-koppling
Med Azure SQL Database-kopplingen kan skapa arbetsflöden för din organisation som hanterar data i tabeller. 

Med SQL-databas måste du:

* Skapa ditt arbetsflöde genom att lägga till en ny kund till en databas för kunder eller uppdatera en ordning i en order-databas.
* Använd åtgärder för att hämta en rad med data, infoga en ny rad och även ta bort. Till exempel när en post har skapats i Dynamics CRM Online (en utlösare), sedan infoga en rad i en Azure SQL Database (en åtgärd). 

Den här artikeln visar hur du använder SQL Database-anslutningen i en logikapp och visar även åtgärderna.

Läs mer om Logic Apps i [vad är logic apps](../logic-apps/logic-apps-overview.md) och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-sql-database"></a>Anslut till Azure SQL Database
Innan din logikapp får åtkomst till alla tjänster måste du först skapa en *anslutning* till tjänsten. En anslutning kan du ansluta en logikapp och en annan tjänst. Till exempel för att ansluta till SQL-databas måste du först skapa en SQL-databas *anslutning*. Om du vill skapa en anslutning kan du ange de autentiseringsuppgifter som du vanligtvis använder för att få åtkomst till tjänsten som du ansluter till. Så ange dina autentiseringsuppgifter för SQL-databas för att skapa anslutningen i SQL-databas. 

#### <a name="create-the-connection"></a>Skapa anslutningen
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Använda en utlösare
Den här anslutningen har inte utlösare. Använd andra utlösare för att starta logikapp, till exempel en upprepning utlösare, en HTTP-Webhook-utlösare, utlösare som är tillgängliga med övriga kopplingar och mycket mer. [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) innehåller ett exempel.

## <a name="use-an-action"></a>Använda en åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Klicka på plustecknet. Du ser flera alternativ: **lägga till en åtgärd**, **Lägg till ett villkor**, eller en av de **mer** alternativ.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Välj **lägga till en åtgärd**.
3. I rutan skriver du ”sql” om du vill hämta en lista över alla tillgängliga åtgärder.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. I det här exemplet väljer **SQL Server - Get-raden**. Om det finns redan en anslutning, väljer du den **tabellnamn** från nedrullningsbara listan, och ange den **rad-ID** du vill återställa.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Om du uppmanas att ange anslutningsinformation anger du information för att skapa anslutningen. [Skapa anslutningen](connectors-create-api-sqlazure.md#create-the-connection) i den här artikeln beskriver dessa egenskaper. 
   
   > [!NOTE]
   > I det här exemplet returnerar vi en rad från en tabell. Lägg till en annan åtgärd som skapar en fil med hjälp av fälten från tabellen om du vill se data i den här raden. Till exempel lägga till en OneDrive-åtgärd som fälten Förnamn och efternamn används för att skapa en ny fil i molnet storage-konto. 
   > 
   > 
5. **Spara** dina ändringar (övre vänstra hörnet i verktygsfältet). Din logikapp sparas och aktiveras automatiskt.

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/sql/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga kopplingar i Logic Apps på [API: er listan](apis-list.md).

