---
title: Typer av utdata från Azure Stream Analytics-jobb
description: Läs mer om Stream Analytics utdata Dataalternativ inklusive Power BI för analysresultat som mål.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 30fa7e081c24339b7fa9f572d9feb25a0f920a86
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="stream-analytics-outputs-options-for-storage-and-analysis"></a>Strömma Analytics utdata: alternativ för lagring och analys
När du redigerar ett Stream Analytics-jobb kan du överväga hur resulterande data används. Hur kan du visa resultatet av Stream Analytics-jobbet och där kan du lagra den?

Azure Stream Analytics har olika alternativ för att lagra utdata och visa analysresultat för att aktivera en mängd olika program mönster. Detta gör det enkelt att visa jobbutdata och ger dig flexibilitet vid användning och lagring av jobbutdata för datalagring och andra ändamål. Inga utdata som konfigurerats i jobbet måste finnas innan jobbet har startats och händelser börjar flöda. Till exempel, om du använder Blob storage som utdata skapa jobbet inte ett lagringskonto automatiskt. Skapa ett lagringskonto innan Stream Analytics-jobbet har startats.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Strömma Analytics stöder [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Data Lake Store kan du lagra data med en storlek, typ och införandet hastighet för drifts- och undersökande analyser. Stream Analytics måste dessutom ha behörighet att komma åt Data Lake Store.

### <a name="authorize-an-azure-data-lake-store-account"></a>Godkänna ett Azure Data Lake Store-konto

1. När lagring av Data Lake väljs som utdata i Azure-portalen, uppmanas du att auktorisera en anslutning till en befintlig Data Lake Store.  

   ![Auktorisera Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Om du redan har åtkomst till Data Lake Store, klicka på ”Verifiera nu” och en sida visas som anger ”omdirigera tillstånd”. När tillståndet lyckas, visas sidan där du kan konfigurera Data Lake Store-utdata.  

3. När du har Data Lake Store-kontot autentiseras kan du konfigurera egenskaper för Data Lake Store-utdata. Tabellen nedan är listan över egenskapsnamn och deras beskrivning för att konfigurera din Data Lake Store-utdata.

   ![Auktorisera Data Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

<table>
<tbody>
<tr>
<td><B>EGENSKAPSNAMN</B></td>
<td><B>BESKRIVNING</B></td>
</tr>
<tr>
<td>Kolumnalias</td>
<td>Ett eget namn som används i frågor för att dirigera utdata till denna Data Lake Store.</td>
</tr>
<tr>
<td>Kontonamn</td>
<td>Namnet på lagring av Data Lake-konto där du skickar din utdata. Visas med listan över Data Lake Store-konton som är tillgängliga i din prenumeration.</td>
</tr>
<tr>
<td>Sökvägar för Prefix</td>
<td>Den filsökväg som används för att skriva filer i den angivna Data Lake Store-konto. Du kan ange en eller flera instanser av {date} och {time} variabler.<BR> Exempel 1: mapp1/logs / {date} / {time}<BR>Exempel 2: mapp1/logs / {date}<BR>Här finns även i situationer där en ny fil skapas:<BR>1. Ändra utdataschemat <BR>2. Externa eller interna omstart av ett jobb<BR><BR>Dessutom, om sökvägar filen innehåller inte en avslutande ”/”, det senaste mönstret i sökvägen behandlas som ett filnamnsprefix.<BR></td>
</tr>
<tr>
<td>Datumformat [<I>valfria</I>]</td>
<td>Du kan välja datumformat där filerna ordnas om datumtoken används i sökvägen till prefix. Exempel: ÅÅÅÅ/MM/DD</td>
</tr>
<tr>
<td>Tidsformat [<I>valfria</I>]</td>
<td>Ange tidsformat där filerna ordnas om tid token används i sökvägen till prefix. Det enda värdet som stöds är för närvarande HH.</td>
</tr>
<tr>
<td>Händelsen serialiseringsformat</td>
<td>Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Om du använder CSV- eller JSON-format, måste kodning anges. UTF-8 är det enda kodformat som stöds för närvarande.</td>
</tr>
<tr>
<td>Avgränsare</td>
<td>Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering CSV. Värden som stöds är kommatecken, semikolon, blanksteg, fliken och vertikalstreck.</td>
</tr>
<tr>
<td>Format</td>
<td>Gäller endast för JSON-serialisering. Radseparering innebär att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. Denna matris stängs endast när jobbet stoppar eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad avgränsade JSON, eftersom den inte kräver någon särskild hantering när utdatafilen fortfarande skrivs till.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Förnya auktorisering för Data Lake Store
Du måste autentiseras ditt Data Lake Store-konto om lösenordet har ändrats sedan jobbet skapades eller senast autentiserad. Om du inte autentiseras jobbet kommer inte genereras av resultaten och ett felmeddelande om att behovet av återauktorisering loggas i loggarna för åtgärden. För närvarande finns en begränsning där autentiseringstoken måste uppdateras manuellt efter 90 dagar för alla jobb med Data Lake Store-utdata. 

Förnya auktorisering, **stoppa** jobbet > Gå till din Data Lake Store-utdata > klickar du på den **förnya auktorisering** länka och under en kort tid en sida visas som anger ”omdirigera tillstånd..”. Sidan stängs automatiskt och om detta lyckas visar ”tillstånd har förnyats”. Du måste sedan klicka på **spara** längst ned på sidan och kan fortsätta genom att starta om jobbet från den **stoppats senast** att undvika dataförlust.

![Auktorisera Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) kan användas som utdata för data som är relationell natur och för program som är beroende av innehållet finns i en relationsdatabas. Stream Analytics-jobb att skriva till en befintlig tabell i Azure SQL-databas.  Tabellens schema måste exakt matcha fälten och deras typer som utdata från ditt jobb. En [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) kan också anges som utdata via SQL-databas alternativet output samt. I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en SQL Database-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Kolumnalias |Ett eget namn som används i frågor för att dirigera utdata till den här databasen. |
| Databas |Namnet på databasen där du skickar din utdata |
| Servernamn |SQL Database-servernamn |
| Användarnamn |Användarnamnet som har behörighet att skriva till databasen |
| Lösenord |Lösenord för att ansluta till databasen |
| Tabell |Tabellnamnet där utdata skrivs. Tabellnamnet är skiftlägeskänsligt och schemat för den här tabellen ska matcha exakt med antalet fält och deras typer som skapas av jobbutdata. |

> [!NOTE]
> För närvarande stöds Azure SQL Database-erbjudande för ett jobbutdata i Stream Analytics. En Azure-dator som kör SQL Server med en databas som är ansluten stöds dock inte. Detta kan ändras i framtida versioner.
> 
> 

## <a name="blob-storage"></a>Blob Storage
BLOB storage erbjuder en kostnadseffektiv och skalbar lösning för att lagra stora mängder Ostrukturerade data i molnet.  En introduktion på Azure Blob storage och användning finns i dokumentationen till [hur du använder BLOB](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en blob-utdata.

<table>
<tbody>
<tr>
<td>EGENSKAPSNAMN</td>
<td>BESKRIVNING</td>
</tr>
<tr>
<td>Kolumnalias</td>
<td>Ett eget namn som används i frågor för att dirigera utdata till blob-lagring.</td>
</tr>
<tr>
<td>Lagringskonto</td>
<td>Namnet på det lagringskonto där du skickar din utdata.</td>
</tr>
<tr>
<td>Lagringskontonyckel</td>
<td>Den hemliga nyckeln som associeras med lagringskontot.</td>
</tr>
<tr>
<td>Lagringsbehållaren</td>
<td>Behållare innehåller en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du överför en blobb till Blob-tjänsten måste du ange en behållare för blobben.</td>
</tr>
<tr>
<td>Prefixet sökvägar [valfritt]</td>
<td>Filen sökvägar som används för att skriva dina blobbar i den angivna behållaren. <BR> Du kan välja att använda en eller flera instanser av följande 2 variabler för att ange hur ofta BLOB skrivs i sökväg-mönster: <BR> {date}, {time} <BR> Exempel 1: cluster1/logs / {date} / {time} <BR> Exempel 2: cluster1/logs / {date} <BR> <BR> Filnamngivning följer enligt följande konvention: <BR> {Path Prefix Pattern}/schemaHashcode_Guid_Number.extension <BR> <BR> Exempel utdatafilerna: <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> Här finns även i situationer där en ny fil skapas: <BR> 1. Aktuella filen överskrider det maximala tillåtna antalet block (för närvarande 50 000) <BR> 2. Ändra utdataschemat <BR> 3. Externa eller interna omstart av ett jobb  </td>
</tr>
<tr>
<td>[Valfritt] datumformat</td>
<td>Du kan välja datumformat där filerna ordnas om datumtoken används i sökvägen till prefix. Exempel: ÅÅÅÅ/MM/DD</td>
</tr>
<tr>
<td>[Valfritt] tidsformat</td>
<td>Ange tidsformat där filerna ordnas om tid token används i sökvägen till prefix. Det enda värdet som stöds är för närvarande HH.</td>
</tr>
<tr>
<td>Händelsen serialiseringsformat</td>
<td>Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Om du använder CSV- eller JSON-format, måste kodning anges. UTF-8 är det enda kodformat som stöds för närvarande.</td>
</tr>
<tr>
<td>Avgränsare</td>
<td>Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering CSV. Värden som stöds är kommatecken, semikolon, utrymme, fliken och vertikalstreck.</td>
</tr>
<tr>
<td>Format</td>
<td>Gäller endast för JSON-serialisering. Radseparering innebär att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. Denna matris stängs endast när jobbet stoppar eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad avgränsade JSON, eftersom den inte kräver någon särskild hantering när utdatafilen fortfarande skrivs till.</td>
</tr>
</tbody>
</table>

När du använder blob storage som utdata, skapas en ny fil i blob i följande fall:

* Om filen är större än det maximala antalet tillåtna block (Observera att det högsta tillåtna antalet block kan nås utan att den maximala tillåtna blobbstorleken. Till exempel utdata är hög, hittar du mer byte per block och filstorlek är större. Om frekvensen av utgående är låg varje block har mindre data och filstorleken är mindre.)  
* Om det finns en schemaändring i utdata och utdataformat kräver fast schema (CSV- och Avro).  
* Om ett jobb startas antingen externt eller internt omstart av ett jobb.  
* Om frågan fullständigt är partitionerad skapas ny fil för varje partition i utdata.  
* Om en fil eller en behållare för lagringskontot tas bort av användaren.  
* Om resultatet blir tid partitionerats med hjälp av prefixet sökvägar, används en ny blob när frågan flyttar till nästa timma.

## <a name="event-hub"></a>Händelsehubb
[Händelsehubbar](https://azure.microsoft.com/services/event-hubs/) är en mycket skalbar publicera och prenumerera händelseinmatare. Det kan samla in miljontals händelser per sekund. När resultatet av ett Stream Analytics-jobb blir indata för en annan direktuppspelningsjobbet är en använda en Händelsehubb som utdata.

Det finns några parametrar som behövs för att konfigurera Event Hub-dataströmmar som utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Kolumnalias |Ett eget namn som används i frågor för att dirigera utdata till den här Event Hub. |
| Service Bus-namnområde |En Service Bus-namnrymd är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny Händelsehubb skapade du även en Service Bus-namnrymd |
| Händelsehubb |Namnet på din Event Hub-utdata |
| Namnet på Händelsehubben princip |Den princip för delad åtkomst som kan skapas på fliken Event Hub konfigurera. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar |
| Event Hub principnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnrymd |
| Partitionen nyckelkolumn [valfritt] |Kolumnen innehåller Partitionsnyckeln för Event Hub-utdata. |
| Händelsen serialiseringsformat |Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds. |
| Encoding |För CSV- och JSON är UTF-8 endast stöds Kodningsformatet just nu |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och vertikalstreck. |
| Format |Gäller endast för JSON-serialisering. Radseparering innebär att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. Denna matris stängs endast när jobbet stoppar eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad avgränsade JSON, eftersom den inte kräver någon särskild hantering när utdatafilen fortfarande skrivs till. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) kan användas som utdata för ett Stream Analytics-jobb för att tillhandahålla för omfattande visualisering av analysresultat. Den här funktionen kan användas för kontrollpaneler, rapportgenerering och mått som drivs reporting.

### <a name="authorize-a-power-bi-account"></a>Auktorisera en Power BI-konto
1. När Power BI väljs som utdata i Azure-portalen, uppmanas du att auktorisera en befintlig Power BI-användare eller skapa ett nytt Power BI-konto.  
   
   ![Auktorisera Power BI-användare](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Skapa ett nytt konto om du inte ännu har en och sedan klicka på Verifiera nu.  En skärm som liknar följande visas.  
   
   ![Azure-konto Powerbi](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. I det här steget Ange arbets-eller skolkonto för att auktorisera Power BI-utdata. Om du inte är redan registrerat dig för Power BI, väljer du logga nu. Arbets- eller skolkonto kontot som används för Power BI kan skilja sig från kontot Azure-prenumeration som du är inloggad på för tillfället.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurera egenskaper för Power BI-utdata
När du har Power BI-konto autentiseras kan konfigurera du egenskaperna för Power BI-utdata. Tabellen nedan är listan över egenskapsnamn och deras beskrivning för att konfigurera din Power BI-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Kolumnalias |Ett eget namn som används i frågor för att dirigera utdata till den här PowerBI-utdata. |
| Grupp-arbetsytan |Om du vill aktivera delning av data med andra Power BI-användare kan du välja grupper i Power BI-konto eller Välj ”Min arbetsyta” om du inte vill att skriva till en grupp.  Uppdatering av en befintlig grupp kräver förnya Power BI-autentisering. |
| DataSet-namnet |Ange ett namn för dataset som önskas för Power BI-utdata ska användas |
| Tabellnamn |Ange ett tabellnamn under datauppsättningen för Power BI-utdata. För närvarande kan Power BI-utdata från Stream Analytics-jobb bara ha en tabell i en datamängd |

En genomgång över hur du konfigurerar en Power BI-utdata och instrumentpanelen finns i [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md) artikel.

> [!NOTE]
> Inte uttryckligen skapa dataset och tabellen i Power BI-instrumentpanelen. Dataset och tabellen fylls i automatiskt när jobbet startas och jobbet startar pumpande utdata till Power BI. Observera att om frågan jobbet genereras inte några resultat dataset, och tabellen inte skapats. Observera att om Power BI har redan en datauppsättning och en tabell med samma namn som det anges i det här Stream Analytics-jobbet, över befintliga data.
> 
> 

### <a name="schema-creation"></a>Schemat skapas
Azure Stream Analytics skapar en Power BI dataset och tabell för användarens räkning som om det inte redan finns. I annat fall uppdateras tabellen med nya värden. Det finns för närvarande en begränsning som endast en tabell kan finnas inom en datamängd.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Datatypskonverteringen från Stream Analytics till Power BI
Azure Stream Analytics uppdaterar datamodellen dynamiskt vid körning om resultatet schemat ändras. Kolumnen namnändringar, kolumnen typen ändringar och tillägg eller borttagning av kolumner som spåras.

Den här tabellen innehåller datatypkonverteringar från [Stream Analytics-datatyper](https://msdn.microsoft.com/library/azure/dn835065.aspx) till Power BIs [Entity Data Model (EDM) typer](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) om en POWER BI dataset och tabellen inte finns.


Från Stream Analytics | Till Powerbi
-----|-----|------------
bigint | Int64
nvarchar(max) | Sträng
datetime | DateTime
flyttal | Dubbel
Posten matris | Sträng typ, konstantvärde ”IRecord” eller ”IArray”

### <a name="schema-update"></a>Uppdatera schemat
Stream Analytics härleder modellschemat data baserat på den första uppsättningen av händelser i utdata. Om det behövs uppdateras senare modellschemat data för att hantera inkommande händelser som inte ryms i det ursprungliga schemat.

Den `SELECT *` frågan bör undvikas om du vill förhindra dynamisk schemauppdatering över rader. Förutom potentiella konsekvenser för prestanda, kan även det resultera i osäkerhet på den tid det tar för resultat. Du måste välja exakt fälten som ska visas i Power BI-instrumentpanelen. Dessutom vara datavärdena kompatibel med datatypen.


Föregående/Current | Int64 | Sträng | DateTime | Dubbel
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sträng | Sträng | Dubbel
Dubbel | Dubbel | Sträng | Sträng | Dubbel
Sträng | Sträng | Sträng | Sträng |  | Sträng | 
DateTime | Sträng | Sträng |  DateTime | Sträng


### <a name="renew-power-bi-authorization"></a>Förnya Power BI-auktorisering
Du måste autentiseras Power BI-konto om lösenordet har ändrats sedan jobbet skapades eller senast autentiserad. Om Multi-Factor Authentication (MFA) har konfigurerats på din Azure Active Directory (AAD)-klient måste du också förnya Power BI-auktorisering varannan vecka. Ett symtom på det här problemet är inga jobbutdata och en ”autentisera användaren error” i åtgärden loggarna:

  ![Power BI uppdatera fel](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Lös problemet genom att stoppa körs jobbet och gå till Power BI-utdata.  Klicka på länken ”förnya auktorisering” och starta om jobbet från stoppats senast att undvika dataförlust.

  ![Powerbi förnyar auktorisering](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Azure Table storage](../storage/common/storage-introduction.md) ger hög tillgänglighet mycket skalbar lagring, så att ett program kan anpassas automatiskt efter användarens behov. Table storage är Microsofts NoSQL nyckel-och attributdatabas, vilket kan utnyttja för strukturerade data med färre villkor i schemat. Azure Table storage kan användas för att lagra data för beständiga och effektiv hämtning.

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en tabellutdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Kolumnalias |Ett eget namn som används i frågor för att dirigera utdata till den här table storage. |
| Lagringskonto |Namnet på det lagringskonto där du skickar din utdata. |
| Lagringskontonyckel |Åtkomst till nyckeln som associeras med lagringskontot. |
| Tabellnamn |Namnet på tabellen. Tabellen skapas om den inte finns. |
| Partitionsnyckel |Namnet på utdatakolumnen innehåller Partitionsnyckeln. Partitionsnyckeln är en unik identifierare för partitionen i en given tabell som utgör den första delen av en entitets primärnyckel. Det är ett strängvärde som kan vara upp till 1 KB. |
| Radnyckel |Namnet på utdatakolumnen innehåller Radnyckeln. Radnyckeln är en unik identifierare för en entitet i en given partition. Utgör den andra delen av en entitets primärnyckel. Radnyckeln är ett strängvärde som kan vara upp till 1 KB. |
| Batchstorlek |Antalet poster för en batchåtgärd. Vanligtvis standard räcker för de flesta jobb, referera till den [tabell batchåtgärd spec](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) för mer information om hur du ändrar den här inställningen. |
 
## <a name="service-bus-queues"></a>Service Bus-köer
[Service Bus-köer](https://msdn.microsoft.com/library/azure/hh367516.aspx) erbjuder en First In, First Out (FIFO) meddelandeleverans till en eller flera konkurrerande konsumenter. Normalt förväntas meddelanden tas emot och bearbetas av mottagarna i den ordning som de har lagts till i kön och varje meddelande tas emot och bearbetas av bara en meddelandekonsument.

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en utgående kö.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Kolumnalias |Ett eget namn som används i frågor för att dirigera utdata till den här Service Bus-kö. |
| Service Bus-namnområde |En Service Bus-namnrymd är en behållare för en uppsättning meddelandeentiteter. |
| Könamnet |Namnet på Service Bus-kö. |
| Kön Principnamn |När du skapar en kö kan skapa du även principer för delad åtkomst på fliken Konfigurera för kön. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Kön principnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnrymd |
| Händelsen serialiseringsformat |Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds. |
| Encoding |För CSV- och JSON är UTF-8 endast stöds Kodningsformatet just nu |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och vertikalstreck. |
| Format |Gäller endast för JSON-typen. Radseparering innebär att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. |

Antalet partitioner är [baserat på Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltal som unikt för varje partition.

## <a name="service-bus-topics"></a>Avsnitt om Service Bus
När Service Bus-köer innehåller en kommunikationsmetod för en till en från avsändaren och mottagaren, [Service Bus-ämnen](https://msdn.microsoft.com/library/azure/hh367516.aspx) tillhandahålla en en-till-många-kommunikation.

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en tabellutdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Kolumnalias |Ett eget namn som används i frågor för att dirigera utdata till den här Service Bus-ämne. |
| Service Bus-namnområde |En Service Bus-namnrymd är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny Händelsehubb skapade du även en Service Bus-namnrymd |
| Ämnesnamn |Ämnen är meddelandeentiteter, liknande händelsehubbar och köer. De har utformats för att samla in händelseströmmar från ett antal olika enheter och tjänster. När ett ämne skapas, ges även ett specifikt namn. Meddelanden som skickas till ett ämne är inte tillgängligt om inte en prenumeration har skapats, så se till att det finns en eller flera prenumerationer under avsnittet |
| Avsnittet Principnamn |När du skapar ett ämne kan skapa du även principer för delad åtkomst på fliken avsnittet Konfigurera. Varje princip för delad åtkomst har namn, behörigheter som du ställa in och åtkomstnycklar |
| Avsnittet principnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnrymd |
| Händelsen serialiseringsformat |Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds. |
 | Encoding |Om du använder CSV- eller JSON-format, måste kodning anges. UTF-8 är endast stöds Kodningsformatet just nu |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och vertikalstreck. |

Antalet partitioner är [baserat på Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltal som unikt för varje partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos-DB](https://azure.microsoft.com/services/documentdb/) är en global och flera olika modeller databas tjänsten att obegränsad erbjuder elastisk utskalning runt världen, omfattande frågan och automatisk indexering via schema-oberoende datamodeller garanteras låg latens och branschledande omfattande serviceavtal. Mer information om Cosmos DB samling alternativ för Stream Analytics, referera till den [Stream Analytics med Cosmos DB som utdata](stream-analytics-documentdb-output.md) artikel.

> [!Note]
> Just nu är Azure Stream Analytics endast stöd för anslutning till CosmosDB med **SQL API**.
> Andra Azure Cosmos DB-API: er stöds inte ännu. Om platsen Azure Stream Analytics till Azure DB som Cosmos-konton som har skapats med andra API: er, kanske data inte korrekt lagras. 

I följande tabell beskrivs egenskaperna för att skapa ett Azure DB som Cosmos-utdata.
| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett alias till refererar detta utdata i Stream Analytics-fråga. |
| Mottagare | Cosmos DB |
| Importalternativ | Välj antingen ”Välj Cosmos DB från prenumerationen”, eller ”ange Cosmos DB-inställningar manuellt”.
| Konto-ID | Namn eller endpoint-URI för Cosmos-DB-konto. |
| Kontonyckel | Den delade åtkomstnyckeln för kontot Cosmos DB. |
| Databas | Databasnamnet Cosmos DB. |
| Samlingsnamnsmönster | Namnet på samlingen eller deras mönster för samlingar som ska användas. <br/>Samlingsnamnsformatet kan konstrueras med valfritt {partition}-token, där partitionerna börjar från 0. Två exempel:  <br/>1. _MyCollection_ – en samling med namnet ”MyCollection” måste finnas.  <br/>2. _MyCollection {partition}_ – baserat på partitioneringskolumnen. <br/>Partitionering kolumnen samlingarna måste finnas – ”MyCollection0”, ”MyCollection1”, ”MyCollection2” och så vidare. |
| Partitionsnyckel | Valfri. Det här krävs bara om du använder en {partition}-token i din samlingsnamnsmönstret.<br/> Partitionsnyckeln är namnet på fältet i utdatahändelserna används för att ange nyckel för att partionera utdata över samlingarna.<br/> Enda samling utdata för kan en godtycklig utdatakolumnen användas till exempel PartitionId. |
| Dokument-ID |Valfri. Namnet på fältet i utdatahändelserna används för att ange den primära nyckeln operations baseras på vilka insert eller update.  

## <a name="azure-functions"></a>Azure Functions
Azure Functions är en serverlös beräkningstjänst som gör det möjligt köra kod på begäran utan att behöva installera eller hantera infrastruktur. Gör det möjligt att implementera kod som utlöses av händelser i Azure eller tjänster från tredje part.  Den här möjligheten i Azure Functions för att svara på utlösare gör det fysiska utdata för ett Azure Stream Analytics. Den här utdataadapter tillåter användare att ansluta Stream Analytics till Azure Functions och köra ett skript eller kodavsnitt som svar på en mängd olika händelser.

Azure Stream Analytics anropar Azure Functions via HTTP-utlösare. Det nya Azure funktionen utdata-kortet är tillgänglig med följande konfigurerbara egenskaper:

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Funktionsapp |Namnet på din App i Azure Functions |
| Funktion |Namnet på funktionen i Azure Functions appen |
| Maximal batchstorlek |Den här egenskapen kan användas för att ange den maximala storleken för varje utdata som skickas till din Azure-funktion. Det här värdet är som standard 256 KB |
| Maxantal för Batch  |Som namnet anger kan den här egenskapen du ange det maximala antalet händelser i varje batch som skickas till Azure Functions. Värdet för antal för högsta batch är 100 |
| Nyckel |Om du vill använda en Azure-funktion från en annan prenumeration kan göra du detta genom att tillhandahålla nyckeln för att komma åt din funktion |

Observera att när Azure Stream Analytics tar emot 413 (http-begäran entiteten för stort) undantag från Azure-funktion, minskar storleken på batchar skickar den till Azure Functions. I din Azure Funktionskoden använder du det här undantaget för att se till att Azure Stream Analytics inte skicka stora batchar. Kontrollera också att högsta batch antal och storlek värden som används i funktionen stämmer överens med de värden som anges i Stream Analytics-portalen. 

Även i en situation där det finns ingen händelse hamnar i ett tidsfönster, inga utdata skapas. Därför kallas inte computeResult-funktionen. Det här beteendet är konsekvent med de inbyggda mängdfunktionerna.

## <a name="partitioning"></a>Partitionering

I följande tabell sammanfattas partition support och antalet utdata-skrivare för varje Utdatatyp av:

| Utdatatyp | Partitionering | Partitionsnyckeln  | Antal utdata-skrivare | 
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Använd {date} och {time}-token i prefix sökvägar. Välj datumformat, till exempel ÅÅÅÅ-MM/DD, DD/MM/ÅÅÅÅ-MM-DD-ÅÅÅÅ. HH används för tidsformat. | Samma som indata. | 
| Azure SQL Database | Nej | Ingen | Inte tillämpligt. | 
| Azure Blob Storage | Ja | Använd {date} och {time}-token i sökväg-mönster. Välj datumformat, till exempel ÅÅÅÅ-MM/DD, DD/MM/ÅÅÅÅ-MM-DD-ÅÅÅÅ. HH används för tidsformat. | Samma som indata. | 
| Azure Event Hub | Ja | Ja | Samma som utdata Event Hub-partitioner. |
| Power BI | Nej | Ingen | Inte tillämpligt. | 
| Azure Table Storage | Ja | Alla utdata-kolumnen.  | Samma som indata eller föregående steg. | 
| Azure Service Bus-ämne | Ja | Valt automatiskt. Antalet partitioner baseras på den [storlek och Service Bus SKU](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltal som unikt för varje partition.| Samma som utdata.  |
| Azure Service Bus-kö | Ja | Valt automatiskt. Antalet partitioner baseras på den [storlek och Service Bus SKU](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltal som unikt för varje partition.| Samma som utdata. |
| Azure Cosmos DB | Ja | Använd {partition}-token i samlingsnamnsmönstret. {partition} värdet baseras på PARTITION BY-sats i frågan. | Samma som indata. |
| Azure Functions | Nej | Ingen | Inte tillämpligt. | 


## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
Du har fått en introduktion till Stream Analytics, en hanteringstjänst för analys av dataströmmar från Internet of Things. Mer information om den här tjänsten finns här:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
