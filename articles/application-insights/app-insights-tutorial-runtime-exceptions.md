---
title: "Diagnostisera körningsundantag med hjälp av Azure Application Insights | Microsoft Docs"
description: "Självstudie om att hitta och diagnostisera körningsundantag i dina program med hjälp av Azure Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 115611c5d4eeffb0f0600dd0a792ee9f80247e36
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Hitta och diagnostisera körningsundantag med Azure Application Insights

Azure Application Insights samlar in telemetri från ditt program för att identifiera och diagnostisera körningsundantag.  I den här kursen går vi igenom den här processen med ditt program.  Lär dig att:

> [!div class="checklist"]
> * ändra ditt projekt för att aktivera undantagsspårning
> * identifiera undantag för olika komponenter i programmet
> * visa information om ett undantag
> * ladda ned en ögonblicksbild av undantaget till Visual Studio för felsökning
> * analysera information om misslyckade förfrågningar med frågespråk
> * skapa ett nytt arbetsobjekt för att åtgärda den felaktiga koden.


## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Ladda ned och installera [Visual Studio Snapshot Debugger](http://aka.ms/snapshotdebugger).
- Aktivera [Visual Studio Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](app-insights-asp-net.md). 
- I självstudien spåras hur du identifierar ett undantag i ditt program, så ändra koden i din miljö för utveckling eller testning så att du genererar ett undantag. 

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analysera fel
Application Insights samlar in eventuella fel i programmet, och du kan se frekvensen över olika åtgärder så att du kan fokusera på de som påverkar upplevelsen mest.  Du kan sedan detaljgranska informationen om dessa fel och identifiera grundorsaken.   

1. Välj **Application Insights** och sedan din prenumeration.  
2. Du kan öppna panelen **Fel** genom att antingen välja **Fel** på menyn **Undersök** eller klicka på diagrammet **Misslyckade förfrågningar**.

    ![Misslyckade förfrågningar](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

3. I panelen **Misslyckade förfrågningar** visas antalet misslyckade förfrågningar och hur många användare som påverkats för varje åtgärd i programmet.  Om du sorterar informationen efter användare kan du se vilka fel som påverkar användarna mest.  I det här exemplet är **GET Employees/Create** och **GET Customers/Details** lämpliga kandidater att undersöka på grund av det stora antalet fel och användare som påverkats.  Om du väljer en åtgärd visas ytterligare information om åtgärden i den högra panelen.

    ![Panelen Misslyckade förfrågningar](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

4. Minska tidsfönstret och zooma in på den period där felfrekvensen har en topp.

    ![Fönstret Misslyckade förfrågningar](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

5. Klicka på **Visa information** för att se detaljer om åtgärden.  Då visas bland annat ett Gantt-diagram med två misslyckade beroenden som sammanlagt tog nästan en halv sekund att slutföra.  Mer information om hur du analyserar prestandaproblem finns i självstudien [Hitta och diagnostisera prestandaproblem med Azure Application Insights](app-insights-tutorial-performance.md).

    ![Detaljer om misslyckade förfrågningar](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

6. I driftinformationen visas också ett FormatException-undantag som verkar ha orsakat felet.  Klicka på undantaget eller på **Top 3 exception types** (De 3 vanligaste undantagstyperna) om du vill visa detaljerad information.  Du kan se att orsaken är ett ogiltigt postnummer.

    ![Undantagsinformation](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)

> [!NOTE]
Aktivera [förhandsgranskningsupplevelsen](app-insights-previews.md) Unified details: E2E Transaction Diagnostics (Enhetlig information: E2E-transaktionsdiagnostik) om du vill se telemetriliknande förfrågningar, beroenden, undantag, spårningar, händelser och så vidare på serversidan i en enda helskärmsvy. 

När den här förhandsgranskningen är aktiverad kan du se hur lång tid som tillbringats i beroendeanrop tillsammans med eventuella fel eller undantag i en enda enhetlig vy. När det gäller transaktioner mellan olika komponenter kan du snabbt se vilken komponent, vilket beroende eller undantag som är rotorsak i Gannt-diagrammet och informationsfönstret. Du kan expandera den undre delen om du vill se tidssekvensen för spårningar eller händelser som samlats in för den valda komponentåtgärden. [Läs mer om den nya upplevelsen](app-insights-transaction-diagnostics.md)  

![Transaktionsdiagnostik](media/app-insights-tutorial-runtime-exceptions/e2e-transaction-preview.png)

## <a name="identify-failing-code"></a>Identifiera felaktig kod
Snapshot Debugger samlar in ögonblicksbilder av de vanligaste undantagen i ditt program, som är till hjälp när du ska diagnostisera grundorsaken i produktion.  Du kan visa de här ögonblicksbilderna i portalen, se anropsstacken och inspektera variablerna på varje nivå av stacken. Du kan sedan felsöka källkoden genom att ladda ned ögonblicksbilden och öppna den i Visual Studio 2017.

1. Klicka på **Open debug snapshot** (Öppna ögonblicksbild för felsökning) i egenskaperna för undantaget.
2. Panelen **Debug Snapshot** (Ögonblicksbild för felsökning) öppnas med anropsstacken för förfrågningen.  Om du klickar på en metod visas värdena för alla lokala variabler vid tidpunkten för förfrågningen.  Om du börjar med den översta metoden i det här exemplet ser vi att det finns lokala variabler som inte har något värde.

    ![Ögonblicksbild för felsökning](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. Det första anropet som har giltiga värden är **ValidZipCode**, och vi kan se att ett postnummer angavs tillsammans med bokstäver som inte kan översättas till ett heltal.  Det här verkar vara felet i koden som måste åtgärdas.

    ![Ögonblicksbild för felsökning](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Om du vill ladda ned den här ögonblicksbilden till Visual Studio och leta rätt på den faktiska kod som måste åtgärdas klickar du på **Download Snapshot** (Ladda ned ögonblicksbild).
6. Ögonblicksbilden läses in i Visual Studio.
7. Nu kan du köra en felsökningssession i Visual Studio som snabbt identifierar vilken kodrad som orsakar undantaget.

    ![Undantag i kod](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Använda analysdata
Alla data som samlas in av Application Insights lagras i Azure Log Analytics, så att du har tillgång till ett funktionsrikt frågespråk som hjälper dig att analysera data på en mängd olika sätt.  Vi kan använda informationen till att analysera de förfrågningar som genererat undantaget vi undersöker. 

8. Klicka på CodeLens-informationen över koden om du vill visa telemetrin som tillhandahålls av Application Insights.

    ![Kod](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Klicka på **Analyze impact** (Analysera påverkan) för att öppna Application Insights Analytics.  Det fylls i med flera frågor som kan ge detaljerad information om misslyckade förfrågningar, till exempel vilka användare, webbläsare och regioner som påverkas.<br><br>![Analys](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Lägg till arbetsobjekt
Om du ansluter Application Insights till ett spårningssystem som Visual Studio Team Services eller GitHub kan du skapa ett arbetsobjekt direkt från Application Insights.

1. Återgå till panelen **Exception Properties** (Egenskaper för undantag) i Application Insights.
2. Klicka på **Nytt arbetsobjekt**.
3. Panelen **Nytt arbetsobjekt** öppnas med detaljer om undantaget ifyllda.  Du kan lägga till ytterligare information innan du sparar objektet.

    ![Nytt arbetsobjekt](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du identifierar körningsundantag går du vidare till nästa självstudie, där du får lära dig hur du identifierar och diagnostiserar prestandaproblem.

> [!div class="nextstepaction"]
> [Identifiera prestandaproblem](app-insights-tutorial-performance.md)