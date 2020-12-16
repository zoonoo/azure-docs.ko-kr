---
title: Service Fabric 클러스터에 상태 비저장 전용 노드 형식 배포
description: Azure Service fabric 클러스터에서 상태 비저장 노드 유형을 만들고 배포 하는 방법에 대해 알아봅니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: 6259de345b534bfb51ef6ba1a9c3895800546caf
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605499"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>상태 비저장 전용 노드 유형 (미리 보기)을 사용 하 여 Azure Service Fabric 클러스터 배포
Service Fabric 노드 형식은 특정 시점에 상태 저장 서비스가 노드에 배치 될 수 있다는 가정 하에 제공 됩니다. 상태 비저장 노드 형식은 노드 형식에 대 한 이러한 가정을 완화 하므로 더 빠른 규모 확장 작업과 같은 다른 기능을 사용할 수 있도록 하 고, 단일 가상 머신 확장 집합에서 100 개 보다 많은 노드를 확장 하 여 청동 내구성에서 자동 OS 업그레이드를 지원 합니다.

* 주 노드 형식은 상태 비저장으로 구성할 수 없습니다.
* 상태 비저장 노드 형식은 청동 내구성 수준 에서만 지원 됩니다.
* 상태 비저장 노드 형식은 Service Fabric Runtime 버전 7.1.409 이상 에서만 지원 됩니다.


샘플 템플릿을 사용할 수 있습니다. [Service Fabric 상태 비저장 노드 형식 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Service Fabric 클러스터에서 상태 비저장 노드 형식 사용
클러스터 리소스에서 하나 이상의 노드 유형을 상태 비저장으로 설정 하려면 **Isstateless 비저장** 속성을 "true"로 설정 합니다. 상태 비저장 노드 유형을 사용 하 여 Service Fabric 클러스터를 배포 하는 경우 클러스터 리소스에 하나 이상의 주 노드 유형을 포함 해야 합니다.

* Service Fabric cluster resource apiVersion는 "2020-12-01-preview" 이상 이어야 합니다.

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateles": false,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>상태 비저장 노드 형식에 대 한 가상 머신 확장 집합을 구성 하는 중
상태 비저장 노드 형식을 사용 하려면 다음과 같은 방법으로 기본 가상 머신 확장 집합 리소스를 구성 해야 합니다.

* 100 개 이상의 Vm으로 크기를 조정 하는 요구 사항에 따라 true/false로 설정 해야 하는  **Singleplacementgroup** 속성 값입니다.
* 롤링으로 설정 해야 하는 확장 집합의 **Upgrademode** 입니다.
* 롤링 업그레이드 모드를 사용 하려면 응용 프로그램 상태 확장 또는 상태 프로브를 구성 해야 합니다. 아래 제안 된 대로 상태 비저장 노드 형식에 대 한 기본 구성을 사용 하 여 상태 프로브를 구성 합니다. 응용 프로그램이 nodetype에 배포 되 면 상태 프로브/상태 확장 포트를 변경 하 여 응용 프로그램 상태를 모니터링할 수 있습니다.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>네트워킹 요구 사항
### <a name="public-ip-and-load-balancer-resource"></a>공용 IP 및 Load Balancer 리소스
가상 머신 확장 집합 리소스에서 100 개 이상의 Vm으로 크기를 조정할 수 있도록 하려면 해당 가상 머신 확장 집합에서 참조 하는 부하 분산 장치 및 IP 리소스가 둘 다 *표준* SKU를 사용 해야 합니다. SKU 속성을 사용 하지 않고 부하 분산 장치 또는 IP 리소스를 만들면 100 개 이상의 Vm으로의 크기 조정을 지원 하지 않는 기본 SKU가 만들어집니다. 표준 SKU 부하 분산 장치는 기본적으로 외부의 모든 트래픽을 차단 합니다. 외부 트래픽을 허용 하려면 서브넷에 NSG를 배포 해야 합니다.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> 공용 IP 및 부하 분산 장치 리소스에서 SKU의 내부 변경 작업을 수행할 수 없습니다. 기본 SKU가 있는 기존 리소스에서 마이그레이션하는 경우이 문서의 마이그레이션 섹션을 참조 하세요.

### <a name="virtual-machine-scale-set-nat-rules"></a>가상 머신 확장 집합 NAT 규칙
부하 분산 장치 인바운드 NAT 규칙은 가상 머신 확장 집합의 NAT 풀과 일치 해야 합니다. 각 가상 머신 확장 집합에는 고유한 인바운드 NAT 풀이 있어야 합니다.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>표준 SKU Load Balancer 아웃 바운드 규칙
표준 Load Balancer 및 표준 공용 IP는 기본 Sku 사용에 비해 새로운 기능 및 아웃 바운드 연결에 대 한 다양 한 동작을 소개 합니다. 표준 SKU로 작업하는 경우 아웃바운드 연결을 하려는 경우 표준 공용 IP 주소 또는 표준 공용 Load Balancer를 사용하여 명시적으로 정의해야 합니다. 자세한 내용은 [아웃 바운드 연결](../load-balancer/load-balancer-outbound-connections.md) 및 [Azure 표준 Load Balancer](../load-balancer/load-balancer-overview.md)를 참조 하세요.

>[!NOTE]
> 표준 템플릿은 기본적으로 모든 아웃 바운드 트래픽을 허용 하는 NSG를 참조 합니다. 인바운드 트래픽은 Service Fabric 관리 작업에 필요한 포트로 제한 됩니다. NSG 규칙은 요구 사항에 맞게 수정할 수 있습니다.

>[!NOTE]
> 표준 SKU SLB를 사용 하는 Service Fabric 클러스터는 각 노드 형식에 443 포트에서 아웃 바운드 트래픽을 허용 하는 규칙이 있는지 확인 해야 합니다. 이는 클러스터 설정을 완료 하는 데 필요 하며, 이러한 규칙이 없는 모든 배포는 실패 합니다.



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>기본 SKU Load Balancer 및 기본 SKU IP를 사용 하 여 클러스터에서 상태 비저장 노드 유형을 사용 하도록 마이그레이션
모든 마이그레이션 시나리오에 대해 새 상태 비저장 전용 노드 유형을 추가 해야 합니다. 기존 노드 형식은 상태 비저장 전용으로 마이그레이션할 수 없습니다.

기본 SKU를 사용 하 여 Load Balancer 및 IP를 사용 하는 클러스터를 마이그레이션하려면 먼저 표준 SKU를 사용 하 여 완전히 새로운 Load Balancer 및 IP 리소스를 만들어야 합니다. 이러한 리소스는 내부에서 업데이트할 수 없습니다.

새 LB 및 IP는 사용할 새 상태 비저장 노드 형식에서 참조 되어야 합니다. 위의 예제에서는 상태 비저장 노드 형식에 사용할 새 가상 머신 확장 집합 리소스를 추가 합니다. 이러한 가상 머신 확장 집합은 새로 만든 LB 및 IP를 참조 하 고 Service Fabric 클러스터 리소스에서 상태 비저장 노드 유형으로 표시 됩니다.

시작 하려면 기존 리소스 관리자 템플릿에 새 리소스를 추가 해야 합니다. 해당 리소스는 다음과 같습니다.
* 표준 SKU를 사용 하는 공용 IP 리소스입니다.
* 표준 SKU를 사용 하는 Load Balancer 리소스입니다.
* 가상 머신 확장 집합을 배포 하는 서브넷에서 참조 하는 NSG입니다.

리소스 배포를 완료 한 후에는 원본 클러스터에서 제거 하려는 노드 형식의 노드를 사용 하지 않도록 설정할 수 있습니다.


## <a name="next-steps"></a>다음 단계 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [노드 유형과 가상 머신 확장 집합](service-fabric-cluster-nodetypes.md)

