---
title: "Azure gyors üzembe helyezés – Virtuális gépek létrehozása PowerShell használatával | Microsoft Docs"
description: "Gyorsan megismerheti a Linux rendszerű virtuális gépek PowerShell segítségével történő létrehozásának módját."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: b2526e639c131627838e6ea0254601e80633f7f3
ms.lasthandoff: 03/21/2017


---

# <a name="create-a-linux-virtual-machine-with-powershell"></a>Linux rendszerű virtuális gép létrehozása PowerShell segítségével

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletesen bemutatja, hogyan lehet egy Ubuntu Server 14.04 LTS-t futtató Azure-beli virtuális gépet a PowerShell használatával létrehozni.

Első lépésként a Windows-beli felhasználói profiljának `.ssh` mappájába kell menteni az `id_rsa.pub` fájlt. Az Azure-hoz tartozó SSH-kulcsok létrehozásával kapcsolatos részletes információkért olvassa el az [SSH-kulcsok az Azure-hoz történő létrehozását](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető cikket.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Azure-erőforráscsoport létrehozása Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-networking-resources"></a>Hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location westeurope `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location westeurope `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Hozzon létre egy hálózati biztonsági csoportot és egy hálózati biztonsági csoportszabályt. A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. Ebben az esetben létrejön egy bejövő szabály a 22-es porthoz, amely lehetővé teszi a bejövő SSH-kapcsolatokat.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH
```

Hozzon létre egy hálózati kártyát a virtuális géphez. A hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ebben a konfigurációban a virtuális gép üzembe helyezése során használt beállítások szerepelnek, például a virtuális gép rendszerképe, mérete és hitelesítési konfigurációja.

```powershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 14.04.2-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Hozza létre a virtuális gépet.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy SSH-kapcsolatot a virtuális géppel.

Futtassa a következő parancsokat a virtuális gép nyilvános IP-címének visszaadásához.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Ha olyan rendszert használ, amelyre telepítve van az SSH, a következő paranccsal csatlakozhat a virtuális géphez. Ha Windows rendszert használ, a [Putty](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-ssh-from-windows?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-private-key-for-putty) segítségével hozhatja létre a kapcsolatot. 

```bash 
ssh <Public IP Address>
```

Ha a rendszer kéri, adja meg a következő bejelentkezési felhasználónevet: `azureuser`. Ha megadott egy hozzáférési kódot az SSH-kulcsok létrehozásakor, azt is meg kell adnia.

## <a name="delete-virtual-machine"></a>Virtuális gép törlése

Ha már nincs rá szükség, az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Magas rendelkezésre állású virtuális gép létrehozása oktatóanyag](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[A virtuális gépek PowerShell használatával történő üzembe helyezését ismertető minták megismerése](./virtual-machines-linux-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

