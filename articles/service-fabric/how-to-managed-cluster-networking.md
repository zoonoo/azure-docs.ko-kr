---
title: Service Fabric 관리형 클러스터에 대한 네트워크 설정 구성
description: NSG 규칙, RDP 포트 액세스, 부하 분산 규칙 등에 대해 Service Fabric 관리형 클러스터를 구성하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 5164a7e3aeb1e82700bd5c5bc4d44e55de64421b
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111895612"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Service Fabric 관리형 클러스터에 대한 네트워크 설정 구성

Service Fabric 관리형 클러스터는 기본 네트워킹 구성을 사용하여 만들어집니다. 이 구성은 필수 클러스터 기능에 대한 필수 규칙과 기본적으로 모든 아웃바운드 트래픽을 허용하는 등의 몇 가지 선택적 규칙으로 구성되어 고객이 보다 수월하게 구성할 수 있습니다.

기본 네트워킹 구성 외에도 시나리오의 요구 사항을 충족하도록 네트워킹 규칙을 수정할 수 있습니다.

## <a name="nsg-rules-guidance"></a>NSG 규칙 참고 자료

관리형 클러스터에 대한 새 NSG 규칙을 만들 때 다음의 고려 사항을 알고 있어야 합니다.

* Service Fabric 관리형 클러스터는 필수 기능을 위해 NSG 규칙 우선 순위 범위 0 ~ 999를 예약합니다. 1000보다 작은 우선 순위 값으로 사용자 지정 NSG 규칙을 만들 수는 없습니다.
* Service Fabric 관리형 클러스터는 선택적인 NSG 규칙을 만들기 위해 우선 순위 범위 3001 ~ 4000을 예약합니다. 이러한 규칙을 자동으로 추가하여 구성을 쉽고 빠르게 만들 수 있습니다. 우선 순위 범위 1000 ~ 3000에 사용자 지정 NSG 규칙을 추가하여 이러한 규칙을 재정의할 수 있습니다.
* 사용자 지정 NSG 규칙은 우선 순위가 범위 1000 ~ 3000 내에 있어야 합니다.

## <a name="apply-nsg-rules"></a>NSG 규칙 적용

[클러스터에 NSG(네트워크 보안 그룹) 규칙을 적용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.servicefabric/service-fabric-secure-nsg-cluster-65-node-3-nodetype)하려면 클래식(비관리형) Service Fabric 클러스터를 사용하여 별도의 *Microsoft.Network/networkSecurityGroups* 리소스를 선언하고 관리해야 합니다. Service Fabric 관리형 클러스터를 사용하여 배포 템플릿의 클러스터 리소스 내에서 직접 NSG 규칙을 할당할 수 있습니다.

*Microsoft.ServiceFabric/managedclusters* 리소스(버전 `2021-05-01` 이상)의 [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) 속성을 사용하여 NSG 규칙을 할당합니다. 예를 들면 다음과 같습니다.

```json
            "apiVersion": "2021-05-01",
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

Service Fabric 관리형 클러스터는 기본적으로 RDP 포트에 대한 액세스를 사용하지 않습니다. Service Fabric 관리형 클러스터 리소스에서 다음 속성을 설정하여 인터넷으로 RDP 포트를 열 수 있습니다.

```json
"allowRDPAccess": true
```

allowRDPAccess 속성을 true로 설정하면 다음 NSG 규칙이 클러스터 배포에 추가됩니다.

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

Service Fabric 관리형 클러스터는 노드 형식의 각 인스턴스에 대한 인바운드 NAT 규칙을 자동으로 만듭니다. 특정 인스턴스(클러스터 노드)에 도달하는 포트 매핑을 찾으려면 다음 단계를 수행합니다.

Azure Portal을 사용하여 RDP(원격 데스크톱 프로토콜)에 대한 인바운드 NAT 규칙을 만든 관리형 클러스터를 찾습니다.

1. SFC_{cluster-id} 형식으로 명명된 구독 내에서 관리형 클러스터 리소스 그룹으로 이동합니다.

2. LB-{cluster-name} 형식을 사용하여 클러스터용 부하 분산 장치를 선택합니다.

3. 부하 분산 장치 페이지에서 인바운드 NAT 규칙을 선택합니다. 인바운드 NAT 규칙을 검토하여 노드의 대상 포트 매핑에 대한 인바운드 프런트 엔드 포트를 확인합니다. 

   다음 스크린샷에는 세 가지 다른 노드 형식에 대한 인바운드 NAT 규칙이 나와 있습니다.

   ![인바운드 NAT 규칙][Inbound-NAT-Rules]

   기본적으로 Windows 클러스터의 경우 프런트 엔드 포트가 50000 이상 범위에 있고, 대상 포트는 포트 3389이며, 대상 노드의 RDP 서비스에 매핑됩니다.

4. 특정 노드(확장 집합 인스턴스)에 원격으로 연결합니다. 클러스터를 만들 때 설정한 사용자 이름 및 암호 또는 구성한 다른 자격 증명을 사용할 수 있습니다.

다음 스크린샷에는 원격 데스크톱 연결을 사용하여 Windows 클러스터의 앱(인스턴스 0) 노드에 연결하는 방법이 나와 있습니다.

![원격 데스크톱 연결][sfmc-rdp-connect]

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection 및 HttpGatewayConnection 포트

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG 규칙: SFMC_AllowServiceFabricGatewayToSFRP

Service Fabric 리소스 공급자가 클러스터의 clientConnectionPort 및 httpGatewayConnectionPort에 액세스할 수 있도록 기본 NSG 규칙이 추가됩니다. 이 규칙은 'ServiceFabric' 서비스 태그를 통해 포트에 대한 액세스를 허용합니다.

>[!NOTE]
>이 규칙은 항상 추가되며 재정의할 수 없습니다.

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

이 선택적 규칙을 사용하면 고객이 SFX에 액세스하고, PowerShell을 통해 클러스터에 연결하며, clientConnectionPort 및 httpGatewayPort용 LB 포트를 열어 인터넷에서 Service Fabric 클러스터 API 엔드포인트를 사용할 수 있습니다.

>[!NOTE]
>동일한 포트에 대해 액세스, 방향, 프로토콜 값이 동일한 사용자 지정 규칙이 있는 경우 이 규칙은 추가되지 않습니다. 사용자 지정 NSG 규칙으로 이 규칙을 재정의할 수 있습니다.

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

Service Fabric 관리형 클러스터는 *Managedcluster* 속성의 "loadBalancingRules" 섹션에 구성된 모든 부하 분산 장치(LB) 포트에 대해 기본 우선 순위 범위에서 NSG 규칙을 만듭니다. 이 규칙은 인터넷에서 인바운드 트래픽용 LB 포트를 엽니다.  

>[!NOTE]
>이 규칙은 선택적 우선 순위 범위에 추가되며 사용자 지정 NSG 규칙을 추가하여 재정의할 수 있습니다.

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

Service Fabric 관리형 클러스터는 관리형 클러스터 속성의 "loadBalancingRules" 섹션에 구성된 모든 포트뿐만 아니라 패브릭 게이트웨이 포트에 대한 부하 분산 장치 프로브를 자동으로 만듭니다.

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

[Service Fabric 관리형 클러스터 구성 옵션](how-to-managed-cluster-configuration.md)

[Service Fabric 관리형 클러스터 개요](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png

