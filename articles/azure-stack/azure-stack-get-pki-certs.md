---
title: Generera Azure Stack infrastruktur för offentliga nycklar för distribution av Azure-stacken integrerat system | Microsoft Docs
description: Beskriver Azure Stack PKI-certifikat distributionsprocessen för Azure-stacken integrerat system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cbc1efaee7404c3ffc82acea0846136c43eba2a9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure Stack certifikat signering begäran generation

Verktyget Azure Stack beredskap för installation som beskrivs i den här artikeln finns [från PowerShell-galleriet](https://aka.ms/AzsReadinessChecker). Verktyget skapar signering certifikatbegäranden (kundtjänstrepresentanter) lämpar sig för en Azure-Stack-distribution. Certifikat bör begärs, skapas och verifieras med tillräckligt med tid för att testa före distributionen. 

Verktyget Azure Stack beredskap Checker (AzsReadinessChecker) utför följande certifikatbegäranden:

 - **Standard certifikatbegäranden**  
    Begära enligt [generera PKI-certifikat för distribution av Azure-stacken](azure-stack-get-pki-certs.md). 
 - **Typ av begäran**  
    Begära flera jokertecken SAN, flera Domäncertifikat enda jokerteckencertifikat begäranden.
 - **Plattform som en tjänst**  
    Du kan också begära plattform som en tjänst (PaaS) namn på certifikat som anges i [Azure Stack infrastruktur för offentliga nycklar certifikatkrav - valfria PaaS certifikat](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Förutsättningar

Datorn måste uppfylla följande krav innan du genererar CSR(s) för PKI-certifikat för distribution av en Azure-stacken:

 - Microsoft Azure-stacken beredskap layout
 - Attribut för certifikat:
    - Regionnamn
    - Externa fullständigt kvalificerade domännamnet (FQDN)
    - Ämne
 - Windows 10 eller Windows Server 2016

## <a name="generate-certificate-signing-requests"></a>Generera begäranden om Certifikatsignering

Följ dessa steg för att förbereda och validera Azure Stack PKI-certifikat: 

1.  Installera AzsReadinessChecker från en PowerShell-kommandotolk (5.1 eller senare) genom att köra följande cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Deklarera den **ämne** som en ordnad ordlista. Exempel: 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Om ett nätverksnamn (CN) anges detta kommer att skrivas över med det första DNS-namnet för certifikatbegäran.

3.  Deklarera en målkatalogen som redan finns:

    ````PowerShell  
    $outputDirectory = "$ENV:USERNAME\Documents\AzureStackCSR" 
    ````

4. Deklarera **regionnamn** och en **externa FQDN** avsedda för Azure Stack-distributionen.

    ```PowerShell  
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` utgör grunden som alla externa DNS-namn i Azure-stacken skapas i det här exemplet, portalen att `portal.east.azurestack.contoso.com`.

5. Generera en enda certifikatbegäran med flera Alternativt ämnesnamn inklusive de som krävs för PaaS-tjänster:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IncludePaaS
    ````

6. Att skapa enskilda certifikatsigneringsbegäran för varje DNS-namn utan PaaS-tjänster:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleCSR -OutputRequestPath $OutputDirectory
    ````

7. Granska utdata:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

8.  Skicka den **. REQ** fil som har skapats för din CA (intern eller offentlig).  Den angivna katalogen för **Start AzsReadinessChecker** innehåller CSR(s) krävs för att skicka den till en certifikatutfärdare.  Den innehåller också en underordnad katalog som innehåller INF-filer som används under Certifikatgenereringen begäran som referens. Se till att Certifikatutfärdaren genererar certifikat med hjälp av din begäran som skapats som uppfyller den [kraven för Azure-stacken PKI](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Nästa steg

[Förbered Azure Stack PKI-certifikat](azure-stack-prepare-pki-certs.md)

