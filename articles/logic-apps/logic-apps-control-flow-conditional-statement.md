---
title: "Villkorssatser - kör steg baserat på ett villkor - Azure Logic Apps | Microsoft Docs"
description: "Kör steg i din logikapp efter uppfyller ett villkor. Skapa beslutsträd som kör arbetsflöden baserat på angivet villkor."
services: logic-apps
keywords: "villkorssatser beslutsträd"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 486c1053f42ed3becc2c4b60accc993db7f24baa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="conditional-statements-run-steps-based-on-a-condition-in-logic-apps"></a>Villkorssatser: köra steg baserat på ett villkor i logikappar

Använd för att utföra åtgärder endast efter att skicka ett angivet villkor en *villkorlig instruktionen*. Den här strukturen jämför data i arbetsflödet mot specifika värden eller fält. Sedan kan du definiera olika steg för att köra baserat på om huruvida data uppfyller villkoret. Du kan kapsla villkor i varandra.

Anta att du har en logikapp som skickar för många e-postmeddelanden när nya objekt visas på en webbplats RSS-feed. Du kan lägga till en villkorlig instruktion för att skicka e-post endast när den nya artikeln innehåller en specifik sträng. 

> [!TIP]
> Kör olika steg baserat på olika specifika värden med en [ *växla instruktionen* ](../logic-apps/logic-apps-control-flow-switch-statement.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Till exempel i den här artikeln [skapa det här exemplet logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com eller Office 365 Outlook-konto.

## <a name="add-a-condition"></a>Lägg till ett villkor

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna logikappen i logik App Designer.

2. Lägg till ett villkor på den plats som du vill använda. 

   Om du vill lägga till ett villkor mellan stegen muspekaren på pilen där du vill lägga till villkoret. Välj den **plustecknet** (**+**) som visas, väljer sedan **Lägg till ett villkor**. Exempel:

   ![Lägg till villkor mellan steg](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   När du vill lägga till ett villkor i slutet av arbetsflödet, längst ned i din logikapp, Välj **+ nytt steg** > **Lägg till ett villkor**.

3. Under **villkoret**, skapa dina villkor. 

   1. Ange data eller fält som du vill jämföra i den vänstra rutan.

      Från den **lägga till dynamiskt innehåll** kan du välja befintliga fält från din logikapp.

   2. Välj åtgärden som ska utföras i listan mellersta. 
   3. Ange ett värde eller ett fält som villkor i den högra rutan.

   Exempel:

   ![Redigera villkor i grundläggande läge](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   Här är det fullständiga villkoret:

   ![Slutförd](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Om du vill skapa ett mer avancerade villkor eller använda uttryck, Välj **redigera i Avancerat läge**. Du kan använda uttryck som definieras av den [språk i arbetsflödesdefinitionen](../logic-apps/logic-apps-workflow-definition-language.md).
   > 
   > Exempel:
   >
   > ![Redigera villkor i koden](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. Under **Ja om** och **om Nej**, lägga till stegen för att utföra baserat på om villkoret är uppfyllt. Exempel:

   ![Villkor med Ja och inga sökvägar](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Du kan dra befintliga åtgärder i den **Ja om** och **om Nej** sökvägar.

6. Spara din logikapp.

Nu skickas den här logikapp endast e-post när nya objekt i RSS-feeden uppfyller villkoret.

## <a name="json-definition"></a>JSON-definition

Nu när du har skapat en logikapp med hjälp av instruktionen villkorlig känna till hur definitionen för övergripande koden bakom villkorlig instruktionen.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* För att skicka eller rösta på funktioner och förslag, finns det [Azure Logikappar användare feedbackwebbplats](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)