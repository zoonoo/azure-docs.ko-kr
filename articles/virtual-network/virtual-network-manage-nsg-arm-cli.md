---
title: "네트워크 보안 그룹 관리 - Azure CLI | Microsoft Docs"
description: "Azure 명령줄 인터페이스를 사용하여 네트워크 보안 그룹을 관리하는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee43bfd85bdda16cb496ec938c266c72f0d5688b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="manage-network-security-groups-using-the-azure-cli"></a>Azure CLI를 사용하여 네트워크 보안 그룹 관리

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 클래식 배포 모델 대신 이 모델을 사용하도록 권장합니다.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>필수 요소
아직 설치하지 않은 경우 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치 및 구성하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인합니다. 

## <a name="view-existing-nsgs"></a>기존 NSG 보기
특정 리소스 그룹에서 NSG 목록을 보려면 `-o table` 출력 형식으로 [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) 명령을 실행합니다.

```azurecli
az network nsg list -g RG-NSG -o table
```

예상 출력:

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>NSG에 대한 모든 규칙 나열
**NSG-FrontEnd**라는 NSG 규칙을 보려면 [JMESPATH 쿼리 필터](/cli/azure/query-az-cli2) 및 `-o table` 출력 형식을 사용하여 [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) 명령을 실행합니다.

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

예상 출력:

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list)를 사용하여 NSG에서 사용자 지정 규칙만 나열할 수도 있습니다.
>

## <a name="view-nsg-associations"></a>NSG 연결 보기

**NSG-FrontEnd** NSG가 연결된 리소스를 보려면 `az network nsg show` 명령을 실행합니다. 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

아래의 예제 출력에 표시된 것과 같이 **networkInterfaces** 및 **subnets** 속성을 찾아봅니다.

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

이전 예에서 NSG는 NIC(네트워크 인터페이스)에 연결되지 않으며 **FrontEnd**라는 서브넷에 연결됩니다.

## <a name="add-a-rule"></a>규칙 추가
컴퓨터에서 **NSG-FrontEnd** NSG에 포트 **443**에 대한 **인바운드** 트래픽을 허용하는 규칙을 추가하려면 다음 명령을 입력합니다.

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

예상 출력:

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>규칙 변경
**인터넷**에서만 인바운드 트래픽을 허용하도록 이전에 만든 규칙을 변경하려면 [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) 명령을 실행합니다.

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

예상 출력:

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>규칙 삭제
위에서 만든 규칙을 삭제하려면 다음 명령을 실행합니다.

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>NIC에 NSG 연결
**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에 연결하려면 [az network nic update](/cli/azure/network/nic#az_network_nic_update) 명령을 사용합니다.

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

예상 출력:

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>NIC에서 NSG 분리

**TestNICWeb1** NIC에서 **NSG-FrontEnd** NSG의 연결을 해제하려면 [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) 명령을 다시 실행하고 `--network-security-group` 인수를 빈 문자열(`""`)로 바꿉니다.

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

출력에서 `networkSecurityGroup` 키는 null로 설정됩니다.

## <a name="dissociate-an-nsg-from-a-subnet"></a>서브넷에서 NSG 분리
**FrontEnd** 서브넷에서 **NSG-FrontEnd** NSG의 연결을 해제하려면 [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) 명령을 다시 실행하고 `--network-security-group` 인수를 빈 문자열(`""`)로 바꿉니다.

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

출력에서 `networkSecurityGroup` 키는 null로 설정됩니다.

## <a name="associate-an-nsg-to-a-subnet"></a>서브넷에 NSG 연결
**NSG-FrontEnd** NSG를 **FrontEnd** 서브넷에 다시 연결하려면 다음 명령을 실행합니다.

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

출력에서 `networkSecurityGroup` 키는 다음 값에 유사한 항목이 있습니다.

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>NSG 삭제
리소스에 연결되지 않은 경우 NSG를 삭제할 수 있습니다. NSG를 삭제하려면 다음 단계를 완료합니다.

1. NSG에 연결된 리소스를 확인하려면 [NSG 연결 보기](#View-NSGs-associations)에서처럼 `azure network nsg show`을 실행합니다.
2. NSG가 NIC에 연결된 경우 각 NIC에 대한 [NIC에서 NSG 분리](#Dissociate-an-NSG-from-a-NIC)에서처럼 `azure network nic set`을 실행합니다. 
3. NSG가 서브넷에 연결된 경우 각 서브넷에 대한 [서브넷에서 NSG 분리](#Dissociate-an-NSG-from-a-subnet)에서처럼 `azure network vnet subnet set`을 실행합니다.
4. NSG를 삭제하려면 다음 명령을 실행합니다.

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>다음 단계
* [로깅을 사용합니다](virtual-network-nsg-manage-log.md) .

