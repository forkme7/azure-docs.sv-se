---
title: Nyare mått aviseringar i Azure-Monitor stöds resurser | Microsoft Docs
description: Referens för support mått och loggfiler för nyare Azure nära realtid mått aviseringar.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 6d440a49cb30210d3c0eed7d24e4811cc56925b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Nyare mått aviseringar för Azure-tjänster i Azure-portalen
Azure-Monitor stöder nu en ny avisering Måttyp. Nyare aviseringar skiljer sig från [klassiska mått aviseringar](insights-alerts-portal.md) på flera sätt:

- **Förbättrad latens**: nyare mått aviseringar kan köras så ofta som var en minut. Äldre mått aviseringar körs alltid en frekvens på 5 minuter. Loggen aviseringar fortfarande har en längre tid än en minut fördröjningen på grund av tiden är tar att mata in loggarna. 
- **Stöd för flerdimensionella mått**: du kan meddela om dimensionell mått så att du kan övervaka en enda ett intressant segment av måttet. 
- **Mer kontroll över mått villkor**: du kan definiera bättre Varningsregler. Nyare aviseringar stöd för övervakning maximala, minsta, genomsnittlig och totala värden för mått. 
- **Kombinerade övervakning av flera mått**: du kan övervaka flera mått (för närvarande högst två mått) med en enskild regel. En varning aktiveras om båda mått bryta mot sina respektive tröskelvärden för den angivna tidsperioden. 
- **Bättre meddelandesystem**: alla nya aviseringar använder [åtgärdsgrupper](monitoring-action-groups.md), som är namngiven grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar. Använd inte åtgärdsgrupper klassiska mått aviseringar och äldre logganalys-aviseringar. 
- **Mått från loggar** (begränsad förhandsversion): logga data hamnar i logganalys nu kan extraheras och konverteras till Azure-Monitor mått och sedan visas på samma sätt som andra mått. 

Information om hur du skapar en avisering om nyare mått i Azure-portalen finns [skapa en aviseringsregel i Azure portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal). När den har skapats, kan du hantera aviseringen med hjälp av stegen som beskrivs i [Hantera aviseringar i Azure portal](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).


## <a name="portal-powershell-cli-rest-support"></a>Portalen PowerShell, CLI, REST-stöd
För närvarande kan du kan skapa nyare mått aviseringar endast i Azure-portalen [REST API](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template) eller [Resource Manager-mallar](monitoring-create-metric-alerts-with-templates.md). Stöd för att konfigurera nya aviseringar med PowerShell och Azure-kommandoradsgränssnittet (Azure CLI 2.0) kommer snart.

## <a name="metrics-and-dimensions-supported"></a>Mått och dimensioner som stöds
Nyare mått aviseringar stöder aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera dina mått för rätt nivå. Alla stöds mått tillsammans med tillämpliga dimensioner kan utforskade och visualiseras från [Azure-Monitor - Metrics Explorer (förhandsgranskning)](monitoring-metric-charts.md).

Här är en fullständig lista över Azure övervakaren mått källor som stöds av de nyare aviseringarna:

|Resurstyp  |Dimensioner som stöds  | Tillgängliga mått|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ja        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ja   | [Automation-konton](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Gäller inte| [Batch-konton](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    Gäller inte     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    Gäller inte     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   Gäller inte      |[Skaluppsättningar för den virtuella datorn](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ja| [Behållargrupper](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| Ja| [Data fabriker V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ja     |[Data fabriker V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Gäller inte      |[DB för MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Gäller inte     | [DB för PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ja      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Nej | [Valv](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     Gäller inte    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Gäller inte     | [Programgatewayer](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | Gäller inte| [DNS-zoner](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (endast för Standard-SKU: er)| Ja| [Belastningsutjämnare](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  Gäller inte       |[Offentliga IP-Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | Gäller inte | [Kapacitet](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   Gäller inte      |[Search-tjänster](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ja       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ja     | [Lagringskonton](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ja    | [BLOB-tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Filtjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [kö Services](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) och [tabell tjänster](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Gäller inte       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    Gäller inte     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (förhandsgranskning) | Ja|[Log Analytics arbetsytor](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Logganalys loggar som mätvärden för aviseringar 

Du kan också använda nyare mått aviseringar på populära logganalys loggarna extraherat som mått som en del av mätvärden från förhandsversionen av loggar.  
- [Prestandaräknare](../log-analytics/log-analytics-data-sources-performance-counters.md) för Windows och Linux-datorer
- [Heartbeat-poster för Agenthälsa](../operations-management-suite/oms-solution-agenthealth.md)
- [Uppdateringshantering](../operations-management-suite/oms-solution-update-management.md) poster
 
> [!NOTE]
> Specifikt mått och/eller dimension visas bara om det finns data för den valda perioden. De här måtten är tillgängliga för kunder med arbetsytor i östra USA, västra centrala USA och Västeuropa som har valts i förhandsgranskningen. Om du vill ska vara en del av den här förhandsgranskningen kan logga med [undersökningen](https://aka.ms/MetricLogPreview).

I följande lista över logganalys loggbaserade mått källor stöds:

Mått Namnuppgifter  |Dimensioner som stöds  | Typ av logg  |
|---------|---------|---------|
|Average_Avg. Disk sek/läsning     |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Avg. Disk sek/skrivning     |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Current diskkölängd   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Disk Diskläsningar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Disk disköverföringar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|   Average_ % ledigt utrymme    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Available megabyte     |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_ % allokerade byte som används    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
| Average_Bytes/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|  Average_Bytes per sekund    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|  Average_Bytes totalt/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|  Average_ % processortid    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|   Average_Processor Kölängd    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Windows-prestandaräknare      |
|   Average_ % Ledigai   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % ledigt utrymme   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ används i procent  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Använt utrymme i procent för Average_   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk lästa byte/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk Diskläsningar/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk disköverföringar/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk skrivna byte/s   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Disk Diskskrivningar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free megabyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Logical Disk byte/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ tillgängligt minne i procent |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Tillgängligt växlingsutrymme i Average_ % |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Använt minne i procent för Average_  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % använt växlingsutrymme  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Available minne i megabyte    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Available megabyte växlingsutrymme  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Page Diskläsningar/sek |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Page Diskskrivningar/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Pages per sekund  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Used megabyte växlingsutrymme |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Used minne i megabyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total byte som överförs    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total mottagna byte   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total byte    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total antal skickade paket  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total paket som tagits emot |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total Rx fel    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total Tx-fel    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Total kollisioner   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Avg. Disk sek/läsning |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Avg. Disk sek/disköverföring |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Avg. Disk sek/skrivning    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Physical Disk byte/sek    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Privilegierad tid i Average_Pct    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Användartid i Average_Pct  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Used minne Kbyte |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Virtual delat minne  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % DPC-tid |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ inaktivitetstid i procent    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ avbrottstid i procent   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ %-i/o-väntetid |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ Nice Time    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Privilegierad tid i procent för Average_  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ % processortid   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_ Användartid i procent    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free fysiskt minne   |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free utrymme i växlingsfiler |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Free virtuellt minne    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Processes  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Size lagrad i växlingsfiler    |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Uptime |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Average_Users  |     Ja - dator, objektnamn, InstanceName, räknarsökväg & SourceSystem    |   Linux-prestandaräknare      |
|    Pulsslag  |     Ja - dator, OSType, Version och SourceComputerId    |   Heartbeat-poster |
|    Uppdatering |     Ja - dator, produkt, klassificering, UpdateState valfria & godkända    |   Uppdateringshantering |



## <a name="payload-schema"></a>Nyttolasten i schemat

POST-åtgärden innehåller följande JSON-nyttolast och schemat för alla nära nyare mått varningar när en korrekt konfigurerad [grupp](monitoring-action-groups.md) används:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [aviseringar upplevelse](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure](monitor-alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](monitoring-overview-alerts.md).
