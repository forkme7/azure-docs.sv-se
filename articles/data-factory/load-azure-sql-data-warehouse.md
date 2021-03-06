---
title: Läs in data till Azure SQL Data Warehouse med hjälp av Azure Data Factory | Microsoft Docs
description: Använd Azure Data Factory för att kopiera data till Azure SQL Data Warehouse
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 49ba61ba8cf68a39eef21b1939a3e8a6c92f8827
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Läs in data till Azure SQL Data Warehouse med hjälp av Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad skalbar databas som kan bearbeta massiva mängder data, relationell såväl som icke-relationell. SQL Data Warehouse bygger på arkitekturen massivt parallell bearbetning (MPP) som är optimerad för arbetsbelastningar på företag data warehouse. Den erbjuder molntjänster elasticitet möjlighet att skala lagring och beräkning oberoende av varandra.

Komma igång med Azure SQL Data Warehouse är nu enklare än någonsin när du använder Azure Data Factory. Azure Data Factory är en helt hanterad molnbaserade integration datatjänst. Du kan använda tjänsten för att fylla i ett SQL Data Warehouse med data från ditt befintliga system och spara tid när du skapar din Analyslösningar.

Azure Data Factory har följande fördelar för att läsa in data i Azure SQL Data Warehouse:

* **Enkelt att ställa in**: en intuitiv 5-steg-guiden med ingen skript som krävs.
* **Omfattande stöd för datalager**: inbyggt stöd för ett stort utbud av lokala och molnbaserade dataarkiv. En detaljerad lista finns i tabellen i [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
* **Skyddade och kompatibla**: Data överförs över HTTPS eller ExpressRoute. Tjänsten för global närvaro garanterar att dina data aldrig lämnar geografisk gräns.
* **Enastående prestanda genom att använda PolyBase**: Polybase är det mest effektiva sättet att flytta data till Azure SQL Data Warehouse. Använda fristående blob-funktionen för att uppnå hög belastning hastigheter från alla typer av datalager, inklusive Azure Blob storage och Data Lake Store. (Polybase stöder Azure Blob storage och Azure Data Lake Store som standard.) Mer information finns i [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory kopieringsdata till _Läs in data från Azure SQL-databas till Azure SQL Data Warehouse_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

> [!NOTE]
> Mer information finns i [kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Den här artikeln gäller för version 2 av Azure Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i Azure Data Factory version 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure SQL Data Warehouse: Datalagret innehåller data som kopieras från SQL-databasen. Om du inte har en Azure SQL Data Warehouse, se anvisningarna i [skapa ett SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: Den här kursen kopierar data från en Azure SQL database med Adventure Works LT exempeldata. Du kan skapa en SQL-databas genom att följa instruktionerna i [skapa en Azure SQL database](../sql-database/sql-database-get-started-portal.md). 
* Azure storage-konto: Azure Storage används som den _mellanlagring_ blob i masskopieringsåtgärd. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer **ny** > **Data + analys** > **Data Factory**: 
   
   ![Skapa en ny datafabrik](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. I den **nya data factory** anger värden för fälten som visas i följande bild:
      
   ![Sida för ny datafabrik](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Namnet**: Ange ett globalt unikt namn för din Azure data factory. Om du får felet ”datafabriksnamnet \"LoadSQLDWDemo\" är inte tillgängligt” ange ett annat namn för data factory. Du kan till exempel använda namnet  _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumerationen**: Välj din Azure-prenumeration att skapa datafabriken. 
    * **Resursgruppen**: Välj en befintlig resursgrupp från den nedrullningsbara listan eller Välj den **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Välj **V2 (förhandsgranskning)**.
    * **Plats**: Välj platsen för data factory. Endast platser som stöds visas i listrutan. Datalager som används av datafabriken kan vara på andra platser och regioner. Lagrar dessa data inkluderar Azure Data Lake Store, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När du har skapats, gå till din data factory. Du ser den **Data Factory** startsidan som visas i följande bild:
   
   ![Datafabrikens startsida](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Välj den **författare & övervakaren** rutan för att starta programmet på Integration Data i en separat flik.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse

1. I den **Kom igång** väljer den **kopieringsdata** rutan för att starta verktyget kopieringsdata:

   ![Panel för verktyget Kopiera data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. I den **egenskaper** anger **CopyFromSQLToSQLDW** för den **aktivitet** och markerar **nästa**:

    ![Sidan Egenskaper](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. I den **källa datalagret** väljer **Azure SQL Database**, och välj **nästa**:

    ![Sidan Källdatalager](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Gör följande på sidan **Specify the Azure SQL database** (Ange Azure SQL-databas): 
   1. Välj din Azure SQL-server som **servernamn**.
   2. Välj din Azure SQL-databas som **databasnamn**.
   3. Ange namnet på användaren som **användarnamn**.
   4. Ange användarens lösenord för **lösenord**.
   5. Välj **Nästa**.
   
   ![Ange Azure SQL-databas](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. I den **Välj tabeller som du vill kopiera data eller använda en anpassad fråga** anger **SalesLT** att filtrera tabellerna. Välj den **(Markera allt)** om du vill använda alla tabeller för kopian och välj sedan **nästa**: 

    ![Välj källa tabeller](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. I den **data målarkiv** väljer **Azure SQL Data Warehouse**, och välj **nästa**:

    ![Sidan Måldatalager](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. I den **ange Azure SQL Data Warehouse** gör du följande: 

   1. Välj din Azure SQL-server som **servernamn**.
   2. Välj Azure SQL Data Warehouse för den **databasnamnet**.
   3. Ange namnet på användaren som **användarnamn**.
   4. Ange användarens lösenord för **lösenord**.
   5. Välj **Nästa**.
   
   ![Ange Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. I den **tabell mappning** , granska innehållet och väljer **nästa**. Mappningen för en intelligent tabellen visar. Källtabellerna mappas till måltabell baserat på tabellnamnen. Om en källtabellen inte finns i målet, skapar en måltabell Azure Data Factory med samma namn som standard. Du kan också en källtabell mappas till en befintlig måltabellen. 

   > [!NOTE]
   > Skapande av automatisk tabell för SQL Data Warehouse sink gäller när SQL Server eller Azure SQL Database är källan. Om du kopierar data från en annan källa data store, måste du skapa schemat i Azure SQL Data Warehouse sink innan du kör Datakopieringen.

   ![Sidan för tabellmappning](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. I den **schemamappning** , granska innehållet och väljer **nästa**. Intelligent tabell mappningen baseras på kolumnnamnet. Om du låter Data Factory automatiskt skapa tabellerna kan datatypkonvertering inträffa när det finns inkonsekvenser mellan käll- och Arkiv. Om det finns en typkonvertering för data som inte stöds mellan käll- och kolumnen, visas ett felmeddelande om du bredvid motsvarande register.

    ![Sidan för schemamappning](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. I den **inställningar** väljer Azure storage-konto i den **lagringskontonamnet** listrutan. Kontot används för mellanlagring av data innan den läses in i SQL Data Warehouse med PolyBase. När kopieringen är klar rensas automatiskt den tillfälliga data i Azure Storage. Under **avancerade inställningar**, avmarkera den **Använd standardtyp** alternativ:

    ![Sidan Inställningar](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. I den **sammanfattning** , granskar du inställningarna och väljer **nästa**:

    ![Sammanfattningssida](./media/load-azure-sql-data-warehouse/summary-page.png)
13. I den **distribution sida**väljer **övervakaren** att övervaka pipeline (aktivitet):

    ![Distributionssida](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Observera att fliken **Övervaka** till vänster väljs automatiskt. Den **åtgärder** kolumn innehåller länkar för att visa aktivitet kör information och att köra pipelinen: 

    ![Övervaka pipelinekörningar](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Om du vill visa aktivitetskörningar som är associerade med pipeline kör, Välj den **visa aktiviteten körs** länken i den **åtgärder** kolumn. Det finns 10 kopiera aktiviteter i pipeline och varje aktivitet kopierar en tabell med data. Växla tillbaka till pipelinen körs visa, Välj den **Pipelines** längst upp. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. För att övervaka körning av information för varje kopieringsaktiviteten, Välj den **information** länken under **åtgärder** i aktiviteten övervakningsvyn. Du kan övervaka information som mängden data som kopieras från källan till mottagare, överföring av data, utförande med motsvarande varaktighet och används konfigurationer:

    ![Övervakaraktiviteten kör information](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Nästa steg

Gå till följande artikel för att lära dig om Azure SQL Data Warehouse-stöd: 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-koppling](connector-azure-sql-data-warehouse.md)