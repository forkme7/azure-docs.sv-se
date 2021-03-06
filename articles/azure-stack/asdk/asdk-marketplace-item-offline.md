---
title: Lägg till ett Azure-stacken marketplace-objekt från en lokal källa | Microsoft Docs
description: Beskriver hur du lägger till en lokal operativsystemsavbildning Stack Azure Marketplace.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 676dcdea5912aed5504c386f01d6809eccf763d4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Självstudier: lägga till ett Azure-stacken marketplace-objekt från en lokal källa

Som operatör Azure-stacken är det första du behöver göra så att användarna kan distribuera en virtuell dator (VM) att lägga till en VM-avbildning till stacken för Azure marketplace. Inget publiceras på Azure-stacken Marketplace som standard, men du kan ladda upp VM ISO-avbildningar för att göra dem tillgängliga för användarna. Använd det här alternativet om du har distribuerat Azure Stack i ett scenario med frånkopplade eller i scenarier med begränsad anslutning.

I kursen får du ladda ned en Windows Server 2016 VM-avbildning från den Windows Server-utvärderingar sidan och överföra den till stacken för Azure marketplace.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till en Windows Server 2016 VM-avbildning
> * Verifiera att VM-avbildning är tillgänglig 
> * Testa VM-avbildning

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

- Installera den [Azure Stack-kompatibla Azure PowerShell-moduler](asdk-post-deploy.md#install-azure-stack-powershell)
- Hämta den [Azure Stack-verktyg](asdk-post-deploy.md#download-the-azure-stack-tools)
- Hämta den [ISO-avbildning för Windows Server 2106 virtuella](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) från utvärderingar för Windows Server-sida

## <a name="add-a-windows-server-vm-image"></a>Lägg till en Windows Server-VM-avbildning
Du kan publicera Windows Server 2016-avbildningen till Azure-stacken marketplace genom att lägga till en tidigare hämtade ISO-avbildning med hjälp av PowerShell. 

Använd det här alternativet om du har distribuerat Azure Stack i ett scenario med frånkopplade eller i scenarier med begränsad anslutning.

1. Importera den Azure-stacken `Connect` och `ComputeAdmin` PowerShell-moduler som ingår i katalogen Azure Stack verktyg med hjälp av följande kommandon:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Kör något av följande skript på datorn ASDK värden beroende på om du har distribuerat Azure Stack-miljö med hjälp av Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS):

  - Kommandon för **Azure AD-distributioner**: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - Kommandon för **AD FS-distributioner**:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Lägg till Windows Server 2016-bild till stacken för Azure marketplace. (Ersätt *fully_qualified_path_to_ISO* med sökvägen till Windows Server 2016 ISO som du hämtade.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>Kontrollera marketplace-objektet är tillgängligt
Följ dessa steg för att kontrollera att den nya VM-avbildningen är tillgängliga i stacken för Azure marketplace.

1. Logga in på den [ASDK administrationsportal](https://adminportal.local.azurestack.external).

2. Välj **fler tjänster** > **Marketplace Management**. 

3. Kontrollera att Windows Server 2016 Datacenter VM-avbildning har lagts till.

   ![Hämtade avbildning från Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Testa VM-avbildning
Som operatör Azure Stack kan du använda den [administratörsportal](https://adminportal.local.azurestack.external) för att skapa ett test VM att validera avbildningen har gjorts tillgängliga har. 

1. Logga in på den [ASDK administrationsportal](https://adminportal.local.azurestack.external).

2. Klicka på **nya** > **Compute** > **Windows Server 2016 Datacenter** > **skapa**.  
 ![Skapa virtuell dator från marketplace-avbildning](media/asdk-marketplace-item/new-compute.png)

3. I den **grunderna** bladet anger du följande information och klicka sedan på **OK**:
  - **Namnet**: test-vm-1
  - **Användarnamnet**: AdminTestUser
  - **Lösenordet**: AzS TestVM01
  - **Prenumerationen**: acceptera standardvärdet providern prenumeration
  - **Resursgruppen**: test-vm-rg
  - **Plats**: lokala

4. I den **välja en storlek** bladet, klickar du på **A1 Standard**, och klicka sedan på **Välj**.  

5. I den **inställningar** bladet acceptera standardinställningarna och klickar på **OK**

6. Klicka **OK** på bladet **Sammanfattning** för att skapa den virtuella datorn.  
> [!NOTE]
> Distributionen av virtuella datorer tar några minuter att slutföra.

7. Om du vill visa till den nya virtuella datorn, klickar du på **virtuella datorer**, Sök sedan efter **test-vm-1** och klicka på dess namn.
    ![Första test VM-bild som visas](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Rensa resurser
När du har loggat in på den virtuella datorn, och verifiera att testa image fungerar korrekt, bör du ta bort resursgruppen test. Detta frigör begränsade resurser som är tillgängliga för nod ASDK installationer.

När det inte längre behövs tar du bort resursgruppen, virtuell dator och alla relaterade resurser genom att följa dessa steg:

1. Logga in på den [ASDK administrationsportal](https://adminportal.local.azurestack.external).
2. Klicka på **resursgrupper** > **test-vm-rg** > **ta bort resursgruppen**.
3. Typen **test-vm-rg** som resursgruppens namn och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägg till en Windows Server 2016 VM-avbildning
> * Verifiera att VM-avbildning är tillgänglig 
> * Testa VM-avbildning

Gå vidare till nästa kurs att lära dig hur du skapar en Azure-stacken erbjudandet och planera.

> [!div class="nextstepaction"]
> [Erbjuder Azure Stack IaaS-tjänster](asdk-offer-services.md)
