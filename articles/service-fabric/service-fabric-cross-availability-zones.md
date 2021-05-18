---
title: 가용성 영역에서 클러스터 배포
description: 가용성 영역에서 Azure Service Fabric 클러스터를 만드는 방법을 알아봅니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: a49fd6f97a9130fa0369d2a36cdc38e59613afc1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544386"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>가용성 영역에서 Azure Service Fabric 클러스터 배포
Azure의 가용성 영역은 데이터 센터 오류에서 애플리케이션 및 데이터를 보호하는 고가용성 기능입니다. 가용성 영역은 Azure 지역 내에서 독립적인 전원, 냉각 및 네트워킹을 갖춘 고유한 물리적 위치입니다.

Service Fabric은 특정 영역에 고정된 노드 형식을 배포하여 가용성 영역에 걸쳐 있는 클러스터를 지원합니다. 이렇게 하면 애플리케이션의 고가용성을 보장합니다. Azure 가용성 영역을 모든 지역에서 사용할 수 있는 것은 아닙니다. 자세한 내용은 [Azure 가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

사용 가능한 샘플 템플릿: [Service Fabric 교차 가용성 영역 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>가용성 영역에 걸쳐 있는 Azure Service Fabric 클러스터의 주 노드 형식에 권장되는 토폴로지
가용성 영역에 분산된 Service Fabric 클러스터는 클러스터 상태의 고 가용성을 보장합니다. 영역에서 Service Fabric 클러스터를 확장하려면 지역에서 지원하는 각 가용성 영역에 주 노드 형식을 만들어야 합니다. 이렇게 하면 각 주 노드 형식에 시드 노드가 균등하게 분산됩니다.

주 노드 형식에 권장되는 토폴로지에는 아래에 설명된 리소스가 필요합니다.

* Platinum으로 설정된 클러스터 안정성 수준
* 주로 표시된 세 가지 노드 형식
    * 각 노드 형식은 다른 영역에 있는 자체 가상 머신 확장 집합에 매핑되어야 합니다.
    * 각 가상 머신 확장 집합에는 5개 이상의 노드(실버 내구성)가 있어야 합니다.
* 표준 SKU를 사용하는 단일 공용 IP 리소스
* 표준 SKU를 사용하는 단일 부하 분산 장치 리소스
* Virtual Machine Scale Sets를 배포하는 서브넷에서 참조하는 NSG

>[!NOTE]
> Service Fabric은 영역에 걸쳐 있는 단일 가상 머신 확장 집합을 지원하지 않으므로 가상 머신 확장 집합 단일 배치 그룹 속성을 true로 설정해야 합니다.

Azure Service Fabric 가용성 영역 아키텍처를 보여 주는 다이어그램 ![Azure Service Fabric 가용성 영역 아키텍처를 보여 주는 다이어그램][sf-architecture]

영역에 걸쳐 있는 가상 머신 확장 집합의 FD/UD 형식을 설명하는 샘플 노드 목록

 ![영역에 걸쳐 있는 가상 머신 확장 집합의 FD/UD 형식을 설명하는 샘플 노드 목록][sf-multi-az-nodes]

**영역에서 서비스 복제본 배포**: 서비스가 영역에 걸쳐 있는 nodeTypes에 배포되면 복제본이 별도의 영역에 위치하도록 배치됩니다. 이는 각 nodeTypes에 있는 노드의 장애 도메인이 영역 정보(예: FD = fd:/zone1/1 등)로 구성되므로 보장됩니다. 예를 들어 5개의 복제본 또는 서비스 인스턴스의 경우 배포는 2-2-1이 되고 런타임은 AZ 전체에 동일한 배포를 보장하려고 합니다.

**사용자 서비스 복제본 구성**: 교차 가용성 영역 nodeTypes에 배포된 상태 저장 사용자 서비스는 복제본 수는 대상 = 9, 최소 = 5 구성으로 구성되어야 합니다. 이 구성은 6개의 복제본이 다른 두 영역에서 계속 작동하므로 한 영역이 중단된 경우에도 서비스가 작동하는 데 도움이 됩니다. 이러한 시나리오에서는 애플리케이션 업그레이드도 진행됩니다.

**클러스터 ReliabilityLevel**: 클러스터의 시드 노드 수와 시스템 서비스의 복제본 크기를 정의합니다. 교차 가용성 영역 설정에는 영역 복원력을 사용하기 위해 영역에 분산되어 있는 노드 수가 더 많으므로 안정성 값이 높을수록 노드에 더 많은 시드 노드와 시스템 서비스 복제본이 있고 영역 간에 균등하게 분산됩니다. 영역 장애가 발생하더라도 클러스터 및 시스템 서비스는 영향을 받지 않습니다. "ReliabilityLevel = Platinum"은 클러스터의 영역에 걸쳐 9개의 시드 노드(각 영역에 3개의 시드)가 있음을 보장하므로 교차 가용성 영역 설정에 권장됩니다.

**영역 중단 시나리오**: 영역이 중단되면 해당 영역의 모든 노드가 중단된 것으로 나타납니다. 이러한 노드의 서비스 복제본도 중단됩니다. 다른 영역에 복제본이 있으므로 서비스는 작동 중인 영역으로 장애 조치(failover)되는 주 복제본을 사용하여 계속 응답합니다. 대상 복제본 개수가 아직 달성되지 않아서 VM 개수가 최소 대상 복제본 크기보다 더 크기 때문에 서비스는 경고 상태로 표시됩니다. 이후에 Service Fabric 부하 분산 장치는 구성된 대상 복제본 개수와 일치하도록 작업 영역에 복제본을 가져옵니다. 이 시점에서 서비스는 정상으로 표시됩니다. 중단된 영역이 복구되면 부하 분산 장치는 다시 모든 서비스 복제본을 모든 영역에 균등하게 분산시킵니다.

## <a name="networking-requirements"></a>네트워킹 요구 사항
### <a name="public-ip-and-load-balancer-resource"></a>공용 IP 및 부하 분산 장치 리소스
가상 머신 확장 집합 리소스에서 영역 속성을 사용하려면 해당 가상 머신 확장 집합에서 참조하는 부하 분산 장치와 IP 리소스가 모두 *표준* SKU를 사용해야 합니다. SKU 속성 없이 부하 분산 장치 또는 IP 리소스를 만들면 가용성 영역을 지원하지 않는 기본 SKU가 만들어집니다. 표준 SKU 부하 분산 장치는 기본적으로 외부의 모든 트래픽을 차단합니다. 외부 트래픽을 허용하려면 서브넷에 NSG를 배포해야 합니다.

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
> 공용 IP 및 부하 분산 장치 리소스에서 SKU 내부 변경을 수행할 수 없습니다. 기본 SKU가 있는 기존 리소스에서 마이그레이션하는 경우 이 문서의 마이그레이션 섹션을 참조하세요.

### <a name="virtual-machine-scale-set-nat-rules"></a>가상 머신 확장 집합 NAT 규칙
부하 분산 장치 인바운드 NAT 규칙은 가상 머신 확장 집합의 NAT 풀과 일치해야 합니다. 각 가상 머신 확장 집합에는 고유한 인바운드 NAT 풀이 있어야 합니다.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>표준 SKU Load Balancer 아웃바운드 규칙
표준 Load Balancer 및 표준 공용 IP는 기본 SKU 사용과 비교할 때 아웃바운드 연결에 새로운 기능 및 서로 다른 동작을 도입합니다. 표준 SKU로 작업하는 경우 아웃바운드 연결을 하려는 경우 표준 공용 IP 주소 또는 표준 공용 Load Balancer를 사용하여 명시적으로 정의해야 합니다. 자세한 내용은 [아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md) 및 [Azure 표준 Load Balancer](../load-balancer/load-balancer-overview.md)를 참조하세요.

>[!NOTE]
> 표준 템플릿은 기본적으로 모든 아웃바운드 트래픽을 허용하는 NSG를 참조합니다. 인바운드 트래픽은 Service Fabric 관리 작업에 필요한 포트로 제한됩니다. NSG 규칙은 요구 사항에 맞게 수정할 수 있습니다.

>[!NOTE]
> 표준 SKU SLB를 사용하는 Service Fabric 클러스터는 각 노드 형식에 포트 443에서 아웃바운드 트래픽을 허용하는 규칙이 있는지 확인해야 합니다. 이는 클러스터 설정을 완료하는 데 필요하며, 이러한 규칙이 없는 모든 배포는 실패합니다.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 영역 사용
영역을 사용하도록 설정하려면 가상 머신 확장 집합에서 가상 머신 확장 집합 리소스에 다음 세 가지 값을 포함해야 합니다.

* 첫 번째 값은 가상 머신 확장 집합을 배포할 가용성 영역을 지정하는 **zones** 속성입니다.
* 두 번째 값은 true로 설정해야 하는 "singlePlacementGroup" 속성입니다.
* 세 번째 값은 Service Fabric 가상 머신 확장 집합 확장의 "faultDomainOverride" 속성입니다. 이 속성의 값에는 이 가상 머신 확장 집합이 배치될 영역만 포함되어야 합니다. 예: "faultDomainOverride": "az1" Azure Service Fabric 클러스터에는 지역 간 지원이 없기 때문에 모든 가상 머신 확장 집합 리소스를 동일한 지역에 배치해야 합니다.

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
        "publisher": "Microsoft.Azure.ServiceFabric",
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
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric 클러스터 리소스에서 여러 주 노드 형식 사용
클러스터 리소스에서 하나 이상의 노드 형식을 주로 설정하려면 "isPrimary" 속성을 "true"로 설정합니다. 가용성 영역에서 Service Fabric 클러스터를 배포하는 경우 고유한 영역에 3개의 노드 형식이 있어야 합니다.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>기본 SKU 부하 분산 장치 및 기본 SKU IP를 사용하는 클러스터에서 가용성 영역을 사용하도록 마이그레이션
기본 SKU와 함께 부하 분산 장치 및 IP를 사용하던 클러스터를 마이그레이션하려면 먼저 표준 SKU를 사용하여 완전히 새로운 부하 분산 장치 및 IP 리소스를 만들어야 합니다. 이러한 리소스는 내부에서 업데이트할 수 없습니다.

새 LB 및 IP는 사용하려는 새 교차 가용성 영역 노드 형식에서 참조되어야 합니다. 위의 예에서 세 개의 새로운 가상 머신 확장 집합 리소스가 영역 1, 2 및 3에 추가되었습니다. 이러한 Virtual Machine Scale Sets는 새로 생성된 LB 및 IP를 참조하고 Service Fabric 클러스터 리소스에서 주 노드 형식으로 표시됩니다.

시작하려면 기존 Resource Manager 템플릿에 새 리소스를 추가해야 합니다. 해당 리소스는 다음과 같습니다.
* 표준 SKU를 사용하는 공용 IP 리소스
* 표준 SKU를 사용하는 부하 분산 장치 리소스
* Virtual Machine Scale Sets를 배포하는 서브넷에서 참조하는 NSG
* 주로 표시된 세 가지 노드 형식
    * 각 노드 형식은 다른 영역에 있는 자체 가상 머신 확장 집합에 매핑되어야 합니다.
    * 각 가상 머신 확장 집합에는 5개 이상의 노드(실버 내구성)가 있어야 합니다.

이러한 리소스의 예는 [샘플 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)에서 찾을 수 있습니다.

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

리소스 배포가 완료되면 원래 클러스터에서 주 노드 형식의 노드를 사용하지 않도록 설정할 수 있습니다. 노드를 사용하지 않도록 설정하면 시스템 서비스는 위 단계에서 배포된 새 주 노드 형식으로 마이그레이션합니다.

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

노드를 모두 사용하지 않도록 설정하면 시스템 서비스가 영역에 분산된 주 노드 형식에서 실행됩니다. 그런 다음 클러스터에서 사용하지 않도록 설정된 노드를 제거할 수 있습니다. 노드가 제거되면 원래 IP, 부하 분산 장치 및 가상 머신 확장 집합 리소스를 제거할 수 있습니다.

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

그런 다음 배포한 Resource Manager 템플릿에서 이러한 리소스에 대한 참조를 제거해야 합니다.

최종 단계에는 DNS 이름 및 공용 IP 업데이트가 포함됩니다.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(미리 보기) 단일 가상 머신 확장 집합에서 여러 가용성 영역 사용

앞에서 언급한 솔루션은 AZ당 하나의 nodeType을 사용합니다. 다음 솔루션을 통해 사용자는 동일한 nodeType에 3개의 AZ를 배포할 수 있습니다.

**이 기능은 현재 미리 보기 상태이므로 현재 프로덕션 시나리오에서는 지원되지 않습니다.**

전체 샘플 템플릿이 [여기](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)에 있습니다.

![Azure Service Fabric 가용성 영역 아키텍처][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 영역 구성
가상 머신 확장 집합에서 영역을 사용하도록 설정하려면 가상 머신 확장 집합 리소스에 다음 세 가지 값을 포함해야 합니다.

* 첫 번째 값은 가상 머신 확장 집합에 있는 가용성 영역을 지정하는 **zones** 속성입니다.
* 두 번째 값은 true로 설정해야 하는 "singlePlacementGroup" 속성입니다. **3개의 AZ에 걸쳐 있는 확장 집합은 "singlePlacementGroup = true"에서도 최대 300개의 VM으로 확장할 수 있습니다.**
* 세 번째 값은 엄격한 영역 균형을 보장하는 "zoneBalance"입니다. 이는 "True"여야 합니다. 이렇게 하면 영역 간 VM 배포가 불균형하지 않도록 하여 영역 중 하나가 중단될 때 다른 두 영역에는 클러스터를 중단 없이 계속 실행되도록 하는 충분한 VM이 있습니다. 불균형 VM 배포가 있는 클러스터는 해당 영역에 대부분의 VM이 있을 수 있으므로 영역 중단 시나리오에서 사용하지 못하게 될 수 있습니다. 영역에서 VM 분산이 불균형하면 서비스 배치 관련 문제 및 인프라 업데이트가 중단될 수 있습니다. [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)에 대해 읽어 보세요.
* FaultDomain 및 UpgradeDomain 재정의는 구성할 필요가 없습니다.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Service Fabric 클러스터에는 주 nodeType이 하나 이상 있어야 합니다. 주 nodeType의 DurabilityLevel은 실버 이상이어야 합니다.**
> * AZ 스패닝 가상 머신 확장 집합은 durabilityLevel에 관계없이 3개 이상의 가용성 영역으로 구성되어야 합니다.
> * 실버 내구성(또는 그 이상)이 있는 AZ 스패닝 가상 머신 확장 집합은 15개 이상의 VM이 있어야 합니다.
> * 브론즈 내구성이 있는 AZ 스패닝 가상 머신 확장 집합에는 6개 이상의 VM이 있어야 합니다.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Service Fabric nodeType에서 여러 영역에 대한 지원 활성화
여러 가용성 영역을 지원하려면 Service Fabric nodeType을 사용 하도록 설정해야 합니다.

* 첫 번째 값은 **multipleAvailabilityZones** 이며 nodeType에 대해 true로 설정해야 합니다.
* 두 번째 값은 **sfZonalUpgradeMode** 이며 선택 사항입니다. 여러 AZ를 사용하는 nodetype이 클러스터에 이미 있는 경우 이 속성을 수정할 수 없습니다.
  속성은 업그레이드 도메인에서 VM의 논리적 그룹화를 제어합니다.
  **값이 "Parallel"로 설정된 경우:** nodetype 아래의 VM은 5개 UD의 영역 정보를 무시하고 UD로 그룹화됩니다. 이로 인해 모든 영역에서 UD0이 동시에 업그레이드됩니다. 이 배포 모드는 업그레이드 속도가 더 빠르지만 업데이트를 한 번에 한 영역에만 적용해야 한다는 SDP 지침에 위배되므로 권장되지 않습니다.
  **값이 생략되거나 "Hierarchical"로 설정된 경우:** VM은 최대 15개의 UD에 영역 배포를 반영하도록 그룹화됩니다. 각 3개 영역에는 5개의 UD가 있습니다. 이렇게 하면 첫 번째 영역 내에서 5개의 UD를 완료한 후에만 다음 영역으로 이동하고 15개의 UD(3개 영역, 5개의 UD)에 걸쳐 천천히 업데이트가 영역별로 진행됩니다. 이는 클러스터 및 사용자 애플리케이션의 관점에서 더 안전합니다.
  이 속성은 ServiceFabric 애플리케이션 및 코드 업그레이드에 대한 업그레이드 동작만 정의합니다. 기본 가상 머신 확장 집합 업그레이드는 모든 AZ에서 여전히 병렬입니다.
  이 속성은 여러 영역이 사용하도록 설정되지 않은 노드 형식의 UD 배포에 영향을 주지 않습니다.
* 세 번째 값은 **vmssZonalUpgradeMode = Parallel** 입니다. 여러 AZ를 사용하는 nodeType이 추가된 경우 클러스터에서 구성할 *필수* 속성입니다. 이 속성은 모든 AZ에서 동시에 발생하는 가상 머신 확장 집합 업데이트에 대한 업그레이드 모드를 정의합니다.
  현재 이 속성은 병렬으로만 설정할 수 있습니다.
* Service Fabric 클러스터 리소스 apiVersion은 "2020-12-01-preview" 이상이어야 합니다.
* 클러스터 코드 버전은 "7.2.445" 이상이어야 합니다.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * 공용 IP 및 부하 분산 장치 리소스는 문서의 앞부분에서 설명한 대로 표준 SKU를 사용해야 합니다.
> * nodeType의 "multipleAvailabilityZones" 속성은 nodeType을 만들 때만 정의할 수 있으며 나중에 수정할 수 없습니다. 따라서 이 속성으로 기존 nodeTypes를 구성할 수 없습니다.
> * "sfZonalUpgradeMode"가 생략되거나 "Hierarchical"로 설정된 경우 클러스터에 업그레이드 도메인이 더 있으므로 클러스터 및 애플리케이션 배포 속도가 느려집니다. 업그레이드 정책 제한 시간을 올바르게 조정하여 15개의 업그레이드 도메인에 대한 업그레이드 기간을 통합하는 것이 중요합니다. 배포가 Azure Resource Serbice 배포 제한 시간인 12시간을 초과하지 않도록 앱과 클러스터 모두에 대한 업그레이드 정책을 업데이트해야 합니다. 즉, 배포는 15개의 UD에 대해 12시간 이상(예: UD당 40분 이상 걸리지 않아야 함) 걸리지 않아야 한다는 것을 의미합니다.
> * 클러스터가 한 영역 중단 시나리오에서 계속 사용할 수 있도록 클러스터 **reliabilityLevel = Platinum** 을 설정합니다.

>[!NOTE]
> 모범 사례로 sfZonalUpgradeMode를 Hierarchical로 설정하거나 생략하는 것이 좋습니다. 배포는 더 적은 양의 복제본 및/또는 인스턴스에 영향을 주는 VM의 영역 배포를 따르므로 더 안전하게 만들 수 있습니다.
> 배포 속도가 중요하거나 여러 AZ를 사용하는 노드 형식에서 상태 비저장 워크로드만 실행되는 경우 sfZonalUpgradeMode를 Parallel로 설정합니다. 그러면 모든 AZ에서 UD 워크가 병렬로 발생합니다.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>여러 가용성 영역을 사용하는 노드 형식으로 마이그레이션
모든 마이그레이션 시나리오의 경우 여러 가용성 영역이 지원되는 새 nodeType을 추가해야 합니다. 기존 nodeType은 여러 영역을 지원하도록 마이그레이션할 수 없습니다.
[여기](./service-fabric-scale-up-primary-node-type.md) 문서에서는 새 nodeType을 추가하고 IP 및 LB 리소스와 같은 새 nodeType에 필요한 다른 리소스를 추가하는 자세한 단계를 설명합니다. 또한 여러 가용성 영역을 사용하는 nodeType이 클러스터에 추가된 후 기존 nodeType을 사용 중지하는 방법에 대해서도 설명합니다.

* 기본 LB 및 IP 리소스를 사용하는 nodeType에서 마이그레이션: AZ당 하나의 노드 형식이 있는 솔루션의 경우 이미 [여기](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip)에 설명되어 있습니다. 
    새 노드 형식의 경우 유일한 차이점은 AZ당 각각 1개가 아닌 모든 AZ에 대해 1개의 가상 머신 확장 집합과 1개의 nodetype만 있다는 점입니다.
* NSG와 함께 표준 SKU LB 및 IP 리소스를 사용하는 nodeType에서 마이그레이션: 새 LB, IP 및 NSG 리소스를 추가할 필요가 없으며 새 nodeType에서 동일한 리소스를 다시 사용할 수 있다는 점을 제외하고 위에서 설명한 것과 동일한 절차를 따릅니다.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png