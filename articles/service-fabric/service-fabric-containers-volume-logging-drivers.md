---
title: Azure Service Fabric Docker Compose (förhandsversion) | Microsoft Docs
description: Azure Service Fabric accepterar formatet Docker Compose för att göra det lättare att dirigera befintliga behållare med hjälp av Service Fabric. Stöd för Docker Compose är för närvarande under förhandsgranskning.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 72ff8e99453d3067492d50c3e35396dba9f23c12
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Använda Docker volym plugin-program och drivrutiner för loggning i din behållaren
Azure Service Fabric har stöd för att ange [Docker volym plugin-program](https://docs.docker.com/engine/extend/plugins_volume/) och [Docker loggning drivrutiner](https://docs.docker.com/engine/admin/logging/overview/) för behållartjänsten. Du kan spara dina data i [Azure Files](https://azure.microsoft.com/services/storage/files/) när din behållaren flyttas eller startas om på en annan värd.

För närvarande stöds endast volym drivrutiner för Linux-behållare. Om du använder Windows-behållare kan du mappa en volym till en Azure-filer [SMB3 resursen](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) utan en volymdrivrutin. Uppdatera dina virtuella datorer (VM) i klustret till den senaste versionen av Windows Server 1709 för den här mappningen.


## <a name="install-the-docker-volumelogging-driver"></a>Installera drivrutinen för Docker-volym-loggning

Om Docker volym/loggning drivrutinen inte är installerad på datorn, kan du installera den manuellt med hjälp av RDP/SSH-protokoll. Du kan utföra installationen med dessa protokoll via en [skaluppsättning för virtuell dator uppstart skriptet](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) eller en [SetupEntryPoint skriptet](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Ett exempel på skript för att installera den [Docker volym drivrutinen för Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) är följande:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter stöder inte mappning SMB monteringar behållare ([som stöds bara på Windows Server version 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Den här begränsningen förhindrar nätverksmappningen för volymen och Azure-filer volym drivrutiner på versioner som är äldre än 1709. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Ange plugin-programmet eller drivrutinen i manifestet
Plugin-program har angetts i applikationsmanifestet på följande sätt:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Den **källa** taggen för den **volym** element som refererar till källmappen. Källmappen kan vara en mapp på den virtuella datorn som är värd för behållarna eller en fjärransluten beständiga arkivet. Den **mål** taggen är platsen som den **källa** mappas till i behållaren körs. Därför måste ditt mål för en plats som redan finns i en behållare.

Parametrar för program stöds för volymer som visas i föregående manifestet fragment (leta efter `MyStoreVar` exempel använda).

När du anger en plugin-volym, skapar Service Fabric automatiskt volymen med hjälp av de angivna parametrarna. Den **källa** taggen är namnet på volymen och **drivrutinen** anger drivrutinens volym plugin-programmet. Alternativen kan specificeras med hjälp av den **DriverOption** tagga på följande sätt:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Om en drivrutin för Docker-loggen har angetts som du behöver distribuera agenter (eller behållare) för att hantera loggarna i klustret. Den **DriverOption** tagg kan användas för att ange alternativ för logg-drivrutinen.

## <a name="next-steps"></a>Nästa steg
Om du vill distribuera behållare till ett Service Fabric-kluster finns i artikeln [distribuera en behållare för Service Fabric](service-fabric-deploy-container.md).
