---
title: 'Azure Service Fabric övervakning och diagnostik: översikt | Microsoft Docs'
description: Läs mer om övervakning och diagnostik för Azure Service Fabric-kluster, program och tjänster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: dd2446fda204f4026ac8080c658ca1aa9419f1bd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Övervaknings- och diagnostikfunktionerna för Azure Service Fabric

Den här artikeln innehåller en översikt över övervakning och diagnostik för Azure Service Fabric. Övervaknings- och diagnostikfunktionerna är kritiska för utveckling, testning och distribution av arbetsbelastningar i en molnmiljö. Övervakning kan du spåra hur dina program används din resursutnyttjande och den övergripande hälsan för klustret. Du kan använda informationen för att diagnostisera och åtgärda eventuella problem och förhindra att problem inträffar i framtiden. 

## <a name="application-monitoring"></a>Programövervakning
Programövervakning spårar hur funktioner och komponenter i programmet används. Du vill övervaka dina program och se till att problem som påverkar användare fångas. Övervaka dina program kan vara användbart i följande scenarier:
* fastställa programinläsning och användaren trafik - behöver du skala dina tjänster för att uppfylla kraven för användaren eller adressen potentiella flaskhalsar i ditt program?
* identifiera problem med tjänstkommunikation och fjärrkommunikation över ditt kluster
* Räkna ut vad användarna gör med ditt program – samla in telemetri i dina program hjälper guiden funktionen för framtida utveckling och bättre diagnostik för app-fel
* Övervakning av vad som händer i din körs behållare

Service Fabric stöder många alternativ kan instrumentera programkoden med rätt spårningar och telemetri. Vi rekommenderar att du använder Application Insights (AI). AIS integrering med Service Fabric innehåller tooling upplevelser för Visual Studio och Azure portal samt Service Fabric specifika mått, vilket ger en miljö med omfattande out box-loggning. Även om många loggar skapas automatiskt och samlas in för dig med AI, rekommenderar vi att du lägger till ytterligare anpassad loggning för dina program för att skapa en rikare upplevelse för diagnostik. Läs mer om att komma igång med Application Insights med Service Fabric på [händelseanalys med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Övervakning av plattform (kluster)
Övervaka Service Fabric-kluster är viktigt att säkerställa att plattformen och alla arbetsbelastningar körs som avsett. Ett av målen för Service Fabric är att hålla program motståndskraftiga mot maskinvarufel. Det här målet uppnås via plattformens system services möjlighet att identifiera problem för infrastruktur och snabbt redundans arbetsbelastningar till andra noder i klustret. Men i det här fallet vad händer om systemtjänster själva har problem? Eller om du vid försöket att flytta en arbetsbelastning, reglerna för placering av tjänster har brutits? Övervaka klustret kan du hålla dig informerad om aktiviteter som äger rum i din kluster, vilket hjälper till att diagnostisera problem och åtgärda dem effektivt. Det finns några viktiga saker som du vill efter:
* Service Fabric fungerar som förväntat när det gäller att placera dina program och nätverksbelastning runt klustret? 
* Vidtas användaråtgärder på ditt kluster bekräftas och köras på som förväntat? Detta är särskilt relevanta när skalning ett kluster.
* Service Fabric hanterar dina data och din tjänst-tjänst kommunikation inom klustret korrekt?

Service Fabric ger en omfattande uppsättning händelser direkt. Dessa [Service Fabric händelser](service-fabric-diagnostics-events.md) kan nås via EventStore APIs eller operativa kanalen (händelse kanal som exponeras av plattform). 
* EventStore - EventStore (tillgänglig i Windows i version 6.2 och senare, Linux fortfarande pågår från och med den här artikeln datum för senaste uppdatering), visar dessa händelser via en uppsättning API: er (tillgänglig via REST-slutpunkter eller via klientbiblioteket). Läs mer om EventStore på den [EventStore översikt](service-fabric-diagnostics-eventstore.md).
* Service Fabric kanaler – Windows-Service Fabric-Händelsemodul är tillgängliga från en enskild ETW-provider med en uppsättning relevanta `logLevelKeywordFilters` används för att välja mellan drift och Data & Messaging kanaler – detta är det sätt som vi skilja ut utgående Service Fabric-händelser som ska filtreras på efter behov. På Linux, Service Fabric händelser kommer via LTTng och placeras i en tabell för lagring, från där de kan filtreras efter behov. Dessa kanaler innehålla granskat, strukturerade händelser som kan användas för att bättre förstå tillståndet för klustret. Diagnostik är aktiverade som standard när klustret skapas, som skapar en tabell för Azure Storage där händelser från dessa kanaler skickas att fråga i framtiden. 

Vi rekommenderar att du använder EventStore för snabb analys och för att få en ögonblicksbild uppfattning av hur klustret fungerar och om saker händer som förväntat. För att samla in loggar och händelser som genereras av klustret, normalt bör du använda den [Azure Diagnostics tillägget](service-fabric-diagnostics-event-aggregation-wad.md). Detta kan integreras med Service Fabric Analytics, OMS Log Analytics Service Fabric specifika lösning som innehåller en anpassad instrumentpanel för övervakning av Service Fabric-kluster och du kan fråga din klusterhändelser och konfigurera aviseringar. Läs mer om detta i [händelseanalys med OMS](service-fabric-diagnostics-event-analysis-oms.md). 

 Du kan läsa mer om hur du övervakar klustret på [plattform nivån händelse och logga generation](service-fabric-diagnostics-event-generation-infra.md).


 ![OMS SA lösning](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

## <a name="performance-monitoring"></a>Prestandaövervakning
Övervaka infrastrukturen underliggande är en viktig del av förstå statusen för klustret och utnyttjande av nätverksresurser. Mäter systemets prestanda beror på flera faktorer, som mäts vanligtvis via en OEM-tillverkare (Key Performance Indicator). Service Fabric relevanta KPI: er kan mappas till mått som kan samlas in från noder i klustret som prestandaräknare.
Dessa KPI: er kan hjälpa dig med:
* Förstå resursutnyttjande och belastningen - för att skala ditt kluster eller optimera serviceprocesser.
* Förutsäga infrastruktur problem – många problem föregås av plötsliga förändringar (således) prestanda, så du kan använda KPI: er, till exempel nätverk i/o- och CPU-användning för att förutsäga och diagnostisera problem med infrastrukturella.

En lista över prestandaräknare som ska samlas in på infrastrukturnivå finns på [prestandamått](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric tillhandahåller en uppsättning prestandaräknare för programmeringsmodeller Reliable Services och aktörer. Om du använder något av dessa modeller, kan dessa prestandaräknare ge KPI: er för att säkerställa att din aktörer spinning uppåt och nedåt korrekt eller att tillförlitlig tjänst-begäranden hanteras tillräckligt snabbt. Mer information finns i [övervakning för tillförlitlig tjänst fjärrkommunikation](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) och [prestandaövervakning för Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Utöver det har Application Insights också en uppsättning prestandamått den samlar in, om konfigurerad med ditt program.

Använd den [OMS-agent](service-fabric-diagnostics-oms-agent.md) att samla in rätt prestandaräknarna och visa dessa KPI: er i OMS logganalys.

![Diagram för diagnostik](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Hälsoövervakning
Service Fabric-plattformen innehåller en hälsomodell som ger extensible hälsotillståndsrapportering för status för entiteter i ett kluster. Varje nod, program, tjänst, partition, replik eller instansen hälsostatusen kontinuerligt uppdateras. Hälsostatus kan antingen vara ”OK”, ”varning” eller ”Error”. Hälsotillståndet ändras via hälsorapporter som orsakat för varje entitet baserat på problem i klustret. Hälsostatus för entiteter kan kontrolleras när som helst i Service Fabric Explorer (SFX) som visas nedan, eller kan efterfrågas via plattformarna hälsa API. Du kan också anpassa hälsorapporter och ändra hälsostatus för en entitet genom att lägga till egna hälsorapporter eller med hjälp av hälsotillstånd API. Mer information om hälsomodell finns på [introduktion till Service Fabric-hälsoövervakning](service-fabric-health-introduction.md).

![Instrumentpanelen för hälsotillstånd för SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Förutom att visa senaste hälsorapporter i SFX finns också varje rapport som en händelse. Hälsotillstånd händelser kan samlas in via operativa kanalen (se [händelse aggregeringen med Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)), och lagras i Log Analytics för varningar och frågor i framtiden. Detta hjälper dig att identifiera problem som kan påverka din tillgänglighet, så vi rekommenderar att du konfigurerar aviseringar för lämpliga scenarier (anpassade aviseringar via logganalys).

## <a name="other-logging-solutions"></a>Andra lösningar för loggning

Även om de båda lösningarna vi rekommenderas [OMS](service-fabric-diagnostics-event-analysis-oms.md) och [Programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md) har skapats i integrering med Service Fabric många händelser skrivs via etw-leverantörer och är extensible med andra loggning lösningar. Du bör också titta i den [elastisk Stack](https://www.elastic.co/products) (särskilt om du använder ett kluster i en frånkopplad miljö), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/), eller någon annan plattform för dina inställningar. 

Nyckelord för en plattform som du bör inkludera hur nöjd du är med användargränssnittet och frågar alternativ, möjligheten att visualisera data och skapa lättläst instrumentpaneler och ytterligare verktyg de tillhandahåller för att förbättra din övervakning automatiserade som aviseringar.

## <a name="next-steps"></a>Nästa steg

* Komma igång med instrumentering dina program, finns i [programmet nivån händelse och logga generation](service-fabric-diagnostics-event-generation-app.md).
* Lär dig mer om hur du övervakar plattformen och händelserna Service Fabric ger du vid [plattform nivån händelse och logga generation](service-fabric-diagnostics-event-generation-infra.md).
* Gå igenom stegen för att ställa in AI för ditt program med [övervaka och diagnostisera programmet ASP.NET Core på Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Lär dig hur du ställer in OMS Log Analytics för övervakning av behållare- [övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Lär dig mer om allmänna övervakning rekommendationer för Azure-resurser - [metodtips - övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
