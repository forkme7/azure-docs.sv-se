---
title: Ta bort en Azure logganalys-arbetsytan | Microsoft Docs
description: "Lär dig hur du tar bort logganalys-arbetsytan om du har skapat en i en personlig prenumeration eller omstrukturera din arbetsyta modell."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 4f455e26f078360f17f8118f8b5b1db5bd75d473
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Ta bort en Azure logganalys-arbetsytan med Azure-portalen
Det här avsnittet visar hur du använder Azure-portalen för att ta bort en logganalys workpace som du kan inte längre behöver. 

## <a name="to-delete-a-workspace"></a>Så här tar du bort en arbetsyta 
När du tar bort logganalys-arbetsytan raderas alla data som rör din arbetsyta från tjänsten inom 30 dagar.  Vill du vara försiktig när du tar bort en arbetsyta eftersom det kan vara viktiga data och konfigurationer som kan påverka din tjänståtgärder.  
 
Om du är administratör och det finns flera användare som är kopplade till arbetsytan bryts kopplingen mellan användare och arbetsytan. Om användarna är associerade med andra arbetsytor, kan de fortsätta med de andra arbetsytorna logganalys. Men om de inte är associerade med andra arbetsytor behöver sedan de skapa en arbetsyta för att använda Log Analytics. 

1. Logga in på [Azure-portalen](http://portal.azure.com). 
2. I Azure-portalen klickar du på **alla tjänster**. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
3. Välj en arbetsyta i fönstret logganalys prenumerationer och klicka sedan på **ta bort** överst i den mellersta rutan.<br><br> ![Ta bort alternativet från arbetsytan egenskapsrutan](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. När fönstret bekräftelse meddelande visas där du ombeds bekräfta borttagning av arbetsytan klickar du på **Ja**.<br><br> ![Bekräfta borttagning av arbetsytan](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

