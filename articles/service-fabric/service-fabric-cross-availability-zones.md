---
title: 가용성 영역에서 Azure Service Fabric 클러스터 배포 | Microsoft Docs
description: 가용성 영역에서 Azure Service Fabric 클러스터를 만드는 방법에 알아봅니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077457"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>가용성 영역에서 Azure Service Fabric 클러스터 배포
Azure에서 가용성 영역은 고가용성 데이터 센터 오류 로부터 응용 프로그램 및 데이터를 보호 하는 기능입니다. 가용성 영역은 냉각 및 네트워킹 Azure 지역 내의 독립 된 전원, 장착 하는 고유한 실제 위치입니다.

Service Fabric은 고정 된 노드는 특정 영역에 배포 하 여 가용성 영역에 걸쳐 있는 클러스터를 지원 합니다. 이렇게 하면 응용 프로그램의 고가용성입니다. Azure Availability Zones는 선택 영역 에서만 사용할 수 있습니다. 자세한 내용은 [Azure 가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)합니다.

샘플 템플릿을 사용할 수 있습니다. [Service Fabric 간 가용성 영역 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>가용성 영역에 걸쳐 Azure Service Fabric 클러스터의 주 노드 형식에 대 한 토폴로지를 권장 합니다.
가용성 영역에 분산 하는 Service Fabric 클러스터는 고가용성 클러스터 상태를 확인 합니다. 영역에서 Service Fabric 클러스터에 걸쳐, 지역에서 지원 되는 각 가용성 영역에서 주 노드 유형을 만들어야 합니다. 이 분산 시드 노드 균등 하 게 각 주 노드 형식입니다.

주 노드 형식에 대 한 권장 되는 토폴로지는 아래에 설명 된 리소스를 필요 합니다.

* 클러스터 안정성 수준을 플래티넘으로 설정 합니다.
* 노드는 기본으로 표시 합니다.
    * 각 노드 유형은 서로 다른 시간대에 있는 가상 머신 확장 집합 자체에 매핑해야 합니다.
    * 각 가상 머신 확장 집합에 최소 5 개 노드 (실버 내구성) 있어야 합니다.
* 단일 공용 IP 리소스를 표준 SKU를 사용 합니다.
* 표준 SKU를 사용 하 여 단일 부하 분산 장치 리소스입니다.
* 가상 머신 확장을 배포 하는 서브넷에서 참조 하는 NSG를 설정 합니다.

>[!NOTE]
> 가상 머신 확장 집합을 단일 배치 그룹 속성을 Service Fabric 영역을 포함 하는 단일 가상 머신 확장 집합을 지원 하지 않으므로 true로 설정 해야 합니다.

 ![Azure 서비스 패브릭 가용성 영역 아키텍처][sf-architecture]

## <a name="networking-requirements"></a>네트워킹 요구 사항
### <a name="public-ip-and-load-balancer-resource"></a>공용 IP 및 부하 분산 장치 리소스
영역 가상 머신 확장에 대 한 속성 설정 리소스, 부하 분산 장치 및 해당 가상 머신 확장 집합에서 참조 하는 IP 리소스를 사용 하도록 설정 하려면 둘 다를 사용 해야 합니다는 *표준* SKU입니다. 부하 분산 장치를 만들거나 SKU 속성이 없는 IP 리소스 가용성 영역을 지원 하지 않는 기본 SKU를 만듭니다. 표준 SKU 부하 분산 장치는 기본적으로 외부에서 모든 트래픽을 차단 됩니다. 를 외부 트래픽을 허용 하려면 NSG은 서브넷에 배포 되어야 합니다.

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
```

```json
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
> 공용 IP 및 부하 분산 장치 리소스 SKU의 전체 변경 작업을 수행 하는 것이 불가능 합니다. 기본 SKU가 있는 기존 리소스에서로 마이그레이션하는 경우이 문서의 마이그레이션 섹션을 참조 합니다.

### <a name="virtual-machine-scale-set-nat-rules"></a>가상 머신 확장 집합 NAT 규칙
부하 분산 장치 인바운드 NAT 규칙에는 가상 머신 확장 집합에서 NAT 풀와 일치 해야 합니다. 각 가상 머신 확장 집합에는 고유한 인바운드 NAT 풀이 있어야 합니다.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>표준 SKU 부하 분산 장치 아웃 바운드 규칙
표준 Load Balancer 및 표준 공용 IP 기본 Sku를 사용 하 여 비교할 때 아웃 바운드 연결에 새 기능 및 서로 다른 동작을 소개 합니다. 표준 SKU로 작업하는 경우 아웃바운드 연결을 하려는 경우 표준 공용 IP 주소 또는 표준 공용 Load Balancer를 사용하여 명시적으로 정의해야 합니다. 자세한 내용은 [아웃 바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) 하 고 [Azure 표준 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)합니다.

>[!NOTE]
> 표준 템플릿이 기본적으로 모든 아웃 바운드 트래픽을 허용 하는 NSG를 참조 합니다. 인바운드 트래픽은 Service Fabric 관리 작업에 필요한 포트에 제한 됩니다. NSG 규칙은 요구 사항에 맞게 수정할 수 있습니다.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>가상 머신 확장에 대해 영역 집합
영역을 사용 하도록 가상 머신 확장을 설정 하면 가상 머신 확장 집합 리소스에 다음 세 가지 값을 포함 해야 합니다.

* 첫 번째 값을 **영역** 가상 머신 확장 집합을 배포 하는 가용성 영역을 지정 하는 속성입니다.
* 두 번째 값이 설정 되어 있어야 하는 "singlePlacementGroup" 속성을 true로 합니다.
* 세 번째 값은 Service Fabric 가상 머신 확장 집합 확장의 "faultDomainOverride" 속성입니다. 이 속성의 값 영역과 영역 가상 머신 확장 집합에이 배치를 포함 해야 합니다. 예: "faultDomainOverride": "eastus/az1" 모든 가상 머신 확장 집합 리소스 Azure Service Fabric 클러스터 간 지역 지원 하지 않으므로 동일한 지역에 배치 해야 합니다.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric 클러스터 리소스의 여러 주 노드 형식 사용
하나 이상의 노드 형식에는 클러스터 리소스의 기본으로 설정 하려면 "true"로 "isPrimary" 속성을 설정 합니다. 가용성 영역에서 Service Fabric 클러스터를 배포할 때는 개별 영역에 노드는 있어야 합니다.

```json
{
    "reliabilityLevel": "Platinum",
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
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>기본 SKU 부하 분산 장치 및 기본 SKU IP를 사용 하 여 클러스터에서 가용성 영역을 사용 하 여 마이그레이션
사용한 부하 분산 장치 및 IP는 기본 SKU를 사용 하 여, 클러스터를 마이그레이션하려면 먼저 표준 SKU를 사용 하 여 완전히 새로운 Load Balancer 및 IP 리소스를 만들어야 합니다. 이러한 리소스의 현재 위치를 업데이트 하는 것이 불가능 합니다.

사용 하려는 않은 새 크로스 가용성 영역 노드 형식에 새 IP 및 LB 참조 해야 합니다. 세 개의 새 가상 머신 확장 위의 예에서 집합 리소스는 영역 1, 2 및 3에에서 추가 되었습니다. 이러한 가상 머신 크기는 새로 만든 LB 및 IP 참조를 설정 하 고 Service Fabric 클러스터 리소스의 주 노드 형식으로 표시 됩니다.

시작 하려면 기존 Resource Manager 템플릿에 새 리소스를 추가 해야 합니다. 이러한 리소스에는 다음이 포함 됩니다.
* 표준 SKU를 사용 하 여 공용 IP 리소스입니다.
* 표준 SKU를 사용 하 여 부하 분산 장치 리소스입니다.
* 가상 머신 확장을 배포 하는 서브넷에서 참조 하는 NSG를 설정 합니다.
* 노드는 기본으로 표시 합니다.
    * 각 노드 유형은 서로 다른 시간대에 있는 가상 머신 확장 집합 자체에 매핑해야 합니다.
    * 각 가상 머신 확장 집합에 최소 5 개 노드 (실버 내구성) 있어야 합니다.

이러한 리소스의 예로에서 확인할 수 있습니다 합니다 [샘플 템플릿은](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)합니다.

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

리소스 배포를 완료 한 후에 원래 클러스터에서 주 노드 형식의 노드를 사용 하지 않으려면 시작할 수 있습니다. 노드 함으로 시스템 서비스는 위의 단계에서 배포 되는 새 주 노드 형식으로 마이그레이션합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

비활성화 되 면 노드는 모든 시스템 서비스 영역에 분산 되어 있는 주 노드 형식에서 실행 됩니다. 그런 다음 클러스터에서 사용할 수 없는 노드를 제거할 수 있습니다. 노드 제거 되 면 원본 IP, 부하 분산 장치를 제거 하 고 가상 머신 확장 집합 리소스입니다.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

그런 다음 배포 했다고 Resource Manager 템플릿에서 이러한 리소스에 대 한 참조를 제거 해야 합니다.

마지막 단계를 DNS 이름 및 공용 IP를 업데이트 해야 합니다.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
