---
title: Realtidsdata visualisering av sensordata från Azure IoT-hubb – Power BI | Microsoft Docs
description: Använd Power BI om du vill visualisera temperatur- och fuktighetskonsekvens data som samlas in från sensorn och skickas till din Azure IoT-hubb.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: realtid datavisualisering, realtidsdata visualiseringen sensor datavisualisering
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 12ac0596d70ae068ba17713d1251fbf117824f67
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualisera sensordata i realtid från Azure IoT-hubb med Power BI

![Diagram för slutpunkt till slutpunkt](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig att visualisera sensordata i realtid som tar emot din Azure IoT-hubb av Power BI. Om du vill visualisera data i din IoT-hubb med Web Apps du se [Använd Azure Web Apps visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Vad du gör

- Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.
- Skapa, konfigurera och köra ett Stream Analytics-jobb för att överföra data från IoT-hubb till Power BI-konto.
- Skapa och publicera en Power BI-rapport för att visualisera data.

## <a name="what-you-need"></a>Vad du behöver

- Kursen [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb i din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.
- Power BI-konto. ([Testa Powerbi gratis](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I den [Azure-portalen](https://portal.azure.com), klickar du på **skapar du en resurs** > **Sakernas Internet** > **Stream Analytics-jobbet**.
1. Ange följande information för jobbet.

   **Jobbnamnet**: namnet på jobbet. Namnet måste vara globalt unikt.

   **Resursgruppen**: använda samma resursgrupp som använder din IoT-hubb.

   **Plats**: använda samma plats som resursgruppen.

   **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Skapa ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics-jobbet.
1. Under **jobbet topologi**, klickar du på **indata**.
1. I den **indata** rutan klickar du på **Lägg till**, och ange följande information:

   **Ett inmatat alias**: unika alias för indata.

   **Källan**: Välj **IoT-hubb**.

   **Konsumentgrupp**: Välj konsumentgrupp som du nyss skapade.
1. Klicka på **Skapa**.

   ![Lägga till indata till Stream Analytics-jobbet i Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **jobbet topologi**, klickar du på **utdata**.
1. I den **utdata** rutan klickar du på **Lägg till**, och ange följande information:

   **Kolumnalias**: unika alias för utdata.

   **Sink**: Välj **Power BI**.
1. Klicka på **auktorisera**, och sedan logga in på ditt Power BI-konto.
1. När behöriga, anger du följande information:

   **Gruppera arbetsytan**: Välj din grupp målarbetsytan.

   **DataSet-namnet**: Ange ett namn för dataset.

   **Tabellnamn**: Ange ett tabellnamn.
1. Klicka på **Skapa**.

   ![Lägga till utdata till Stream Analytics-jobbet i Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan i Stream Analytics-jobbet

1. Under **jobbet topologi**, klickar du på **frågan**.
1. Ersätt `[YourInputAlias]` med indata alias för jobbet.
1. Ersätt `[YourOutputAlias]` med utdataalias för jobbet.
1. Klicka på **Spara**.

   ![Lägg till en fråga till ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Kör Stream Analytics-jobbet

Klicka på i Stream Analytics-jobbet **starta** > **nu** > **starta**. När jobbet har startar jobbets status har ändrats från **stoppad** till **kör**.

![Kör ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Skapa och publicera en Power BI-rapport för att visualisera data

1. Kontrollera exempelprogrammet som körs på enheten. Om inte, kan du referera till självstudier under [konfigurera enheten](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Logga in på ditt [Power BI](https://powerbi.microsoft.com/en-us/) konto.
1. Gå till arbetsytan grupp som du angav när du skapade utdata för Stream Analytics-jobbet.
1. Klicka på **strömning datauppsättningar**.

   Du bör se listan datamängden som du angav när du skapade utdata för Stream Analytics-jobbet.
1. Under **åtgärder**, klicka på den första ikonen om du vill skapa en rapport.

   ![Skapa en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Skapa ett linjediagram för att visa realtid temperatur över tid.
   1. På sidan Skapa, lägger du till ett linjediagram.
   1. På den **fält** rutan Expandera tabellen som du angav när du skapade utdata för Stream Analytics-jobbet.
   1. Dra **EventEnqueuedUtcTime** till **axel** på den **visualiseringar** fönstret.
   1. Dra **temperatur** till **värden**.

      Nu skapas ett linjediagram. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

      ![Lägg till ett linjediagram för temperatur i en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Skapa en annan linjediagram om du vill visa realtid fuktighet över tid. Om du vill göra detta, följer du stegen ovan och placera **EventEnqueuedUtcTime** på x-axeln och **fuktighet** på y-axeln.

   ![Lägg till ett linjediagram för fuktighet i en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Klicka på **spara** att spara rapporten.
1. Klicka på **filen** > **publicera till webben**.
1. Klicka på **skapa inbäddningskod**, och klicka sedan på **publicera**.

Du har angett rapportlänken att du kan dela med andra för åtkomst till rapporten och ett kodstycke att integrera rapporten i din blogg eller webbplats.

![Publicera en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft erbjuder även den [Power BI-appar](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) för att visa och interagera med dina Power BI-instrumentpaneler och rapporter på din mobila enhet.

## <a name="next-steps"></a>Nästa steg

Du har använt Power BI för att visualisera sensordata i realtid från Azure IoT-hubben.
Det finns ett annat sätt att visualisera data från Azure IoT Hub. Se [Använd Azure Web Apps visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
