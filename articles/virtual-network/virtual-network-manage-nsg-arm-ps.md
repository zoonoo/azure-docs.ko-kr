---
title: 네트워크 보안 그룹 관리 - Azure PowerShell | Microsoft Docs
description: PowerShell을 사용하여 네트워크 보안 그룹을 관리하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca7f4926ca4edf9d20612aca74f6ae5f0ed847b3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2018
ms.locfileid: "30960378"
---
# <a name="manage-network-security-groups-using-powershell"></a>PowerShell을 사용하여 네트워크 보안 그룹 관리

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 클래식 배포 모델 대신 이 모델을 사용하도록 권장합니다.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>정보 검색
기존 NSG를 보고 기존 NSG에 대한 규칙을 검색하며 NSG가 연결된 리소스에 대해 알아볼 수 있습니다.

### <a name="view-existing-nsgs"></a>기존 NSG 보기
구독에서 모든 기존 NSG를 보려면 `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

예상된 결과:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


특정 리소스 그룹에 있는 NSG 목록을 보려면 `Get-AzureRmNetworkSecurityGroup` cmdlet을 실행합니다.

예상 출력:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>NSG에 대한 모든 규칙 나열
**NSG-FrontEnd**로 명명된 NSG의 규칙을 보려면 다음 명령을 입력합니다.

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

예상 출력:

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> 또한 `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules`을 사용하여 **NSG-FrontEnd** NSG에서 기본 규칙을 나열할 수 있습니다.
> 

### <a name="view-nsgs-associations"></a>NSG 연결 보기
**NSG-FrontEnd** NSG가 연결된 리소스를 보려면 다음 명령을 실행합니다.

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

아래와 같이 **NetworkInterfaces** 및 **서브넷** 속성을 찾아봅니다.

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

이전 예에서 NSG는 NIC(네트워크 인터페이스)에 연결되지 않습니다. **FrontEnd**라는 서브넷에 연결됩니다.

## <a name="manage-rules"></a>규칙 관리
기존 NSG에 규칙을 추가하고 기존 규칙을 편집하며 규칙을 제거할 수 있습니다.

### <a name="add-a-rule"></a>규칙 추가
컴퓨터에서 **NSG-FrontEnd** NSG에 포트 **443**에 대한 **인바운드** 트래픽을 허용하는 규칙을 추가하려면 다음 단계를 완료합니다.

1. 다음 명령을 실행하여 기존 NSG를 검색하고 변수에 저장합니다.

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. 다음 명령을 실행하여 규칙을 NSG에 추가합니다.

    ```powershell
    Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. NSG에 대한 변경 사항을 저장하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
    보안 규칙만 표시하는 확장된 출력:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>규칙 변경
**인터넷** 에서 인바운드 트래픽을 허용하도록 위에서 만든 규칙을 변경하려면는 아래 단계를 수행합니다.

1. 다음 명령을 실행하여 기존 NSG를 검색하고 변수에 저장합니다.

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. 새 규칙 설정을 사용하여 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix Internet `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. NSG에 대한 변경 사항을 저장하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    보안 규칙만 표시하는 확장된 출력:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>규칙 삭제
1. 다음 명령을 실행하여 기존 NSG를 검색하고 변수에 저장합니다.

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. NSG로부터 규칙을 삭제하려면 다음 명령을 실행합니다.

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. NSG에 대한 변경 사항을 저장하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    보안 규칙만 표시하는 예상된 출력에는 더 이상 **https-rule** 이 나열되지 않습니다.
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>연결 관리
NSG를 서브넷 및 NIC에 연결할 수 있습니다. 또한 연결된 모든 리소스에서 NSG를 분리할 수 있습니다.

### <a name="associate-an-nsg-to-a-nic"></a>NIC에 NSG 연결
**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에 연결하려면 다음 단계를 완료합니다.

1. 다음 명령을 실행하여 기존 NSG를 검색하고 변수에 저장합니다.

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. 다음 명령을 실행하여 기존 NIC를 검색하고 변수에 저장합니다.

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. **NIC** 변수의 **NetworkSecurityGroup** 속성을 다음 명령을 입력하여 **NSG** 변수의 값으로 설정합니다.

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. NIC에 대한 변경 사항을 저장하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    **NetworkSecurityGroup** 속성만을 표시하는 예상된 출력입니다.
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>NIC에서 NSG 분리
**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에서 분리하려면 다음 단계를 완료합니다.

1. 다음 명령을 실행하여 기존 NIC를 검색하고 변수에 저장합니다.

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. **NIC** 변수의 **NetworkSecurityGroup** 속성을 다음 명령을 실행하여 **$null** 변수의 값으로 설정합니다.

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. NIC에 대한 변경 사항을 저장하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    **NetworkSecurityGroup** 속성만을 표시하는 예상된 출력입니다.
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>서브넷에서 NSG 분리
**NSG-FrontEnd** NSG를 **FrontEnd** 서브넷에서 분리하려면 다음 단계를 완료합니다.

1. 다음 명령을 실행하여 기존 VNet 을 검색하고 변수에 저장합니다.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. 다음 명령을 실행하여 **FrontEnd** 서브넷을 검색하고 변수에 저장합니다.

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. **서브넷** 변수의 **NetworkSecurityGroup** 속성을 다음 명령을 입력하여 **$null** 변수의 값으로 설정합니다.

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. 서브넷에 대한 변경 사항을 저장하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    **FrontEnd** 서브넷의 속성만을 표시하는 예상된 출력입니다. **NetworkSecurityGroup**에 대한 속성이 없습니다.
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>서브넷에 NSG 연결
**NSG-FrontEnd** NSG를 **FronEnd** 서브넷에 다시 연결하려면 다음 단계를 완료합니다.

1. 다음 명령을 실행하여 기존 VNet 을 검색하고 변수에 저장합니다.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. 다음 명령을 실행하여 **FrontEnd** 서브넷을 검색하고 변수에 저장합니다.

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. 다음 명령을 실행하여 기존 NSG를 검색하고 변수에 저장합니다.

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. **서브넷** 변수의 **NetworkSecurityGroup** 속성을 다음 명령을 실행하여 **$null** 변수의 값으로 설정합니다.

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. 서브넷에 대한 변경 사항을 저장하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    **FrontEnd** 서브넷의 **NetworkSecurityGroup** 속성만을 표시하는 예상된 출력입니다.
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>NSG 삭제
리소스에 연결되지 않은 경우 NSG를 삭제할 수 있습니다. NSG를 삭제하려면 다음 단계를 수행합니다.

1. NSG에 연결된 리소스를 확인하려면 [NSG 연결 보기](#View-NSGs-associations)에서처럼 `azure network nsg show`을 실행합니다.
2. NSG가 NIC에 연결된 경우 각 NIC에 대한 [NIC에서 NSG 분리](#Dissociate-an-NSG-from-a-NIC)에서처럼 `azure network nic set`을 실행합니다. 
3. NSG가 서브넷에 연결된 경우 각 서브넷에 대한 [서브넷에서 NSG 분리](#Dissociate-an-NSG-from-a-subnet)에서처럼 `azure network vnet subnet set`을 실행합니다.
4. NSG를 삭제하려면 다음 명령을 실행합니다.

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > `-Force` 매개 변수를 사용하면 삭제를 확인하지 않아도 됩니다.
   > 

## <a name="next-steps"></a>다음 단계
* [로깅을 사용합니다](virtual-network-nsg-manage-log.md) .

