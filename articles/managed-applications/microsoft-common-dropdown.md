---
title: Azure DropDown UI-element | Microsoft Docs
description: Beskriver Microsoft.Common.DropDown UI-element för Azure-portalen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6c92304ae623807ffba05dcdbb576e1cef63b10c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI-element
En markering med en listruta.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "my value",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Etikett för `constraints.allowedValues` är texten som visas för ett objekt och dess värde är värdet av elementet när du valt.
- Om du standardvärdet måste vara en etikett som finns i `constraints.allowedValues`. Om inget anges det första objektet i `constraints.allowedValues` är markerad. Standardvärdet är **null**.
- `constraints.allowedValues` måste innehålla minst ett objekt.
- Det här elementet har inte stöd för den `constraints.required` egenskapen. Emuleras problemet genom att lägga till ett objekt med en etikett och värdet för `""` (tom sträng) till `constraints.allowedValues`.

## <a name="sample-output"></a>Exempel på utdata
```json
"Bar"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).
