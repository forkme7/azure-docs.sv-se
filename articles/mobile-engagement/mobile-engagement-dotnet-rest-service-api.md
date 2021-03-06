---
title: 'Använda REST API för att komma åt Azure Mobile Engagement Service API: er'
description: 'Beskriver hur du använder Mobile Engagement REST-API: er för att komma åt Azure Mobile Engagement Service API: er'
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: ea7fe6a58d8173c410573e3aa978cb1975ab5da7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Med hjälp av REST API: er Azure Mobile Engagement Service
> [!IMPORTANT]
> Azure Mobile Engagement dras tillbaka den 31 mars 2018. Den här sidan tas bort strax efter det.
> 

Azure Mobile Engagement tillhandahåller den [Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx) att hantera enheter, Reach/Push-kampanjer osv.

> [!NOTE]
> Tjänsten Azure Mobile Engagement kommer att dras tillbaka i mars 2018 och är för närvarande endast tillgänglig för befintliga kunder. Mer information finns i [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

Om du inte vill använda REST-API: er direkt vi tillhandahåller även en [Swagger-filen](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) att du kan använda med verktyg för att generera SDK: er för ditt språk. Vi rekommenderar att du använder den [AutoRest](https://github.com/Azure/AutoRest) verktyget att generera din SDK från våra Swagger-fil. Vi har skapat en .NET-SDK på ett liknande sätt som gör att du kan interagera med dessa API: er med hjälp av en C#-omslutning och du behöver inte göra autentisering token förhandlingen och uppdatera själv. Se [Service API .NET SDK-exempel](mobile-engagement-dotnet-sdk-service-api.md) att lära dig hur du använder .net SDK-API: t
