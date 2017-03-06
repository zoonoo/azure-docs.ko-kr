---
title: "네트워크 보안 그룹 관리 - Azure CLI 1.0 | Microsoft Docs"
description: "Azure CLI(명령줄 인터페이스) 1.0을 사용하여 네트워크 보안 그룹을 관리하는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 2e53c3ff2ffbef95d6b72ca6afb3b4de377f0389
ms.lasthandoff: 02/28/2017


---
# <a name="manage-network-security-groups-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 네트워크 보안 그룹 관리

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전 

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다. 

- [Azure CLI 1.0](#View-existing-NSGs) - 클래식 및 리소스 관리 배포 모델용 CLI 
- [Azure CLI 2.0](virtual-network-manage-nsg-arm-cli.md) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 클래식 배포 모델 대신 이 모델을 사용하도록 권장합니다.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="retrieve-information"></a>정보 검색
기존 NSG를 보고 기존 NSG에 대한 규칙을 검색하며 NSG가 연결된 리소스에 대해 알아볼 수 있습니다.

### <a name="view-existing-nsgs"></a>기존 NSG 보기
특정 리소스 그룹에 있는 NSG 목록을 보려면 아래와 같이 `azure network nsg list` 명령을 실행합니다.

```azurecli
azure network nsg list --resource-group RG-NSG
```

예상 출력:

    info:    Executing command network nsg list
    + Getting the network security groups
    data:    Name          Location
    data:    ------------  --------
    data:    NSG-BackEnd   westus
    data:    NSG-FrontEnd  westus
    info:    network nsg list command OK

### <a name="list-all-rules-for-an-nsg"></a>NSG에 대한 모든 규칙 나열
**NSG-FrontEnd**로 명명된 NSG의 규칙을 보려면 아래와 같이 `azure network nsg show` 명령을 실행합니다. 

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd
```

예상 출력:

    info:    Executing command network nsg show
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Name                            : NSG-FrontEnd
    data:    Type                            : Microsoft.Network/networkSecurityGroups
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    Tags                            : displayName=NSG - Front End
    data:    Security group rules:
    data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
    data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
    data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
    data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
    data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
    data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
    data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
    data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
    data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
    data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
    info:    network nsg show command OK

> [!NOTE]
> 또한 `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd`을 사용하여 **NSG-FrontEnd** NSG에서 규칙을 나열할 수 있습니다.
>

### <a name="view-nsg-associations"></a>NSG 연결 보기

**NSG-FrontEnd** NSG가 연결된 리소스를 보려면 아래와 같이 `azure network nsg show` 명령을 실행합니다. 유일한 차이점은 **--json** 매개 변수를 사용하는 것입니다.

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json
```

아래와 같이 **NetworkInterfaces** 및 **서브넷** 속성을 찾아봅니다.

    "networkInterfaces": [],
    ...
    "subnets": [
        {
            "id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
    ],
    ...

위의 예에서 NSG는 NIC(네트워크 인터페이스)에 연결되지 않고 **FrontEnd**라는 서브넷에 연결됩니다.

## <a name="manage-rules"></a>규칙 관리
기존 NSG에 규칙을 추가하고 기존 규칙을 편집하며 규칙을 제거할 수 있습니다.

### <a name="add-a-rule"></a>규칙 추가
컴퓨터에서 **NSG-FrontEnd** NSG에 포트 **443**에 대한 **인바운드** 트래픽을 허용하는 규칙을 추가하려면 다음 명령을 입력합니다.

```azurecli
azure network nsg rule create --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --description "Allow access to port 443 for HTTPS" \
    --protocol Tcp \
    --source-address-prefix * \
    --source-port-range * \
    --destination-address-prefix * \
    --destination-port-range 443 \
    --access Allow \
    --priority 102 \
    --direction Inbound
```

예상 출력:

    info:    Executing command network nsg rule create
    + Looking up the network security rule "allow-https"
    + Creating a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : *
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule create command OK

### <a name="change-a-rule"></a>규칙 변경
**인터넷**에서 인바운드 트래픽을 허용하도록 위에서 만든 규칙을 변경하려면 다음 명령을 실행합니다.

```azurecli
azure network nsg rule set --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --source-address-prefix Internet
```

예상 출력:

    info:    Executing command network nsg rule set
    + Looking up the network security group "NSG-FrontEnd"
    + Setting a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : Internet
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule set command OK

### <a name="delete-a-rule"></a>규칙 삭제
위에서 만든 규칙을 삭제하려면 다음 명령을 실행합니다.

```azurecli
azure network nsg rule delete --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --quiet
```

> [!NOTE]
> `--quiet` 매개 변수를 사용하면 삭제를 확인하지 않아도 됩니다.
>

예상 출력:

    info:    Executing command network nsg rule delete
    + Looking up the network security group "NSG-FrontEnd"
    + Deleting network security rule "allow-https"
    info:    network nsg rule delete command OK

## <a name="manage-associations"></a>연결 관리
NSG를 서브넷 및 NIC에 연결할 수 있습니다. 또한 연결된 모든 리소스에서 NSG를 분리할 수 있습니다.

### <a name="associate-an-nsg-to-a-nic"></a>NIC에 NSG 연결
**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에 연결하려면 다음 명령을 실행합니다.

```azurecli
azure network nic set --resource-group RG-NSG \
    --name TestNICWeb1 \
    --network-security-group-name NSG-FrontEnd
```

예상 출력:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Looking up the network security group "NSG-FrontEnd"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-nic"></a>NIC에서 NSG 분리

**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에서 분리하려면 다음 명령을 실행합니다.

```azurecli
azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""
```

> [!NOTE]
> `network-security-group-id` 매개 변수에 대한 ""(비어 있음) 값에 유의합니다. NSG에 대한 연결을 제거하는 방법입니다. `network-security-group-name` 매개 변수로 동일하게 수행할 수 없습니다.
> 

예상된 결과:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-subnet"></a>서브넷에서 NSG 분리
**NSG-FrontEnd** NSG를 **FrontEnd** 서브넷에서 분리하려면 다음 명령을 실행합니다.

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-id ""
```

예상 출력:

    info:    Executing command network vnet subnet set
    + Looking up the subnet "FrontEnd"
    + Setting subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:    IP configurations:
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
    data:
    info:    network vnet subnet set command OK

### <a name="associate-an-nsg-to-a-subnet"></a>서브넷에 NSG 연결
**NSG-FrontEnd** NSG를 **FrontEnd** 서브넷에 다시 연결하려면 다음 명령을 실행합니다.

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-name NSG-FronEnd
```

> [!NOTE]
> **NSG-FrontEnd** NSG가 **TestVNet** 가상 네트워크와 동일한 리소스 그룹에 있는 경우 위의 명령이 작동합니다. NSG가 다른 리소스 그룹에 있으면 대신 `--network-security-group-id` 매개 변수를 사용하고 NSG에 대한 전체 ID를 제공해야 합니다. `azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json`을 실행하며 **id** 속성을 찾아 보아 id를 검색할 수 있습니다. 
> 

예상 출력:

        info:    Executing command network vnet subnet set
        + Looking up the subnet "FrontEnd"
        + Looking up the network security group "NSG-FrontEnd"
        + Setting subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
        data:
        info:    network vnet subnet set command OK

## <a name="delete-an-nsg"></a>NSG 삭제
리소스에 연결되지 않은 경우 NSG를 삭제할 수 있습니다. NSG를 삭제하려면 다음 단계를 수행합니다.

1. NSG에 연결된 리소스를 확인하려면 [NSG 연결 보기](#View-NSGs-associations)에서처럼 `azure network nsg show`을 실행합니다.
2. NSG가 NIC에 연결된 경우 각 NIC에 대한 [NIC에서 NSG 분리](#Dissociate-an-NSG-from-a-NIC)에서처럼 `azure network nic set`을 실행합니다. 
3. NSG가 서브넷에 연결된 경우 각 서브넷에 대한 [서브넷에서 NSG 분리](#Dissociate-an-NSG-from-a-subnet)에서처럼 `azure network vnet subnet set`을 실행합니다.
4. NSG를 삭제하려면 다음 명령을 실행합니다.

    ```azurecli
    azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet
    ```

    예상 출력:

        info:    Executing command network nsg delete
        + Looking up the network security group "NSG-FrontEnd"
        + Deleting network security group "NSG-FrontEnd"
        info:    network nsg delete command OK

## <a name="next-steps"></a>다음 단계
* [로깅을 사용합니다](virtual-network-nsg-manage-log.md) .


