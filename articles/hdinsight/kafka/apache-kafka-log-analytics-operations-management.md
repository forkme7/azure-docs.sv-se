---
title: Logga Analytics för Apache Kafka - Azure HDInsight | Microsoft Docs
description: Lär dig använda Log Analytics för att analysera loggar från Apache Kafka kluster på Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: larryfr
ms.openlocfilehash: a373ef5cc71d5ae69c83555dc71525aa2188233e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analysera loggar för Apache Kafka på HDInsight

Lär dig använda Log Analytics för att analysera loggar som genereras av Apache Kafka på HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Aktivera logganalys för Kafka

Steg för att aktivera Log Analytics för HDInsight är samma för alla HDInsight-kluster. Använd följande länkar för att förstå hur du skapar och konfigurerar nödvändiga tjänster:

1. Skapa en logganalys-arbetsyta. Mer information finns i [komma igång med en logganalys-arbetsytan](https://docs.microsoft.com/azure/log-analytics) dokumentet.

2. Skapa en Kafka på HDInsight-kluster. Mer information finns i [börja med Apache Kafka på HDInsight](apache-kafka-get-started.md) dokumentet.

3. Konfigurera Kafka klustret om du vill använda logganalys. Mer information finns i [Använd Log Analytics för att övervaka HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentet.

    > [!NOTE]
    > Du kan också konfigurera klustret för att använda Log Analytics med hjälp av den `Enable-AzureRmHDInsightOperationsManagementSuite` cmdlet. Den här cmdleten kräver följande information:
    >
    > * HDInsight-klustrets namn.
    > * Arbetsyte-ID för Log Analytics. Du hittar arbetsyte-ID i logganalys-arbetsytan.
    > * Den primära nyckeln för logganalys-anslutningen. Välj din logganalys-instans för att hitta den primära nyckeln och sedan __OMS-portalen__. Välj OMS-portalen __inställningar__, __anslutna källor__, och sedan __Linux-servrar__.


> [!IMPORTANT]
> Det kan ta cirka 20 minuter innan data är tillgängliga för Log Analytics.

## <a name="query-logs"></a>Frågeloggar

1. Från den [Azure-portalen](https://portal.azure.com), Välj logganalys-arbetsytan.

2. Välj __logga Sök__. Härifrån kan söka du data som samlas in från Kafka. Följande är några exempel genomsöks:

    * Diskanvändning: `Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * CPU-användning: `Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * Inkommande meddelanden per sekund: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * Inkommande byte per sekund: `Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * Utgående byte per sekund: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > Ersätt värdena frågan med din specifika information för klustret. Till exempel `ClusterName_s` måste anges till namnet på klustret. `HostName_s` måste anges till domännamnet för en worker-nod i klustret.

    Du kan också ange `*` att söka alla typer som är inloggad. Följande loggar är för närvarande tillgängliga för frågor:

    | Loggtyp | Beskrivning |
    | ---- | ---- |
    | loggen\_kafkaserver\_CL | Kafka broker server.log |
    | loggen\_kafkacontroller\_CL | Kafka broker controller.log |
    | mått\_kafka\_CL | Kafka JMX mått |

    ![Bild av sökningen för CPU-användning](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Nästa steg

Mer information om logganalys finns i [komma igång med en logganalys-arbetsytan](../../log-analytics/log-analytics-get-started.md) dokumentet.

Mer information om hur du arbetar med Kafka finns i följande dokument:

 * [Spegling Kafka mellan HDInsight-kluster](apache-kafka-mirroring.md)
 * [Öka skalbarheten för Kafka på HDInsight](apache-kafka-scalability.md)
 * [Använda Spark streaming (DStreams) med Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Använda Spark strukturerad strömning med Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
