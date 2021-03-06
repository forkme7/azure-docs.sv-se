---
title: Självstudie – skapa och använd diskar för skalningsuppsättningar med Azure CLI 2.0 | Microsoft Docs
description: Läs hur du använder Azure CLI 2.0 för att skapa och använda hanterade diskar med en VM-skalningsuppsättning, inklusive hur du lägger till, förbereder, listar och kopplar från diskarna.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 86ab38fffa8099f2f9f758a4da89fdfcbb3c7543
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli-20"></a>Självstudie: Skapa och använd diskar med en VM-skalningsuppsättning med Azure CLI 2.0
VM-skalningsuppsättningar använder diskar för att lagra den virtuella datorinstansens operativsystem, program och data. När du skapar och hanterar en skalningsuppsättning, är det viktigt att välja en diskstorlek och konfiguration som lämpar sig för den förväntade arbetsbelastningen. Den här självstudien beskriver hur du skapar och hanterar virtuella datordiskar. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Anslut och förbered datadiskar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, måste du köra Azure CLI version 2.0.29 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).


## <a name="default-azure-disks"></a>Azure-standarddiskar
När en skalningsuppsättning skapas eller skalas, ansluts två diskar automatiskt till varje virtuell datorinstans.

**Operativsystemdisken** – Operativsystemdiskar kan vara upp till 2 TB stora och innehåller de virtuella datorernas operativsystem. Operativsystemdisken kallas som standard */dev/sda*. OS-diskens cachelagringkonfiguration har optimerats för OS-prestanda. Därför bör OS-disken **inte** innehålla program eller data. För program och data använder du datadiskar som beskrivs senare i den här artikeln.

**Temporär disk** – Temporära diskar använder en SSD-enhet som finns på samma Azure-värd som den virtuella datorinstansen. Det här är högpresterande diskar och kan användas för åtgärder som till exempel tillfällig databearbetning. Om den virtuella datorinstansen flyttas till en ny värddator tas dock alla data som lagras på den temporära disken bort. Storleken på den temporära disken bestäms av den virtuella datorinstansens storlek. Temporära diskar kallas */dev/sdb* och har monteringspunkten */mnt*.

### <a name="temporary-disk-sizes"></a>Storlekar för temporära diskar
| Typ | Normala storlekar | Maxstorlek för temporär disk (GiB) |
|----|----|----|
| [Generellt syfte](../virtual-machines/linux/sizes-general.md) | A-, B- och D-serien | 1600 |
| [Beräkningsoptimerad](../virtual-machines/linux/sizes-compute.md) | F-serien | 576 |
| [Minnesoptimerad](../virtual-machines/linux/sizes-memory.md) | D-, E-, G- och M-serien | 6144 |
| [Lagringsoptimerad](../virtual-machines/linux/sizes-storage.md) | L-serien | 5630 |
| [GPU](../virtual-machines/linux/sizes-gpu.md) | N-serien | 1440 |
| [Höga prestanda](../virtual-machines/linux/sizes-hpc.md) | A- och H-serien | 2000 |


## <a name="azure-data-disks"></a>Azure-datadiskar
Du kan lägga till ytterligare datadiskar om du behöver installera program och lagra data. Datadiskar används när du behöver hållbar och responsiv datalagring. Varje datadisk har en maxkapacitet på 4 TB. Storleken på den virtuella datorinstansen avgör hur många datadiskar som kan anslutas. Två datadiskar kan kopplas för varje VM vCPU.

### <a name="max-data-disks-per-vm"></a>Maximalt antal datadiskar per VM
| Typ | Normala storlekar | Maximalt antal datadiskar per VM |
|----|----|----|
| [Generellt syfte](../virtual-machines/linux/sizes-general.md) | A-, B- och D-serien | 64 |
| [Beräkningsoptimerad](../virtual-machines/linux/sizes-compute.md) | F-serien | 64 |
| [Minnesoptimerad](../virtual-machines/linux/sizes-memory.md) | D-, E-, G- och M-serien | 64 |
| [Lagringsoptimerad](../virtual-machines/linux/sizes-storage.md) | L-serien | 64 |
| [GPU](../virtual-machines/linux/sizes-gpu.md) | N-serien | 64 |
| [Höga prestanda](../virtual-machines/linux/sizes-hpc.md) | A- och H-serien | 64 |


## <a name="vm-disk-types"></a>VM-disktyper
Azure tillhandahåller två disktyper.

### <a name="standard-disk"></a>Standarddiskar
Standardlagring stöds av hårddiskar och levererar kostnadseffektiv lagring och prestanda. Standarddiskar passar perfekt för kostnadseffektiv utveckling och testning av arbetsbelastningar.

### <a name="premium-disk"></a>Premiumdiskar
Premiumdiskar backas upp av SSD-baserade diskar med höga prestanda och låg latens. De här diskarna rekommenderas för virtuella datorer som kör produktionsarbetsbelastningar. Premium Storage stöder virtuella datorer i DS-serien, DSv2-serien GS-serien och FS-serien. När du väljer en diskstorlek, avrundas värdet uppåt till nästa typ. Om diskstorleken till exempel är mindre än 128 GB är disktypen P10. Om disktypen är mellan 129 GB och 512 GB är storleken P20. Över 512 GB är storleken en P30.

### <a name="premium-disk-performance"></a>Premiumdiskprestanda
|Premium Storage-disktyp | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Diskens storlek (avrundas uppåt) | 32 GB | 64 GB | 128 GB | 512 GB | 1 024 GB (1 TB) | 2 048 GB (2 TB) | 4 095 GB (4 TB) |
| Högsta IOPS per disk | 120 | 240 | 500 | 2 300 | 5 000 | 7 500 | 7 500 |
Dataflöde per disk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

I tabellen ovan visas högsta IOPS per disk, men högre prestanda kan uppnås genom strimling över flera datadiskar. En Standard_GS5 virtuell dator kan till exempel uppnå maximalt 80 000 IOPS. Mer information om högsta IOPS per VM finns i [Storlekar för virtuella Linux-datorer](../virtual-machines/linux/sizes.md).


## <a name="create-and-attach-disks"></a>Skapa och koppla diskar
Du kan skapa och ansluta diskar när du skapar en skalningsuppsättning eller med en befintlig skalningsuppsättning.

### <a name="attach-disks-at-scale-set-creation"></a>Anslut diskarna när skalningsuppsättningen skapas
Skapa först en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* i regionen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa en VM-skalningsuppsättning med kommandot [az vmss create](/cli/azure/vmss#az_vmss_create). Följande exempel skapar en skalningsuppsättning med namnet *myScaleSet* och genererar SSH-nycklar om de inte redan finns. Två diskar skapas med parametern `--data-disk-sizes-gb`. Den första disken är *64* GB stor och den andra disken är *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorinstanser.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anslut en disk till en befintlig skalningsuppsättning
Du kan även ansluta diskar till en befintlig skalningsuppsättning. Använd skalningsuppsättningen som skapades i det föregående steget för att lägga till en annan disk med [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach). Följande exempel ansluter en ytterligare *128* GB disk:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Förbered datadiskarna
Diskarna som skapas och ansluts till dina skalningsuppsättningar för virtuella datorinstanser är rådiskar. Innan du kan använda dem med dina data och program, måste de förberedas. För att förbereda diskarna, skapar du en partition, skapar ett filsystem och monterar dem.

Du kan använda det anpassade skripttillägget för Azure för att automatisera processen på flera virtuella datorinstanser i en skalningsuppsättning. Det här tillägget kan köra skript lokalt på varje virtuell datorinstans, till exempel för att förbereda anslutna datadiskar. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/linux/extensions-customscript.md).

Följande exempel kör ett skript från ett GitHub-exempellager på varje virtuell datorinstans med [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) som förbereder alla anslutna rådatadiskar:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

För att bekräfta att diskarna har förberetts korrekt, SSH:ar du till en av de virtuella datorinstanserna. Lista anslutningsinformationen för din skalningsuppsättning med [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Använd din egna offentliga IP-adress och portnummer för att ansluta till den första virtuella datorinstansen som visas i följande exempel:

```azurecli-interactive
ssh azureuser@52.226.67.166 -p 50001
```

Undersök partitionerna på den virtuella datorinstansen på följande sätt:

```bash
sudo fdisk -l
```

Följande exempelutdata visar att tre diskar är anslutna till den virtuella datorinstansen – */dev/sdc*, */dev/sdd* och */dev/sde*. Var och en av dessa diskar har en enda partition som använder allt tillgängligt utrymme:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Granska filsystemen och monteringspunkterna på den virtuella datorinstansen på följande sätt:

```bash
sudo df -h
```

Följande exempelutdata visar att de tre diskarna har sina filsystem korrekt monterade under */datadisks*:

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

Diskarna på varje virtuell datorinstans i din skala förbereds automatiskt på samma sätt. Allteftersom din skalningsuppsättning skalar upp, ansluts de nödvändiga datadiskarna till de nya virtuella datorinstanserna. Det anpassade skripttillägget körs också för att automatiskt förbereda diskarna.

Stäng SSH-anslutningen till din VM-instans:

```bash
exit
```


## <a name="list-attached-disks"></a>Lista de anslutna diskarna
Du kan visa information om anslutna diskar till en skalningsuppsättning med [az vmss show](/cli/azure/vmss#az_vmss_show) och fråga på *virtualMachineProfile.storageProfile.dataDisks*:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

Information om diskstorleken, lagringsnivå och LUN (Logical Unit Number) visas. Följande exempelutdata visar information om de tre datadiskarna som är kopplade till skalningsuppsättningen:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Koppla från en disk
När du inte längre behöver en angiven disk, kan du koppla från den från skalningsuppsättningen. Disken tas bort från alla virtuella datorinstanser i skalningsuppsättningen. Om du vill koppla bort en disk från en skalningsuppsättning, använder du [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach) och anger LUN på disken. LUN visas i utdata från [az vmss show](/cli/azure/vmss#az_vmss_show) från föregående avsnitt. Följande exempel kopplar från LUN *2* från skalningsuppsättningen:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och diskar så tar du bort resursgruppen och alla dess resurser med [az group delete](/cli/azure/group#az_group_delete). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du läsa om hur du skapar och använder diskar med skalningsuppsättningar med Azure CLI 2.0:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Anslut och förbered datadiskar

Gå vidare till nästa självstudie för att läsa hur du använder en anpassad avbildning för din skalningsuppsättning för virtuella datorinstanser.

> [!div class="nextstepaction"]
> [Använd en anpassad avbildning för skalningsuppsättningar för virtuella datorinstanser](tutorial-use-custom-image-cli.md)