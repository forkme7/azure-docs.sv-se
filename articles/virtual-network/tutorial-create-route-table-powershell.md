---
title: Dirigera nätverkstrafik Azure PowerShell | Microsoft Docs
description: I den här artikeln lär du dig hur att dirigera nätverkstrafik till en routingtabell med hjälp av PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 2aca1de567dbd4d37daf7f9dd7c407b669396a47
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Dirigera nätverkstrafik till en routingtabell med hjälp av PowerShell

Azure dirigerar automatiskt trafik mellan alla undernät inom ett virtuella nätverk som standard. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbar om du exempelvis vill dirigera trafik mellan undernät via en virtuell nätverksinstallation (NVA). I den här artikeln kan du se hur du:

* Skapa en routningstabell
* Skapa en väg
* Skapa ett virtuellt nätverk med flera undernät
* Associera en routningstabell till ett undernät
* Skapa en NVA som dirigerar trafik
* Distribuera virtuella datorer till olika undernät
* Dirigera trafik från ett undernät till ett annat via en NVA

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver Azure PowerShell Modulversion 5.4.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="create-a-route-table"></a>Skapa en routningstabell

Innan du kan skapa en routingtabell, skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* för alla resurser som har skapats i den här artikeln. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa en routingtabell med [ny AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). I följande exempel skapas en routingtabell som heter *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Skapa en väg

Skapa en väg genom att hämta objektet väg tabell med [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), skapa en väg med [Lägg till AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), sedan skriva konfiguration väg i routningstabellen med [Set AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Innan du kan koppla en routingtabell till ett undernät som du behöver skapa ett virtuellt nätverk och undernät. Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa tre undernät genom att skapa tre konfigurationer för undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). I följande exempel skapas tre konfigurationer för undernät för *offentliga*, *privata*, och *DMZ* undernät:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Skriva undernät konfigurationer till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), vilket skapar undernäten i det virtuella nätverket:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Koppla den *myRouteTablePublic* routningstabellen till den *offentliga* undernät med [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) och sedan skriva undernätskonfiguration till den virtuellt nätverk med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>Skapa en NVA

En NVA är en virtuell dator som utför en nätverksfunktion, som routning, brandvägg eller WAN-optimering.

Innan du skapar en virtuell dator, skapa ett nätverksgränssnitt.

### <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

Innan du skapar ett nätverksgränssnitt kan du behöva hämta den virtuella nätverks-Id med [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), sedan Id för undernätet med [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Skapa ett nätverksgränssnitt med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) i den *DMZ* undernät med IP-vidarebefordring är aktiverat:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator och bifoga en befintlig nätverksgränssnitt, måste du först skapa en VM-konfiguration med [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Konfigurationen innehåller nätverksgränssnittet som du skapade i föregående steg. När du tillfrågas om användarnamn och lösenord, Välj användarnamn och lösenord som du vill logga in på den virtuella datorn med. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Skapa den virtuella datorn med hjälp av VM-konfiguration med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera att trafik från den *offentliga* undernät dirigeras till den *privata* undernätet via virtuell nätverksenhet i ett senare steg. 

Skapa en virtuell dator i den *offentliga* undernät med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVmPublic* i den *offentliga* undernätet för den *myVirtualNetwork* virtuellt nätverk. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Skapa en virtuell dator i den *privata* undernät.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Det tar några minuter att skapa den virtuella datorn. Inte fortsätta med nästa steg förrän den virtuella datorn skapas och Azure returnerar utdata till PowerShell.

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att returnera den offentliga IP-adressen för den *myVmPrivate* VM. I följande exempel returneras den offentliga IP-adressen för den *myVmPrivate* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbords-session med den *myVmPrivate* virtuell dator från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returnerades från föregående kommando.

```
mstsc /v:<publicIpAddress>
```

Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det.

Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen. 

Kommandot tracert.exe används i ett senare steg att testa routning. Tracert använder den kontrollen meddelandet ICMP (Internet Protocol), som nekas via Windows-brandväggen. Aktivera ICMP via Windows-brandväggen genom att ange följande kommando från PowerShell på den virtuella datorn *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Även om spårningsrutt för att testa routning i den här artikeln, rekommenderas inte att tillåta ICMP via Windows-brandväggen för Produktionsdistribution.

Du aktiverade IP-vidarebefordran inom Azure för de virtuella datorernas nätverksgränssnitt i [Aktivera IP-vidarebefordran](#enable-ip-forwarding). I den virtuella datorn måste operativsystemet, eller ett program som körs i den virtuella datorn, kunna vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordring i operativsystemet på den *myVmNva*.

Från en kommandotolk på den *myVmPrivate* VM fjärrskrivbord till den *myVmNva*:

``` 
mstsc /v:myvmnva
```
    
Om du vill aktivera IP-vidarebefordran inom operativsystemet anger du följande kommando i PowerShell från den virtuella datorn *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Starta om den virtuella datorn *myVmNva*, vilket även kopplar från fjärrskrivbordssessionen.

När du fortfarande är ansluten till den virtuella datorn *myVmPrivate* ska du skapa en fjärrskrivbordssession till *myVmPublic*när *myVmNva* har startats om:

``` 
mstsc /v:myVmPublic
```
    
Aktivera ICMP via Windows-brandväggen genom att ange följande kommando från PowerShell på den virtuella datorn *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Om du vill testa att dirigera nätverkstrafik till den virtuella datorn *myVmPrivate* från *myVmPublic* anger du följande kommando från PowerShell på *myVmPublic*:

```
tracert myVmPrivate
```

Svaret liknar följande exempel:
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
Du kan se att det första hoppet är 10.0.2.4, som är NVA-enhetens privata IP-adress. Det andra hoppet är is 10.0.1.4, som är den privata IP-adressen för den virtuella datorn *myVmPrivate*. Vägen som har lagts till i routningstabellen *myRouteTablePublic* och associerats till det *offentliga* undernätet gjorde så att Azure dirigerade trafiken via NVA istället för direkt till det *privata* undernätet.

Stäng fjärrskrivbordssession för den virtuella datorn *myVmPublic*. Du är fortfarande ansluten till *myVmPrivate*.

Om du vill testa att dirigera nätverkstrafik till den virtuella datorn *myVmPublic* från *myVmPrivate* anger du följande kommando från en kommandotolk på *myVmPrivate*:

```
tracert myVmPublic
```

Svaret liknar följande exempel:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
   
Trace complete.
```

Du ser att trafik vidarebefordras direkt från *myVmPrivate* till the *myVmPublic*. Som standard dirigerar Azure trafik direkt mellan undernät.

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapa en routingtabell och som är kopplad till ett undernät. Du har skapat en enkel virtuell nätverksenhet som dirigeras trafiken från offentliga undernät till ett privat undernät. Distribuera en mängd olika förkonfigurerade virtuella nätverksenheter som utför nätverks-funktioner, till exempel brandvägg och WAN-optimering från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men resurser för vissa Azure PaaS-tjänster går inte att distribuera till ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten för resurserna i vissa Azure PaaS-tjänster till trafik enbart från ett undernät för ett virtuell dator. Mer information finns i avsnittet [begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources-powershell.md).
