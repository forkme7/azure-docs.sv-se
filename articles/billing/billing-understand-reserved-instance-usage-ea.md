---
title: "Förstå Azure reserverade instans användning för företag | Microsoft Docs"
description: "Lär dig hur du läsa hur du använder för att förstå tillämpningen av reserverade för Enterprise-registrering."
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 515eae3c9a84a171bebc5213f5824e1b50336e34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Förstå reserverade användning för Enterprise-registrering
Förstå användning av reserverade instans med hjälp av ReservationId från [Reservation sidan](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) och användning av filen från [EA-portalen.](https://ea.azure.com) Du kan också se reservation användning i sammanfattningen för användning i [EA-portalen.](https://ea.azure.com)

>[!NOTE]
>Om du har köpt reservationen i en betalning per användning fakturering kontext finns [förstå reserverade instans användning för din prenumeration med användningsbaserad betalning.](billing-understand-reserved-instance-usage.md)

I följande avsnitt i förutsätter att du kör en Standard_D1_v2 Windows VM i östra USA region och dina reservation information ser ut som i följande tabell:

| Fält | Värde |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Antal |1|
|SKU | Standard_D1|
|Region | usaöstra |

## <a name="reservation-application"></a>Reservation program

Den maskinvara som del av den virtuella datorn omfattas eftersom den distribuerade virtuella datorn matchar reservation-attribut. Om du vill se vilka Windows-program inte omfattas av reserverade instans, gå till Azure reserverade VM-instanser programvarukostnader, gå till [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md)


### <a name="reservation-usage-in-csv"></a>Användning av reservationen i csv
Du kan hämta EA användning csv från EA-portalen. Filtrera efter ytterligare information i hämtade csv-filen och Skriv i Reservation-ID. Följande skärmbild visar relaterade fält till reservationen:

![EA csv för reserverade instans](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. ReservationId i ytterligare information om fältet representerar reservationen som användes för att tillämpa fördelen att den virtuella datorn.
2. ConsumptionMeter är MeterId för den virtuella datorn.
3. Detta är ReservationMeter med $0 kostnaden eftersom kostnaden för att köra VM har betalats av reservationen. 
4. Standard_D1 är en vCPU Virtuella och den virtuella datorn distribueras utan stöd för Azure-Hybrid. Den här mätaren omfattar därför extra kostnad för Windows-program. Se [Azure reservera VM-instanser Windows programvarukostnader.](billing-reserved-instance-windows-software-costs.md) att hitta mätaren motsvarar D-serien 1 kärna VM. Om Azure Hybrid förmånen används, tillämpas inte den här extra kostnad.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Användning av reservationen i sammanfattningssida för användning i EA-portalen

Reserverade instans användning även visas i sammanfattningen för användning i EA portal: ![EA Användningsöversikt](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Du debiteras inte för maskinvarukomponent av den virtuella datorn som omfattas av reserverade instans. 
2. Du debiteras för Windows-program som Azure Hybrid förmånen inte används. 

## <a name="next-steps"></a>Nästa steg
Mer information om reserverade instanser för virtuella datorer, finns i följande artiklar.

- [Förskottsbetala virtuella datorer med reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reserverade virtuella Datorinstanser](billing-manage-reserved-vm-instance.md)
- [Spara pengar på virtuella datorer med den reserverade virtuella instanser](billing-save-compute-costs-reservations.md)
- [Förstå hur reserverade virtuella datorinstans rabatt används](billing-understand-vm-reservation-charges.md)
- [Förstå reserverade användning för din prenumeration med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Windows programvarukostnader ingår inte i reserverade instanser](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.