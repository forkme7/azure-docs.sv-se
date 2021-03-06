---
title: Skapa en Azure CDN-profil och CDN-slutpunkt | Microsoft Docs
description: Den här snabbstarten visar hur du aktiverar Azure CDN genom att skapa en ny CDN-profil och CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/13/2018
ms.author: mazha
ms.custom: mvc
ms.openlocfilehash: 6237b47be878217115849b87ebcd3d980665643a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt
I den här snabbstarten aktiverar du Azure Content Delivery Network (CDN) genom att skapa en ny CDN-profil och CDN-slutpunkt. När du har skapat en profil och en slutpunkt kan du börja leverera innehåll till dina kunder.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter
För den här snabbstarten måste du ha skapat ett lagringskonto med namnet *mystorageacct123*, som du använder för ursprungets värdnamn. Mer information finns i [Integrera ett Azure-lagringskonto med Azure CDN](cdn-create-a-storage-account-with-cdn.md)

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt

När du har skapat en CDN-profil kan använda du den för att skapa en slutpunkt.

1. På instrumentpanelen i Azure Portal väljer du den CDN-profil som du skapade. Om du inte hittar den väljer du **Alla tjänster** och sedan **CDN-profiler**. På sidan **CDN-profiler** markerar du den profil som du vill använda. 
   
    CDN-profilsidan visas.

2. Välj **Slutpunkt**.
   
    ![CDN-profil](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Sidan **Lägg till en slutpunkt** visas.

    Använd inställningarna i tabellen på bilden.
   
    ![Fönstret Lägg till slutpunkt](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

    | Inställning | Värde |
    | ------- | ----- |
    | **Namn** | Ange *my-endpoint-123* som slutpunktens värdnamn. Det här namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. Namnet används för att komma åt cachelagrade resurser på domänen _&lt;slutpunktens namn&gt;_.azureedge.net.|
    | **Typ av ursprung** | Välj **Lagring**. | 
    | **Ursprungets värdnamn** | Ange *mystorageacct123.blob.core.windows.net* som värdnamn. Det här namnet måste vara globalt unikt. Om det redan används kan du ange ett annat namn. |
    | **Sökväg till ursprung** | Lämna tomt. |
    | **Ursprungsvärdadress** | Använd det standardgenererade värdet. |  
    | **Protokoll** | Använd de valda standardalternativen för **HTTP** och **HTTPS**. |
    | **Ursprungsport** | Använd standardportvärdena. | 
    | **Optimerat för** | Använd standardvalet **Allmän webbleverans**. |
    
3. Välj **Lägg till** för att skapa den nya slutpunkten.
   
   När slutpunkten har skapats, visas den i en lista över slutpunkter för profilen.
    
   ![CDN-slutpunkt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Slutpunkten kan inte användas direkt eftersom det tar tid för registreringen att sprida sig. 


## <a name="clean-up-resources"></a>Rensa resurser
I föregående steg skapade du en CDN-profil och en CDN-slutpunkt i en resursgrupp. Spara dessa resurser om du vill gå till [Nästa steg](#next-steps) och lära dig hur du lägger till en anpassad domän i din slutpunkt. Men om du inte tänker använda de här resurserna i framtiden kan du ta bort dem genom att ta bort resursgruppen (på så sätt undviker du ytterligare kostnader):

1. På den vänstra menyn i Azure Portal väljer du **Resursgrupper** och sedan **my-resource-group-123**.

2. På sidan **Resursgrupp** väljer du **Ta bort resursgrupp**, skriver *my-resource-group-123* i textrutan och väljer **Ta bort**.

    Den här åtgärden tar bort resursgruppen, profilen och slutpunkten som du skapade i den här snabbstarten.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du lägger till en anpassad domän i din CDN-slutpunkt finns i följande självstudiekurs:

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](cdn-map-content-to-custom-domain.md)


