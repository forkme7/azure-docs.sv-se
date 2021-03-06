---
title: 'Azure-portalen: Skapa en hanterad SQL Database-instans | Microsoft Docs'
description: Skapa en hanterad Azure SQL Database-instans i ett virtuellt nätverk.
keywords: sql database tutorial, create a sql database managed instance
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 1e04b12241121d3894e2d71df423bae4f000a60d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Skapa en hanterad Azure SQL Database-instans i Azure-portalen

Den här kursen visar hur du skapar en hanterad Azure SQL Database-instans (förhandsversion) med Azure Portal i ett dedikerat undernät för ett virtuellt nätverk (VNet) och sedan ansluter till den hanterade instansen med SQL Server Management Studio på en virtuell dator i samma virtuella nätverk.

> [!div class="checklist"]
> * Godkänn prenumerationen
> * Konfigurera ett virtuellt nätverk (VNet)
> * Skapa ny routningstabell och en väg
> * Applicera routningstabellen för undernätet för den hanterade instansen
> * Skapa en hanterad instans
> * Skapa ett nytt undernät i det virtuella nätverket för en virtuell dator
> * Skapa en virtuell maskin i det nya undernätet i det virtuella nätverket
> * Ansluta till den virtuella datorn
> * Installera SSMS och anslut till den hanterade instansen


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT]
> En lista över regioner där hanterade instanser är tillgängliga finns i [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/) (Migrera dina databaser till en helt hanterad tjänst med Azure SQL Database Managed Instance).
 
## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="whitelist-your-subscription"></a>Godkänn prenumerationen

Hanterade instanser frigörs först som en skyddad förhandsversion som kräver att din prenumeration ska vara godkänd. Om prenumerationen inte är godkänd utför du följande steg för att ta emot och acceptera villkoren för förhandsversionen och för att skicka en begäran om vitlistning av prenumerationen.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp den **hanterade instansen** och välj sedan **Azure SQL Database Managed Instance (förhandsversion)**.
3. Klicka på **Skapa**.

   ![managed instance create](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Välj din prenumeration, klicka på **villkoren för förhandsversionen** och ange sedan den begärda informationen.

   ![managed instance preview terms](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Klicka på **OK** när du är klar.

   ![managed instance preview terms](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > Du kan fortsätta och slutföra följande avsnitt i dessa självstudier medan du väntar på godkännande av förhandsversionen.

## <a name="configure-a-virtual-network-vnet"></a>Konfigurera ett virtuellt nätverk (VNet)

Följande steg visar hur du skapar ett nytt virtuellt [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md)-nätverk (VNet) för användning av din hanterade instans. Mer information om konfiguration av virtuella nätverk finns i avsnittet om [konfiguration av virtuella nätverk för hanterade instanser](sql-database-managed-instance-vnet-configuration.md).

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp och klicka på **Virtual Network**, kontrollera den **Resource Manager** som är markerad för distributionsläge och klicka sedan på **Skapa**.

   ![virtual network create](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Fyll i formuläret för det virtuella nätverket med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Adressutrymme**|Giltiga adressintervall, t.ex. 10.14.0.0/24|Det virtuella nätverkets adressnamn i CIDR-notation.|
   |**Prenumeration**|Din prenumeration|Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions).|
   |**Resursgrupp**|Giltiga resursgrupper (nya eller befintliga)|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Plats**|Valfri giltig plats| För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Namn på undernät**|Giltiga undernätsnamn, t.ex. mi_subnet|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Adressintervall för undernätet**|Giltiga adressintervall, t.ex. 10.14.0.0/28. Använd ett adressutrymme för undernät som är mindre än själva adressutrymmet för att ge utrymme för att skapa andra undernät i samma virtuella nätverk, t.ex. ett undernät som värd för test/klientappar eller gateway-undernät för att ansluta från lokala nätverk eller andra virtuella nätverk.|Undernätets adressintervall i CIDR-notation. Detta måste finnas i det virtuella nätverkets adressutrymme|
   |**Serviceslutpunkter**|Disabled|Aktivera en eller flera slutpunkter för det här undernätet|
   ||||

   ![virtual network create form](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Klicka på **Skapa**.

## <a name="create-new-route-table-and-a-route"></a>Skapa ny routningstabell och en väg

Följande steg visar hur du skapar en väg för nästa hopp till Internet 0.0.0.0/0.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp och klicka på **Routningstabell** och klicka sedan på **Skapa** på sidan Routningstabell. 

   ![route table create](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Fyll i formuläret för routningstabellen med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Prenumeration**|Din prenumeration|Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions).|
   |**Resursgrupp**|Välj den resursgrupp som du skapade i de tidigare stegen|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Plats**|Välj den resursgrupp som du angav i de tidigare stegen| För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Inaktivera BCP-vägspridning**|Disabled||
   ||||

   ![route table create form](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Klicka på **Skapa**.
5. När routningstabellen har skapats öppnar du den.

   ![route table](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Klicka på **Vägar** och sedan på **Lägg till**.

   ![route table add](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  Lägg till **0.0.0.0/0 Next Hop Internet route (nästa hopp, Internet-väg)** som den **enda** vägen, med hjälp av informationen i följande tabell:

    | Inställning| Föreslaget värde | Beskrivning |
    | ------ | --------------- | ----------- |
    |**Vägnamn**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
    |**Adressprefix**|0.0.0.0/0|Den IP-adress i CIDR-notationen som den här vägen avser.|
    |**Nästa hopp-typ**|Internet|Nästa hopp hanterar matchande paket för den här vägen|
    |||

    ![route](./media/sql-database-managed-instance-tutorial/route.png)

8. Klicka på **OK**.

## <a name="apply-the-route-table-to-the-managed-instance-subnet"></a>Applicera routningstabellen för undernätet för den hanterade instansen

Följande steg visar hur du anger den nya routningstabellen för undernätet för den hanterade instansen.

1. Ange routningstabellen för undernätet för den hanterade instansen genom att öppna det virtuella nätverk du skapade tidigare.
2. Klicka på **Undernät** och sedan på undernätet för den hanterade instansen (**mi_subnet** i följande skärmbild).

    ![subnet](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Klicka på **Routningstabell** och välj sedan **myMI_route_table**.

    ![set route table](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Klicka på **Spara**

    ![set route table-save](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

Följande steg visar hur du skapar en hanterad instans när förhandsversionen har godkänts.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp den **hanterade instansen** och välj sedan **Azure SQL Database Managed Instance (förhandsversion)**.
3. Klicka på **Skapa**.

   ![managed instance create](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Välj din prenumeration och kontrollera att villkoren för förhandsversionen är **godkända**.

   ![managed instance preview accepted](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. Fyll i formuläret för den hanterade instansen med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn på hanterad instans**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratörsinloggning för hanterad instans**|Giltigt användarnamn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Resursgrupp**|Den resursgrupp som du skapade tidigare||
   |**Plats**|Den plats som du tidigare valt|För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Virtuellt nätverk**|Det virtuella nätverk som du skapade tidigare|

   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. Klicka på **Prisnivå** för att beräkna storlek på instanser och lagringsresurser samt granska alternativen för prisnivå. Som standard får din instans 32 GB lagringsutrymme kostnadsfritt, vilket kanske inte är tillräckligt för dina program.
6. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor. 
   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. När du är klar klickar du på **Verkställ** för att spara ditt val.  
8. Klicka på **Skapa** för att distribuera den hanterade instansen.
9. Klicka på **ikonen för meddelanden** för att visa status för distributionen.
 
   ![deployment progress](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. Klicka på **Distribution pågår** för att öppna fönstret för den hanterade instansen om du vill fortsätta övervaka förloppet för distributionen.
 
   ![deployment progress 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Under distributionen kan du fortsätta till nästa procedur.

> [!IMPORTANT]
> För den första instansen i ett undernät tar distributionen vanligtvis mycket längre tid än för efterföljande instanser. Ibland kan den ta mer än 24 timmar att slutföra. Avbryt inte distributionen om den tar längre tid än förväntat. Att det tar lång tid att distribuera den första instansen är bara tillfälligt. Förvänta dig en betydande minskning av distributionstiden kort efter att den offentliga granskningen börjat.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>Skapa ett nytt undernät i det virtuella nätverket för en virtuell dator

Följande steg visar hur du skapar ett andra undernät i det virtuella nätverket för en virtuell dator där du installerar SQL Server Management Studio och ansluter till din hanterade instans.

1. Öppna den virtuella nätverksresursen.
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. Klicka på **Undernät** och sedan på **+Undernät**.
 
   ![add subnet](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. Fyll i formuläret för undernätet med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Adressintervall (CIDR-block)**|Giltiga adressintervall inom det virtuella nätverket (använd standardvärdet)||
   |**Nätverkssäkerhetsgrupp**|Ingen||
   |**Routningstabell**|Inget||
   |**Tjänstens slutpunkter**|Ingen||

   ![vm subnet details](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Klicka på **OK**.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Skapa en virtuell maskin i det nya undernätet i det virtuella nätverket

Följande steg visar hur du skapar en virtuell dator i samma virtuella nätverk som den hanterade instansen skapas i. 

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter** eller **Windows 10**. I det här avsnittet av självstudierna används Windows Server. Windows 10 konfigureras på ungefär samma sätt. 

   ![compute](./media/sql-database-managed-instance-tutorial/compute.png)

3. Fyll i formuläret för den virtuella datorn med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Typ av virtuell datordisk**|SSD|SSD ger det bästa förhållandet mellan pris och prestanda.|   
   |**Användarnamn**|Giltigt användarnamn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).| 
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Prenumeration**|Din prenumeration|Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions).|
   |**Resursgrupp**|Den resursgrupp som du skapade tidigare||
   |**Plats**|Den plats som du tidigare valt||
   |**Har du redan en Windows-licens?**|Nej|Om du äger Windows-licenser med aktivt Software Assurance (SA) kan du använda Azure Hybrid-förmån för att spara beräkningskostnader|
   ||||

   ![virtual machine create form](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. Klicka på **OK**.
4. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. För den här självstudien behöver du bara en liten virtuell dator.

    ![VM-storlekar](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. Klicka på **Välj**.
6. I formuläret **Inställningar** klickar du på **Undernät** och väljer **vm_subnet**. Välj inte undernätet där den hanterade instansen finns, utan välj i stället ett annat undernät i samma virtuella nätverk.

    ![Inställningar för virtuell dator](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. Klicka på **OK**.
8. På sammanfattningssidan kontrollerar du informationen i erbjudandet och klickar sedan på **Skapa** för att starta distributionen av den virtuella datorn.
 
## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Följande steg visar hur du ansluter till den nya virtuella datorn med hjälp av en fjärrskrivbordsanslutning.

1. När distributionen är klar går du till den virtuella datorresursen.

    ![Virtuell dator](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Klicka på knappen **Anslut** på den virtuella datorns egenskaper. En protokollfil för fjärrskrivbord (.rdp-fil) skapas och hämtas.
3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. 
4. Klicka på **Anslut** när du uppmanas till det. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

5. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

6. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

Du är ansluten till den virtuella datorn i Serverhanterarens instrumentpanel.

> [!IMPORTANT]
> Fortsätt inte förrän den hanterade instansen har etablerats. När den har etablerats hämtar du värdnamnet för din instans i fältet **Hanterad instans** på fliken **Översikt** för din hanterade instans. Namnet kan se ut så här: **drfadfadsfd.tr23.westus1-a.worker.database.windows.net**.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>Installera SSMS och anslut till den hanterade instansen

Följande steg visar hur du laddar ned och installerar SSMS och sedan ansluter till dina hanterade instanser.

1. I Serverhanteraren klickar du på **Lokal server** i det vänstra fönstret.

    ![server manager properties](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. I fönstret **Egenskaper** klickar du på **På** för att ändra den förbättrade säkerhetskonfigurationen i Internet Explorer.
3. I dialogrutan **Förbättrad säkerhetskonfiguration i Internet Explorer** klickar du på **Av** i administratörsdelen i dialogrutan och sedan på **OK**.

    ![internet explorer enhanced security configuration](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. Öppna **Internet Explorer** från Aktivitetsfältet.
5. Välj alternativet för att **använda rekommenderade inställningar för säkerhet och kompatibilitet** och klicka sedan på **OK** för att slutföra installationen av Internet Explorer 11.
6. Skriv in https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms i webbadressfältet och klicka på **Retur**. 
7. Ladda ned den senaste versionen av SQL Server Management Studio och klicka på **Kör** när du blir uppmanad att göra det.
8. När du uppmanas till detta klickar du på **Installera** för att börja.
9. När installationen är klar klickar du på **Stäng**.
10. Öppna SSMS.
11. I dialogrutan **Anslut till server** anger du **värdnamnet* för din hanterade instans i fältet **Servernamn**. Välj sedan **SQL Server-autentisering**, ange ditt inloggningsnamn och lösenord och klicka på **Anslut**.

    ![ssms anslut](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

När du ansluter kan du visa system- och användardatabaserna i noden för databaser och olika objekt i noderna för säkerhet, server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

> [!NOTE]
> Om du vill återställa en befintlig SQL-databas till en hanterad instans kan du använda [Azure Database Migration Service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) för att återställa från en databassäkerhetskopia, [T-SQL RESTORE-kommandot](sql-database-managed-instance-restore-from-backup-tutorial.md) för att återställa från en databassäkerhetskopia eller en [import från en BACPAC-fil](sql-database-import.md).

## <a name="next-steps"></a>Nästa steg

I den här kursen fick du lära dig hur du skapar en hanterad Azure SQL Database-instans (förhandsversion) med Azure Portal i ett dedikerat undernät för ett virtuellt nätverk (VNet) och sedan hur du ansluta till den hanterade instansen med SQL Server Management Studio på en virtuell dator i samma virtuella nätverk.  Du har lärt dig att: 

> [!div class="checklist"]
> * Godkänn prenumerationen
> * Konfigurera ett virtuellt nätverk (VNet)
> * Skapa ny routningstabell och en väg
> * Applicera routningstabellen för undernätet för den hanterade instansen
> * Skapa en hanterad instans
> * Skapa ett nytt undernät i det virtuella nätverket för en virtuell dator
> * Skapa en virtuell maskin i det nya undernätet i det virtuella nätverket
> * Ansluta till den virtuella datorn
> * Installera SSMS och anslut till den hanterade instansen

Gå vidare till nästa kurs och lär dig hur du återställer en databassäkerhetskopia till en hanterad Azure SQL Database-instans.

> [!div class="nextstepaction"]
>[Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans](sql-database-managed-instance-restore-from-backup-tutorial.md)
