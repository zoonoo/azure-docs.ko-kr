---
title: Service Fabric 관리 클러스터에 대 한 네트워크 설정 구성 (미리 보기)
description: NSG 규칙, RDP 포트 액세스, 부하 분산 규칙 등에 대해 Service Fabric 관리 되는 클러스터를 구성 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745916"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Service Fabric 관리 클러스터에 대 한 네트워크 설정 구성 (미리 보기)

기본 네트워킹 구성을 사용 하 여 관리 되는 클러스터 Service Fabric 만들어집니다. 이 구성은 필수 클러스터 기능에 대 한 필수 규칙 및 고객 구성을 용이 하 게 하기 위한 몇 가지 선택적 규칙으로 구성 됩니다.

기본 네트워킹 구성 외에도 시나리오의 요구 사항에 맞게 네트워킹 규칙을 수정할 수 있습니다.

## <a name="nsg-rules-guidance"></a>NSG 규칙 지침

관리 되는 클러스터에 대 한 새 NSG 규칙을 만들 때 고려해 야 할 사항에 주의 하세요.

* 관리 되는 클러스터 Service Fabric 필수 기능의 경우 NSG 규칙 우선 순위 범위 0 ~ 999을 예약 합니다. 1000 보다 작은 우선 순위 값을 사용 하 여 사용자 지정 NSG 규칙을 만들 수 없습니다.
* 관리 되는 클러스터 Service Fabric 선택적인 NSG 규칙을 만들기 위해 우선 순위 3001 ~ 4000을 예약 합니다. 이러한 규칙을 자동으로 추가 하 여 구성을 빠르고 쉽게 만들 수 있습니다. 우선 순위 범위 1000에서 3000로 사용자 지정 NSG 규칙을 추가 하 여 이러한 규칙을 재정의할 수 있습니다.
* 사용자 지정 NSG 규칙의 우선 순위는 1000 ~ 3000 범위 내에 있어야 합니다.

## <a name="apply-nsg-rules"></a>NSG 규칙 적용

[클러스터에 NSG (네트워크 보안 그룹) 규칙을 적용](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype)하려면 클래식 (관리 되지 않는) Service Fabric 클러스터를 사용 하 여 별도의 *Microsoft. network/networkSecurityGroups* 리소스를 선언 하 고 관리 해야 합니다. Service Fabric 관리 되는 클러스터를 사용 하 여 배포 템플릿의 클러스터 리소스 내에서 직접 NSG 규칙을 할당할 수 있습니다.

*ServiceFabric/managedclusters* 리소스 (버전 이상)의 [networksecurityrules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) 속성을 사용 `2021-01-01-preview` 하 여 nsg 규칙을 할당 합니다. 예를 들면 다음과 같습니다.

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="rdp-ports"></a>RDP 포트

Service Fabric 관리 클러스터는 기본적으로 RDP 포트에 대 한 액세스를 허용 하지 않습니다. Service Fabric 관리 클러스터 리소스에서 다음 속성을 설정 하 여 인터넷에 대 한 RDP 포트를 열 수 있습니다.

```json
"allowRDPAccess": true 
```

AllowRDPAccess 속성이 true로 설정 되 면 다음 NSG 규칙이 클러스터 배포에 추가 됩니다.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection 및 HttpGatewayConnection 포트

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG 규칙: SFMC_AllowServiceFabricGatewayToSFRP

Service Fabric 리소스 공급자가 클러스터의 clientConnectionPort 및 httpGatewayConnectionPort에 액세스할 수 있도록 기본 NSG 규칙이 추가 됩니다. 이 규칙은 ' ServiceFabric ' 서비스 태그를 통해 포트에 대 한 액세스를 허용 합니다.

>[!NOTE]
>이 규칙은 항상 추가 되며 재정의할 수 없습니다.

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG 규칙: SFMC_AllowServiceFabricGatewayPorts

ClientConnectionPort에 대 한 액세스를 허용 하 고 인터넷에서 httpGatewayPort 하는 선택적 NSG 규칙입니다. 이 규칙을 통해 고객은 SFX에 액세스 하 고, PowerShell을 사용 하 여 클러스터에 연결 하 고, 외부에서 Service Fabric 클러스터 API 끝점을 사용할 수 있습니다. 

>[!NOTE]
>동일한 포트에 대 한 동일한 액세스, 방향 및 프로토콜 값을 가진 사용자 지정 규칙이 있는 경우에는이 규칙이 추가 되지 않습니다. 사용자 지정 NSG 규칙을 사용 하 여이 규칙을 재정의할 수 있습니다. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

## <a name="load-balancer-ports"></a>부하 분산 장치 포트

Service Fabric 관리 클러스터에서 *Managedcluster* 속성 아래의 "loadBalancingRules" 섹션에 구성 된 모든 부하 분산 장치 (LB) 포트의 기본 우선 순위 범위에서 nsg 규칙을 만듭니다. 이 규칙은 인터넷에서 인바운드 트래픽에 대 한 LB 포트를 엽니다.  

>[!NOTE]
>이 규칙은 선택적 우선 순위 범위에 추가 되며 사용자 지정 NSG 규칙을 추가 하 여 재정의할 수 있습니다.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

## <a name="load-balancer-probes"></a>부하 분산 장치 프로브

관리 클러스터 Service Fabric 관리 클러스터 속성의 "loadBalancingRules" 섹션 아래에 구성 된 모든 포트 뿐만 아니라 패브릭 게이트웨이 포트에 대해 부하 분산 장치 프로브를 자동으로 만듭니다.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="next-steps"></a>다음 단계

[Service Fabric 관리 클러스터 구성 옵션](how-to-managed-cluster-configuration.md)

[Service Fabric 관리 클러스터 개요](overview-managed-cluster.md)
