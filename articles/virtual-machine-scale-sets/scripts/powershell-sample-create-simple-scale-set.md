---
title: Azure PowerShell-exempel – skapa en grundläggande VM-skalningsuppsättning| Microsoft Docs
description: Azure PowerShell-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d6417803ae465ec167f1f8980aae84629715c7d7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-basic-virtual-machine-scale-set-with-powershell"></a>Skapa en grundläggande VM-skalningsuppsättning med PowerShell
Det här skriptet skapar en VM-skalningsuppsättning som kör Windows Server 2016. När skriptet har körts får du åtkomst till de virtuella datorinstanserna via RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.ps1 "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Skapar VM-skalningsuppsättningen och alla stödresurser, inklusive virtuellt nätverk, belastningsutjämning och NAT-regler. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för skalningsuppsättningar för virtuella Azure-datorer](../powershell-samples.md).