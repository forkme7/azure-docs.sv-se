---
title: "Växla över och misslyckas tillbaka Hyper-V VM replikerat till Azure med Site Recovery | Microsoft Docs"
description: "Lär dig hur du växla över Hyper-V virtuella datorer till Azure och växla tillbaka till den lokala platsen med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/8/2018
ms.author: raynew
ms.openlocfilehash: 7863feb29fbb04f643aa3b7e1984209f44cdbe9a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Växla över och misslyckas tillbaka Hyper-V VM replikerat till Azure

Den här självstudiekursen beskrivs hur du redundansväxlar en Hyper-V virtuell dator till Azure. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Kontrollera egenskaperna Hyper-V-dator för att kontrollera överensstämmer med kraven för Azure
> * Köra en redundans i Azure
> * Återaktivera skyddet av virtuella Azure-datorer till den lokala platsen
> * Växla tillbaka från Azure till lokala
> * Återaktivera skyddet av lokala virtuella datorer för att börja replikera till Azure igen

Detta är den femte självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna.

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokal VMware](tutorial-prepare-on-premises-hyper-v.md)
3. Ställ in katastrofåterställning för [Hyper-V VMs](tutorial-hyper-v-to-azure.md), eller för [Hyper-V-datorer som hanteras i System Center VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
4. [Köra ett återställningstest](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Förbereda för redundans och återställning efter fel

Kontrollera att det finns inga ögonblicksbilder på den virtuella datorn och att den lokala virtuella datorn är avstängd under återaktivera skydd. Detta hjälper att säkerställa datakonsekvens vid replikeringen. Sätt inte på den virtuella datorn förrän återaktiveringen av skyddet har slutförts. 

Redundans och återställning efter fel består av fyra steg:

1. **Redundansväxla till Azure**: Redundansväxlar datorer från den lokala platsen till Azure.
2. **Skyddar virtuella datorer i Azure**: skyddar Azure virtuella datorer, så att de startar replikeras tillbaka till lokala Hyper-V virtuella datorer.
3. **Växla över till lokala**: kör en redundans från Azure till lokala platsen, när den är tillgänglig.
4. **Skapa nytt lokalt VMs**: när data har misslyckats tillbaka, skyddar lokala virtuella datorer för att starta replikering av dem till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera VM-egenskaperna och se till att den virtuella datorn uppfyller [Azure-kraven](hyper-v-azure-support-matrix.md#replicated-vms).

1. I **skyddade objekt**, klickar du på **replikerade objekt** >< VM-name >.

2. I den **replikerade objekt** fönstret granska informationen om VM, hälsotillstånd och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
     - I **beräknings- och nätverksinställningar**, du kan ändra inställningarna för virtuell dator och nätverksinställningar, inklusive undernätet där den virtuella Azure-datorn. Hanterade diskar stöds inte för återställning efter fel från Azure till Hyper-V.
   kommer att finnas efter växling vid fel och IP-adressen som ska tilldelas till den.
    - I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="fail-over-to-azure"></a>Växla över till Azure

1. I **Inställningar** > **Replikerade objekt** klickar du på VM > **+Redundans**.
2. I **redundans** markerar du den **senaste** återställningspunkt. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker göra en avstängning av virtuella källdatorer innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
4. När du har kontrollerat redundans Klicka **genomför**. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas så stoppas replikeringen av den virtuella datorn. Om du avbryter pågår, stoppar för växling vid fel, men den virtuella datorn inte kommer att replikeras igen.

## <a name="reprotect-azure-vms"></a>Återaktivera skyddet av virtuella Azure-datorer

1. I den **AzureVMVault** > **replikerade objekt**, högerklicka på den virtuella datorn som har redundansväxlats och välj **skydda igen**.
2. Kontrollera att skyddet riktningen är **Azure till lokala**.
3. Den lokala virtuella datorn stängs av under återaktivera skydd för att säkerställa datakonsekvens. Aktivera inte den efter att återaktivera skyddet har slutförts.
4. Den virtuella datorn efter att återaktivera skyddet-processen börjar replikering från Azure till den lokala platsen.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Växla över från Azure och skyddar den lokala virtuella datorn

Växla över från Azure till den lokala platsen och starta replikering av virtuella datorer från den lokala platsen till Azure.

1. I **inställningar** > **replikerade objekt**, klicka på den virtuella datorn > **planerad redundans**.
2. I **bekräfta planerad redundans**kontrollerar redundansriktning (från Azure) och välj käll- och målplatserna.
3. Välj **synkronisera data innan redundans (synkronisera endast deltaändringar)**. Det här alternativet minimerar avbrottstid för virtuell dator eftersom det synkroniserar utan att stänga av den virtuella datorn.
4. Påbörja redundans. Du kan följa förloppet för växling vid fel på den **jobb** fliken.
5. När den inledande synkroniseringen är klar och du är redo att stänga av den virtuella Azure-datorer klickar du på **jobb** > planerad-redundans-jobb-name > **slutföra växling vid fel**. Detta stängs av Azure VM, överför den senaste ändringar lokalt och startar den virtuella datorn lokalt.
6. Logga in på den lokala virtuella datorn att kontrollera att den är tillgänglig som förväntat.
7. Den lokala virtuella datorn är nu i en **bekräfta väntande** tillstånd. Klicka på **genomför**. Detta tar bort den virtuella Azure-datorer och dess diskar och förbereder den lokala virtuella datorn för omvänd replikering.
Starta replikering av den lokala virtuella datorn till Azure genom att aktivera **omvänd replikera**. Detta utlöser replikering av deltaändringar som har inträffat sedan den virtuella Azure-datorn är avstängd.  
