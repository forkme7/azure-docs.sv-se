---
title: Lagra autentiseringsuppgifter i Azure Key Vault | Microsoft Docs
description: Lär dig hur du lagrar autentiseringsuppgifterna för datalager som används i en Azure key vault som Azure Data Factory kan automatiskt hämta vid körning.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 9a71a455ac4f406695edf722bc83604539eccaa9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Spara autentiseringsuppgifter i Azure Key Vault

Du kan lagra autentiseringsuppgifter för datalager och beräknar i en [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory hämtar autentiseringsuppgifterna vid körning av en aktivitet som använder data store/beräkning.

För närvarande stöder alla aktivitetstyper utom anpassad aktivitet den här funktionen. För kopplingskonfiguration i synnerhet Kontrollera avsnittet ”länkade tjänstegenskaper” i [varje koppling avsnitt](copy-activity-overview.md#supported-data-stores-and-formats) mer information.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [dokumentationen för Data Factory version1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen är beroende av tjänstidentiteten data factory. Lär dig hur det fungerar från [Data factory tjänstidentiteten](data-factory-service-identity.md) och se till att din data factory har en associerad.

## <a name="steps"></a>Steg

Om du vill referera en autentiseringsuppgift lagras i Azure Key Vault, måste du:

1. **[Hämta data factory-tjänsten](data-factory-service-identity.md#retrieve-service-identity)**  genom att kopiera värdet för ”IDENTITY-ID för programmet” skapas tillsammans med din factory.
2. **Ge service identitet åtkomst till Azure Key Vault.** I nyckelvalvet åtkomst -> Principer -> Lägg till ny -> Sök tjänstprogrammet identitet ID för att ge **hämta** behörighet i listrutan hemliga behörigheterna. Det gör denna avsedda factory åtkomst till hemlighet i nyckelvalvet.
3. **Skapa en länkad tjänst som pekar på Azure Key Vault.** Referera till [Azure Key Vault länkade tjänsten](#azure-key-vault-linked-service).
4. **Skapa data länkade lagringstjänst, i vilken referens motsvarande privata nyckel för lagras i valvet.** Referera till [referens hemlighet i nyckelvalvet](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault länkad tjänst

Följande egenskaper stöds för Azure Key Vault länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureKeyVault**. | Ja |
| baseUrl | Ange Azure Key Vault-URL. | Ja |

**Exempel:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Referens hemlighet i nyckelvalvet

När du konfigurerar ett fält i den länkade tjänsten refererar till en hemlighet i nyckelvalvet stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type i fältet måste anges till: **AzureKeyVaultSecret**. | Ja |
| secretName | Namnet på hemlighet i azure key vault. | Ja |
| secretVersion | Versionen av hemlighet i azure key vault.<br/>Om den inte anges använder alltid den senaste versionen av hemligheten.<br/>Om anges fastnar den angivna versionen.| Nej |
| lagra | Refererar till en Azure Key Vault länkade tjänst som används för att lagra autentiseringsuppgifter. | Ja |

**Exempel: (se avsnittet ”password”)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).