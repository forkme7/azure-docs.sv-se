---
title: Regler för namngivning av enheter i Azure Data Factory | Microsoft Docs
description: Beskriver namnregler för enheter som Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0311c2e2223c60861d2b6c87bfa7747dc079856d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - namngivningsregler
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [namngivningsregler i Data Factory version 2](../naming-rules.md).

Följande tabell innehåller namngivningsregler för Data Factory-artefakter.

| Namn | Unika namn | Kontroller |
|:--- |:--- |:--- |
| Data Factory |Unikt över Microsoft Azure. Namn är inte skiftlägeskänsliga, det vill säga `MyDF` och `mydf` referera till samma data factory. |<ul><li>Varje datafabriken är kopplad till en Azure-prenumeration.</li><li>Objektnamn måste börja med en bokstav eller en siffra och får innehålla endast bokstäver, siffror och streck (-).</li><li>Varje streck (-) måste föregås och följas av en bokstav eller en siffra. Streck i följd är inte tillåtna i behållarnamn.</li><li>Namnet kan innehålla 3-63 tecken.</li></ul> |
| Länkade tjänster/tabeller/Pipelines |Unikt med i en data factory. Namn är inte skiftlägeskänsliga. |<ul><li>Maximalt antal tecken i ett tabellnamn: 260.</li><li>Objektnamn måste börja med en bokstav, siffra eller ett understreck (_).</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/” ”, <” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |
| Resursgrupp |Unikt över Microsoft Azure. Namn är inte skiftlägeskänsliga. |<ul><li>Maximalt antal tecken: 1000.</li><li>Namnet får innehålla bokstäver, siffror och följande tecken ”:-” ”, _” ”,,” och ””.</li></ul> |

