---
title: Förstå och justera enheter för strömning i Azure Stream Analytics
description: Den här artikeln beskriver inställningen enheter för strömning och andra faktorer som påverkar prestanda i Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: c96e9825cddd0b60e67cd4752fab8f440ceaae76
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="understand-and-adjust-streaming-units"></a>Förstå och justera enheter för strömning

Azure Stream Analytics aggregerar prestanda ”vikt” för ett jobb som körs i enheter för strömning (SUs). SUs representerar de resurser som används för att köra ett jobb. SU:er är ett sätt att beskriva den relativa kapaciteten för händelsebearbetning utifrån en kombination av mått för processor, minne, och läs- och skrivhastigheter. Den här kapaciteten kan du fokusera på logiken som frågan och sammanfattningar behovet av att hantera maskinvara för att köra Stream Analytics-jobbet inom rimlig tid.

För att uppnå låg latens strömning bearbetning Azure Stream Analytics-jobb som utför all bearbetning i minnet. Direktuppspelningsjobbet misslyckas när du kör slut på minne. Därför för ett produktionsjobb är det viktigt att övervaka ett direktuppspelningsjobb Resursanvändning och kontrollera att det finns tillräckligt med resurser som allokerats för att skydda de jobb som är igång 24/7.

Måttet är en procent tal mellan 0% till 100%. SU % användning mått är vanligtvis mellan 10 och 20% för en strömmande jobbet med minimala storleken. Det är bäst att hålla mått under 80% för tillfällig toppar. Microsoft rekommenderar att du ställer in en avisering på 80% SU-användningen mått för att förhindra resursuttömning. Mer information finns i [Självstudier: konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurera Stream Analytics Strömningsenheter (SUs)
1. Logga in på [Azure-portalen](http://portal.azure.com/)

2. Hitta Stream Analytics-jobbet som du vill skala och sedan öppna den i listan över resurser. 

3. På sidan jobb under den **konfigurera** rubrik, Välj **skala**. 

    ![Azure Stream Analytics-jobbet Portalkonfiguration][img.stream.analytics.preview.portal.settings.scale]
    
4. Använd skjutreglaget för att ange SUs för jobbet. Observera att du är begränsad till specifika SU-inställningar. 

## <a name="monitor-job-performance"></a>Övervaka jobb prestanda
Du kan spåra genomflödet av ett jobb med hjälp av Azure portal:

![Azure Stream Analytics övervaka jobb][img.stream.analytics.monitor.job]

Beräkna förväntade genomflödet av arbetsbelastningen. Om kapaciteten är mindre än förväntat, finjustera inkommande partition, justera frågan och Lägg till SUs ditt jobb.

## <a name="how-many-sus-are-required-for-a-job"></a>Hur många SUs krävs för ett jobb?

Att välja antalet nödvändiga SUs för ett visst jobb beror på hur partition för indata och den fråga som definieras i jobbet. Den **skala** sidan kan du ange antalet SUs. Det är en bra idé att allokera mer SUs än vad som behövs. Stream Analytics-bearbetningsmotorn optimeras för svarstid och genomströmning på bekostnad av ytterligare minnesallokering.

I allmänhet är det bästa sättet är att starta med 6 SUs för frågor som inte använder **PARTITION BY**. Sedan fastställa av platsen genom att använda en prövningsmetod med vilket ändra antalet SUs när du skickar representativt datamängder och undersöka SU % användning mått.

Mer information om hur du väljer rätt antal SUs finns i den här sidan: [skala Azure Stream Analytics-jobb att öka genomflödet](stream-analytics-scale-jobs.md)

> [!Note]
> Om du väljer hur många SUs krävs för ett specifikt jobb beror på konfigurationen av partitionen för indata och frågan som definierats för jobbet. Du kan välja upp till din kvot i SUs för ett jobb. Som standard har varje Azure-prenumeration en kvot på upp till 200 SUs för analytics-jobb i en viss region. För att öka SUs för dina prenumerationer utöver den här kvoten kan kontakta [Microsoft-supporten](http://support.microsoft.com). Giltiga värden för SUs per jobb är 1, 3, 6, och upp i steg 6.

## <a name="factors-that-increase-su-utilization"></a>Faktorer som ökar SU % användning 

Tidsbestämd (tid indatavärdena) frågan element är den grundläggande uppsättningen tillståndskänslig operatorer som tillhandahålls av Stream Analytics. Stream Analytics hanterar tillståndet för dessa åtgärder internt för användarens räkning genom att hantera minnesförbrukning, kontrollpunkter för återhämtning och återställning av systemtillstånd under uppgraderingar av tjänsten. Även om Stream Analytics hanterar fullständigt tillstånden, finns det ett antal rekommendationer om bästa praxis som användare bör.

## <a name="stateful-query-logic-in-temporal-elements"></a>Tillståndskänsliga frågan logiken i den temporala element
En unik möjligheterna för Azure Stream Analytics-jobbet är att genomföra tillståndskänslig bearbetning, till exempel fönsteraggregeringar, temporala kopplingar och temporal analysfunktioner. Var och en av de här operatorerna behåller statusinformation. Den största fönsterstorleken för de här elementen i frågan är sju dagar. 

Begreppet temporalt fönster visas i flera Stream Analytics query element:
1. Fönsteraggregeringar: grupp av av rullande, Hopping och glidande windows

2. Tidsbestämd kopplingar: ansluta med DATEDIFF-funktionen

3. Tidsbestämd analysfunktioner: ISFIRST-, LAST och FÖRDRÖJNING med GRÄNSEN varaktighet

Följande faktorer som påverkar det minne som används (del av strömmande enheter mått) av Stream Analytics-jobb:

## <a name="windowed-aggregates"></a>Fönsteraggregeringar
Det minne som används (tillstånd storleken) för en fönster mängd är inte alltid direkt proportion till storleken på. I stället är minne som används proportionell till Kardinaliteten för data eller antalet grupper i varje tidsperioden.


Till exempel i följande fråga numret som är associerade med `clusterid` är kardinalitet för frågan. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

För att kunna antagande problem som orsakas av hög kardinalitet i den föregående frågan om du skickar händelser till Händelsehubben partitionerats med `clusterid`, och skala ut frågan genom att låta systemet för att bearbeta varje inkommande partition separat med **PARTITION AV** som visas i exemplet nedan:

   ```sql
   SELECT count(*) 
   FROM PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Därför kan antalet `clusterid` värden som kommer till varje nod minskas vilket minskar kardinalitet i gruppen med operatorn. 

Vara bör partitionerade Event Hub partitioner med gruppering för att undvika att behöva ett minska steg. Mer information finns i [översikt av Händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Tidsbestämd kopplingar
Det minne som används en temporal koppling (tillstånd storlek) är proportionell mot antalet händelser i temporal handlingsfrihet av koppling som är inkommande händelsehastighet multiplicera genom att skaka några gånger... Rumsstorlek. Med andra ord är minne som används av kopplingar proportionell mot DateDiff tidsintervallet multiplicerat med genomsnittlig händelsefrekvens.

Antalet omatchade händelser i kopplingen påverkar minnesanvändning för frågan. Följande fråga söker efter reklamannonser som genererar klick:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

I det här exemplet är det möjligt att många annonser visas och några få personer klickar du på den och det krävs att alla händelser under tidsperioden. Förbrukat minne beror på tidsperiodens längd och händelsens frekvens. 

Om du vill reparera det här skicka händelser till Händelsehubben partitioneras vid koppling nycklar (id i detta fall) och skala ut frågan genom att låta systemet att bearbeta varje inkommande partition separat med **PARTITION BY** som visas:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Därför minskas antalet händelser som kommer till varje nod, vilket minskar storleken på tillståndet sparas i fönstret ansluta till. 

## <a name="temporal-analytic-functions"></a>Tidsbestämd analysfunktioner
Det minne som används (tillstånd storleken) för en temporal analytiska funktion är proportionell mot händelsetakten multiplicera av duration. Det minne som används av analysfunktioner är inte proportion till storleken på men istället partitions antal under varje tidsperioden.

Reparationen liknar temporal koppling. Du kan skala ut en fråga med hjälp av **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>I oordning buffert 
Användare kan konfigurera i oordning buffertstorleken i händelseloggen ordning configuration-fönstret. Bufferten används för att hålla indata för varaktighet för perioden och ändra ordning på dem. Storleken på bufferten är proportionell mot inkommande händelsehastighet multiplicera av storleken på i fel ordning. Fönstret standardstorleken är 0. 

Om du vill reparera spill i oordning buffertens skala ut frågan med **PARTITION BY**. När frågan är utpartitionerad sprids den ut över flera noder. Därför minskas antalet händelser som kommer till varje nod, vilket minskar antalet händelser i varje beställning buffert. 

## <a name="input-partition-count"></a>Antal inkommande partitioner 
Varje inkommande partition för ett jobb som indata har en buffert. Jobbet förbrukar större antal inkommande partitioner, desto mer resurser. För varje enhet, kan Azure Stream Analytics bearbeta ungefär 1 MB/s av indata. Därför kan du optimera genom att matcha antalet Stream Analytics strömningsenheter med antalet partitioner i din Event Hub. 

Ett jobb som har konfigurerats med en enhet är vanligtvis tillräckliga för en Händelsehubb med två partitioner (som är minst för Event Hub). Om Händelsehubben har fler partitioner, Stream Analytics-jobbet använder mer resurser, men inte nödvändigtvis använder det extra genomflödet som tillhandahålls av Event Hub. 

Du kanske måste 4 eller 8 partitioner från Event Hub för ett jobb med 6 enheter för strömning. Dock undvika för många onödiga partitioner eftersom som orsakar hög Resursanvändning. Till exempel en Händelsehubb med 16 partitioner eller större i ett Stream Analytics-jobb som har 1 enhet. 

## <a name="reference-data"></a>Referensdata 
Referensdata i ASA läses in i minnet för snabb sökning. Med den aktuella implementeringen behålls varje kopplingsåtgärd med referensdata ett exemplar av referensdata i minnet, även om du ansluter med samma referensdata flera gånger. För frågor med **PARTITION BY**, varje partition har en kopia av referensdata, så att partitionerna är fullständigt frikopplad. Med effekten multiplikator snabbt minnesanvändning mycket hög om du ansluter med referensdata flera gånger med flera partitioner.  

### <a name="use-of-udf-functions"></a>Användning av UDF-funktioner
När du lägger till en UDF-funktion laddar Azure Stream Analytics JavaScript-körning i minnet. Detta påverkar SU %.

## <a name="next-steps"></a>Nästa steg
* [Skapa parallell frågor i Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skala Azure Stream Analytics-jobb för att öka genomströmning](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
