---
title: 'Snabbstart: Tabell-API med .NET – Azure Cosmos DB | Microsoft Docs'
description: Den här snabbstarten visar hur du använder tabell-API i Azure Cosmos DB för att skapa ett program med Azure-portalen och .NET
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quickstart connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.openlocfilehash: c9cbcbd10d19dde3e03ca286d12a3d87513890c1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Snabbstart: Skapa en tabell-API-app med .NET och Azure Cosmos DB 

Den här snabbstarten visar hur du använder [tabell-API](table-introduction.md) i .NET och Azure Cosmos DB för att skapa en app genom att klona ett exempel från GitHub. Den här snabbstarten visar även hur du skapar ett Azure Cosmos DB-konto och hur du använder Datautforskaren för att skapa tabeller och entiteter i den webbaserade Azure-portalen.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

> [!IMPORTANT] 
> Du måste skapa ett nytt tabell-API-konto för att arbeta med de allmänt tillgängliga tabell-API SDK:erna. Tabell-API-konton som skapas i förhandsversionen stöds inte av de allmänt tillgängliga SDK:erna.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```
## <a name="open-the-sample-application-in-visual-studio"></a>Öppna exempelappen i Visual Studio

1. I Visual Studio: Välj **Öppna** på **Arkiv**-menyn och välj sedan **Projekt/lösning**. 

   ![Öppna lösningen](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Navigera till mappen där du klonade exempelappen och öppna filen TableStorage.sln.

   ![Öppna den klonade appen](media/create-table-dotnet/azure-cosmos-db-open-clone.png) 

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. I [Azure-portalen](http://portal.azure.com/) klickar du på **Anslutningssträng**. 

    Använd knapparna på höger sida av fönstret för att kopiera **PRIMÄR ANSLUTNINGSSTRÄNG**.

    ![Visa och kopiera PRIMÄR ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng](./media/create-table-dotnet/connection-string.png)

2. Öppna filen App.config i Visual Studio. 

3. Ta bort kommentarerna för StorageConnectionString på rad 8 och StorageConnectionString på rad 7 eftersom den här självstudien inte använder Azure SDK Storage-emulatorn. Rad 7 och 8 bör nu se ut så här:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Klistra in **PRIMÄR ANSLUTNINGSSTRÄNG** från portalen i värdet StorageConnectionString på rad 8. Klistra in strängen innanför citattecknen. 

    > [!IMPORTANT]
    > Om slutpunkten använder documents.azure.com innebär det att du har ett förhandsversionskonto, och du måste skapa ett [nytt tabell-API-konto](#create-a-database-account) för att kunna arbeta med den allmänt tillgängliga SDK:n för tabell-API. 
    > 

    Rad 8 bör nu se ut ungefär så här:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Tryck på CTRL+S för att spara filen App.config.

Du har nu uppdaterat din app med all information den behöver för att kommunicera med Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen

1. I Visual Studio högerklickar du på projektet **TableStorage** i **Solution Explorer** och därefter på **Hantera NuGet-paket**. 

   ![Hantera NuGet-paket](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)
2. Skriv *Microsoft.Azure.CosmosDB.Table* i rutan **Bläddra** i NuGet. Därmed hittas klientbiblioteket Cosmos DB Table API.
   
   ![Fliken Bläddra i NuGet](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Klicka på **Installera** för att installera **Microsoft.Azure.CosmosDB.Table**-biblioteket. Därmed installeras Azure Cosmos DB Table API-paketet och alla beroenden.

    ![Klicka på Installera](media/create-table-dotnet/azure-cosmosdb-nuget-install.png)

4. Öppna BasicSamples.cs. Högerklicka på rad 52, välj **Brytpunkt** och välj **Infoga brytpunkt**. Infoga en annan brytpunkt på rad 55.

   ![Lägga till en brytpunkt](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Tryck på F5 för att köra appen.

    Konsolfönstret visar namnet på den nya tabelldatabasen (i det här fallet demo91ab4) i Azure Cosmos DB. 
    
    ![Konsolutdata](media/create-table-dotnet/azure-cosmosdb-console.png)

    Se [Felsökning](table-sdk-dotnet.md#troubleshooting) om du får ett felmeddelande om beroenden.

    När du kommer till den första brytpunkten går du tillbaka till Datautforskaren i Azure Portal. Klicka på **Uppdatera**, expandera demotabellen* och klicka på **Entiteter**. Fliken **Entiteter** till höger visar den nya entiteten som har lagts till för Walter Harp. Lägg märke till att telefonnumret för den nya entiteten är 425-555-0101.

    ![Ny entitet](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
6. Stäng fliken **Entiteter** i Datautforskaren.
    
7. Tryck på F5 för att köra appen till nästa brytpunkt. 

    När du kommer till brytpunkten växlar du tillbaka till Azure Portal och klickar på **Entiteter** igen för att öppna fliken **Entiteter**. Du kan nu se att telefonnumret har uppdaterats till 425-555-0105.

8. Tryck på F5 för att köra appen. 
 
   Appen lägger till entiteter som kan användas i en avancerad exempelapp som tabell-API:t inte stöder för närvarande. Appen tar sedan bort tabellen som skapats av exempelappen.

9. I konsolfönstret trycker du på Retur för att avsluta körningen av appen. 
  

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata till tabell-API](table-import.md)

