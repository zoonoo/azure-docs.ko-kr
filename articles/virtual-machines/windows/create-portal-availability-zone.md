---
title: "Azure Portal을 사용하여 영역 설정된 Windows VM 만들기 | Microsoft Docs"
description: "Azure Portal을 사용하여 가용성 영역에서 Windows VM 만들기"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 4d48aff7d29def9fa54438a11885b4ff4fba54cc
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Azure Portal을 사용하여 가용성 영역에서 Windows 가상 컴퓨터 만들기

이 문서는 Azure Portal을 사용하여 Azure 가용성 영역에서 가상 컴퓨터를 만드는 단계를 안내합니다. [가용성 영역](../../availability-zones/az-overview.md)은 Azure 지역에서 물리적으로 별도 영역입니다. 가용성 영역을 사용하여 가능성이 적은 실패 또는 전체 데이터 센터의 손실로부터 앱 및 데이터를 보호합니다.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]


## <a name="log-in-to-azure"></a>Azure에 로그인 

https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 

3. 가상 컴퓨터 정보를 입력합니다. 여기에서 입력한 이사용자 이름과 암호는 가상 컴퓨터에 로그인하는 데 사용됩니다 완료되면 **확인**을 클릭합니다.

    ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 가용성 영역 미리 보기에서 *DS1_v2 표준*과 같은 지원되는 크기 중 하나를 신중하게 선택합니다. 

    ![VM 크기를 보여 주는 스크린샷](./media/create-portal-availability-zone/create-linux-vm-portal-sizes.png)  

5. **설정** > **고가용성** 아래의 **가용성 영역** 드롭다운에서 번호가 매겨진 영역 중 하나를 선택하고 나머지 기본값을 유지하고 **확인**을 클릭합니다.

    ![가용성 영역 선택](./media/create-portal-availability-zone/create-linux-vm-portal-availability-zone.png)

6. 요약 페이지에서 **구매**를 클릭하여 가상 컴퓨터 배포를 시작합니다.

7. Azure Portal 대시보드에 VM을 고정합니다. 배포가 완료되면 VM 요약이 자동으로 열립니다.


## <a name="zone-for-ip-address-and-managed-disk"></a>IP 주소 및 관리 디스크에 대한 영역

가용성 영역에 VM을 배포하는 경우 IP 주소 및 관리 디스크 리소스는 동일한 가용성 영역에 배포됩니다. Azure PowerShell을 사용하여 영역 설정을 확인할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

다음 예에서는 *myResourceGroup*이라는 리소스 그룹에서 리소스에 대한 정보를 가져옵니다. VM을 만드는 데 사용한 리소스 그룹의 이름을 대체합니다.

[Get-AzureRmPublicIpAddress](/en-us/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 공용 IP 주소의 영역을 찾습니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup
```
공용 IP 주소를 보여 주는 출력의 `Zones` 설정은 VM과 동일한 가용성 영역에 있습니다.

```powershell
Name                     : myVM-ip
ResourceGroupName        : myResourceGroup
Location                 : eastus2
Id                       : /subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/danlep0911/providers/Micr
                           osoft.Network/publicIPAddresses/myVM-ip
Etag                     : W/"b67e14c0-7e8a-4d12-91c5-da2a5dfad132"
ResourceGuid             : 314bf57d-9b25-4474-9282-db3561d536aa
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.68.16.25
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVM11842/ipConfigurations/ipconfig1"
                           }
DnsSettings              : null
Zones                    : {2}
```


VM에 대한 관리 디스크 리소스도 동일한 가용성 영역에서 만들어집니다. [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk)를 사용하여 이를 확인할 수 있습니다.

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

관리 디스크를 보여 주는 출력은 VM과 동일한 가용성 영역에 있습니다.

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역에서 VM을 만드는 방법을 배웠습니다. Azure VM에 대한 [영역 및 가용성](regions-and-availability.md)에 대해 자세히 알아봅니다.
