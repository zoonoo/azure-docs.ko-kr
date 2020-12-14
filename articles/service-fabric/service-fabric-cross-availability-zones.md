---
title: 가용성 영역에서 클러스터 배포
description: 가용성 영역에서 Azure Service Fabric 클러스터를 만드는 방법에 대해 알아봅니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 73a3be62e57991b63525372f008e15d8e4f36a74
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401732"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>가용성 영역에서 Azure Service Fabric 클러스터 배포
Azure의 가용성 영역는 데이터 센터 오류 로부터 응용 프로그램 및 데이터를 보호 하는 고가용성 제품입니다. 가용성 영역은 Azure 지역 내에서 독립적인 전원, 냉각 및 네트워킹을 갖춘 고유한 물리적 위치입니다.

Service Fabric는 특정 영역에 고정 된 노드 유형을 배포 하 여 가용성 영역에 걸쳐 있는 클러스터를 지원 합니다. 이렇게 하면 응용 프로그램의 고가용성을 보장 합니다. Azure 가용성 영역는 선택 지역 에서만 사용할 수 있습니다. 자세한 내용은 [Azure 가용성 영역 개요](../availability-zones/az-overview.md)를 참조 하세요.

샘플 템플릿을 사용할 수 있습니다. [Service Fabric 상호 가용성 영역 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>가용성 영역 전체에 걸쳐 있는 Azure Service Fabric 클러스터의 주 노드 형식에 대 한 권장 토폴로지
가용성 영역 간에 분산 된 Service Fabric 클러스터는 클러스터 상태의 고가용성을 보장 합니다. 영역에서 Service Fabric 클러스터를 확장 하려면 해당 지역에서 지 원하는 각 가용성 영역에서 주 노드 유형을 만들어야 합니다. 이렇게 하면 각 주 노드 형식에 대해 초기값 노드가 균등 하 게 분산 됩니다.

주 노드 형식에 권장 되는 토폴로지에서는 아래에 설명 된 리소스가 필요 합니다.

* 클러스터 안정성 수준이 Platinum으로 설정 되어 있습니다.
* 기본으로 표시 된 세 가지 노드 형식입니다.
    * 각 노드 형식은 다른 영역에 있는 자체 가상 머신 확장 집합에 매핑되어야 합니다.
    * 각 가상 머신 확장 집합에는 5 개 이상의 노드 (실버 내구성)가 있어야 합니다.
* 표준 SKU를 사용 하는 단일 공용 IP 리소스입니다.
* 표준 SKU를 사용 하는 단일 Load Balancer 리소스입니다.
* 가상 머신 확장 집합을 배포 하는 서브넷에서 참조 하는 NSG입니다.

>[!NOTE]
> Service Fabric는 영역에 걸쳐 있는 단일 가상 머신 확장 집합을 지원 하지 않으므로 가상 머신 확장 집합 단일 배치 그룹 속성을 true로 설정 해야 합니다.

 ![Azure Service Fabric 가용성 영역 아키텍처][sf-architecture]

## <a name="networking-requirements"></a>네트워킹 요구 사항
### <a name="public-ip-and-load-balancer-resource"></a>공용 IP 및 Load Balancer 리소스
가상 머신 확장 집합 리소스에서 영역 속성을 사용 하도록 설정 하려면 해당 가상 머신 확장 집합에서 참조 하는 부하 분산 장치 및 IP 리소스가 둘 다 *표준* SKU를 사용 해야 합니다. SKU 속성을 사용 하지 않고 부하 분산 장치 또는 IP 리소스를 만들면 가용성 영역를 지원 하지 않는 기본 SKU가 만들어집니다. 표준 SKU 부하 분산 장치는 기본적으로 외부의 모든 트래픽을 차단 합니다. 외부 트래픽을 허용 하려면 서브넷에 NSG를 배포 해야 합니다.

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


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 영역 사용
영역을 사용 하도록 설정 하려면 가상 머신 확장 집합에서 가상 머신 확장 집합 리소스에 다음 세 가지 값을 포함 해야 합니다.

* 첫 번째 값은 가상 머신 확장 집합을 배포할 가용성 영역을 지정 하는 **zones** 속성입니다.
* 두 번째 값은 true로 설정 해야 하는 "Singleto Ementgroup" 속성입니다.
* 세 번째 값은 Service Fabric 가상 머신 확장 집합 확장의 "faultDomainOverride" 속성입니다. 이 속성의 값은이 가상 머신 확장 집합이 배치 될 영역만 포함 해야 합니다. 예: "faultDomainOverride": "az1" Azure Service Fabric 클러스터에는 지역 간 지원이 없기 때문에 모든 가상 머신 확장 집합 리소스를 동일한 지역에 배치 해야 합니다.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric 클러스터 리소스에서 여러 주 노드 유형 사용
클러스터 리소스에서 하나 이상의 노드 형식을 주로 설정 하려면 "isPrimary" 속성을 "true"로 설정 합니다. 가용성 영역에서 Service Fabric 클러스터를 배포 하는 경우 고유한 영역에 3 개의 노드 형식이 있어야 합니다.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>기본 SKU Load Balancer 및 기본 SKU IP를 사용 하 여 클러스터에서 가용성 영역 사용으로 마이그레이션
기본 SKU를 사용 하 여 Load Balancer 및 IP를 사용 하는 클러스터를 마이그레이션하려면 먼저 표준 SKU를 사용 하 여 완전히 새로운 Load Balancer 및 IP 리소스를 만들어야 합니다. 이러한 리소스는 내부에서 업데이트할 수 없습니다.

새 LB 및 IP는 사용 하려는 새로운 교차 가용성 영역 노드 유형에 서 참조 되어야 합니다. 위의 예제에서 3 개의 새 가상 머신 확장 집합 리소스가 영역 1, 2, 3에 추가 되었습니다. 이러한 가상 머신 확장 집합은 새로 만든 LB 및 IP를 참조 하 고 Service Fabric 클러스터 리소스에서 주 노드 유형으로 표시 됩니다.

시작 하려면 기존 리소스 관리자 템플릿에 새 리소스를 추가 해야 합니다. 해당 리소스는 다음과 같습니다.
* 표준 SKU를 사용 하는 공용 IP 리소스입니다.
* 표준 SKU를 사용 하는 Load Balancer 리소스입니다.
* 가상 머신 확장 집합을 배포 하는 서브넷에서 참조 하는 NSG입니다.
* 기본으로 표시 된 세 가지 노드 형식입니다.
    * 각 노드 형식은 다른 영역에 있는 자체 가상 머신 확장 집합에 매핑되어야 합니다.
    * 각 가상 머신 확장 집합에는 5 개 이상의 노드 (실버 내구성)가 있어야 합니다.

이러한 리소스의 예제는 [샘플 템플릿에서](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)찾을 수 있습니다.

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

리소스 배포가 완료 되 면 원래 클러스터에서 주 노드 형식의 노드를 사용 하지 않도록 설정할 수 있습니다. 노드가 사용 하지 않도록 설정 되 면 시스템 서비스는 위 단계에서 배포 된 새 주 노드 유형으로 마이그레이션됩니다.

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

노드가 모두 사용 하지 않도록 설정 되 면 시스템 서비스가 영역에 분산 되는 주 노드 형식에서 실행 됩니다. 그런 다음 클러스터에서 사용할 수 없는 노드를 제거할 수 있습니다. 노드가 제거 되 면 원래 IP, Load Balancer 및 가상 머신 확장 집합 리소스를 제거할 수 있습니다.

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

그런 다음 배포한 리소스 관리자 템플릿에서 이러한 리소스에 대 한 참조를 제거 해야 합니다.

최종 단계에는 DNS 이름 및 공용 IP 업데이트가 포함 됩니다.

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

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>모드 단일 가상 머신 확장 집합에서 여러 가용성 영역 사용

앞에서 언급 한 솔루션은 AZ 당 하나의 nodeType을 사용 합니다. 다음 솔루션을 사용 하면 사용자가 동일한 nodeType에 3 AZ 's를 배포할 수 있습니다.

전체 샘플 템플릿이 [여기](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)에 있습니다.

![Azure Service Fabric 가용성 영역 아키텍처][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 영역 구성
가상 머신 확장 집합에서 영역을 사용 하도록 설정 하려면 가상 머신 확장 집합 리소스에 다음 세 가지 값을 포함 해야 합니다.

* 첫 번째 값은 가상 머신 확장 집합에 있는 가용성 영역를 지정 하는 **zones** 속성입니다.
* 두 번째 값은 true로 설정 해야 하는 "Singleto Ementgroup" 속성입니다.
* 세 번째 값은 "zoneBalance"이 고, true로 설정 된 경우 엄격한 영역 분산을 보장 하는 옵션입니다. [ZoneBalancing](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#zone-balancing)에 대해 읽어 보세요.
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
        "zoneBalance": false
    }
}
```

>[!NOTE]
> * **SF 클러스터에는 하나 이상의 기본 nodeType이 있어야 합니다. DurabilityLevel 기본 nodeTypes는 은색 이상 이어야 합니다.**
> * DurabilityLevel에 관계 없이 3 개 이상의 가용성 영역을 사용 하 여 AZ 스패닝 가상 머신 확장 집합을 구성 해야 합니다.
> * 실버 내구성이 있는 가상 머신 확장 집합에 대 한 AZ 스패닝은 15 개 이상의 Vm이 있어야 합니다.
> * 가상 머신 확장 집합을 사용 하는 AZ를 사용 하 여 가상 머신 확장 집합을 확장 합니다.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Service Fabric nodeType에서 여러 영역에 대 한 지원 사용
여러 가용성 영역을 지원 하려면 nodeType Service Fabric를 사용 하도록 설정 해야 합니다.

* 첫 번째 값은 nodeType에 대해 true로 설정 해야 하는 **multipleAvailabilityZones** 입니다.
* 두 번째 값은 **sfZonalUpgradeMode** 이며 옵션입니다. 여러 AZ가 있는 nodetype이 클러스터에 이미 있는 경우에는이 속성을 수정할 수 없습니다.
      속성은 업그레이드 도메인의 Vm에 대 한 논리적 그룹화를 제어 합니다.
          값이 false (플랫 모드)로 설정 된 경우 노드 형식의 Vm은 5 Ud의 영역 정보를 무시 하 고 UD로 그룹화 됩니다.
          값이 생략 되거나 true (계층 모드)로 설정 된 경우 Vm은 최대 15 Ud 영역 배포를 반영 하도록 그룹화 됩니다. 각 3 개 영역에는 5 개의 Ud 있습니다.
          이 속성은 ServiceFabric 응용 프로그램 및 코드 업그레이드에 대 한 업그레이드 동작만 정의 합니다. 기본 가상 머신 확장 집합 업그레이드는 모든 AZ의에서 계속 병렬 처리 됩니다.
      이 속성은 여러 영역을 사용 하지 않는 노드 형식에 대해 UD 배포에 영향을 주지 않습니다.
* 세 번째 값은 **vmssZonalUpgradeMode = Parallel** 입니다. 여러 AZs를 포함 하는 nodeType을 추가 하는 경우 클러스터에서 구성 해야 하는 *필수* 속성입니다. 이 속성은 모든 AZ in에서 동시에 발생 하는 가상 머신 확장 집합 업데이트에 대 한 업그레이드 모드를 정의 합니다.
      현재이 속성은 병렬 으로만 설정할 수 있습니다.
* Service Fabric cluster resource apiVersion는 "2020-12-01-preview" 이상 이어야 합니다.
* 클러스터 코드 버전은 "7.2.445" 이상 이어야 합니다.

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
> * 공용 IP 및 Load Balancer 리소스는 문서의 앞부분에서 설명한 대로 표준 SKU를 사용 해야 합니다.
> * nodeType의 "multipleAvailabilityZones" 속성은 nodeType을 만들 때만 정의할 수 있으며 나중에 수정할 수 없습니다. 따라서이 속성을 사용 하 여 기존 nodeTypes를 구성할 수 없습니다.
> * "SfZonalUpgradeMode"이 생략 되거나 "계층 구조"로 설정 된 경우 클러스터에 업그레이드 도메인이 더 있으므로 클러스터 및 응용 프로그램 배포 속도가 느려집니다. 업그레이드 정책 시간 제한을 올바르게 조정 하 여 15 개의 업그레이드 도메인에 대 한 업그레이드 기간을 통합 하는 것이 중요 합니다.
> * 클러스터가 한 영역 다운 시나리오와 생존 되도록 클러스터 안정성 수준을 Platinum으로 설정 하는 것이 좋습니다.

>[!NOTE]
> 모범 사례를 위해 sfZonalUpgradeMode를 계층적으로 설정 하거나 생략 하는 것이 좋습니다. 배포는 더 적은 양의 복제본 및/또는 인스턴스에 영향을 주는 Vm의 영역 배포를 따라 더 안전 하 게 만듭니다.
> 배포 속도가 우선 순위 이거나 여러 AZ를 사용 하는 노드 유형에 서 상태 비저장 워크 로드만 실행 되는 경우 sfZonalUpgradeMode을 Parallel으로 설정 합니다. 이로 인해 UD가 모든 AZ의에서 병렬로 발생 합니다.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>여러 가용성 영역를 사용 하 여 노드 형식으로 마이그레이션
모든 마이그레이션 시나리오의 경우 여러 가용성 영역이 지원 되는 새 nodeType을 추가 해야 합니다. 기존 nodeType은 여러 영역을 지원 하도록 마이그레이션할 수 없습니다.
이 문서 [에서는](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-primary-node-type ) 새 nodetype을 추가 하는 자세한 단계를 캡처하고 IP 및 LB 리소스와 같은 새 nodetype에 필요한 다른 리소스도 추가 합니다. 동일한 문서는 여러 가용성 영역이 있는 nodeType이 클러스터에 추가 된 후 기존 nodeType을 사용 중지 하는 방법에 대해서도 설명 합니다.

* 기본 LB 및 IP 리소스를 사용 하는 nodeType에서의 마이그레이션:이는 AZ 당 하나의 노드 형식으로 솔루션 [에 대해 이미 설명 되어](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) 있습니다. 
    새 노드 형식에 대 한 유일한 차이점은 1 개의 가상 머신 확장 집합 및 1 개의 nodetype에 대 한 모든 AZ의 1이 아닌 모든 AZ의 유일한 차이점입니다.
* NSG를 사용 하 여 표준 SKU LB 및 IP 리소스를 사용 하는 nodeType에서의 마이그레이션은 위에 설명 된 것과 동일한 절차를 따르고, 새 LB, IP 및 NSG 리소스를 추가할 필요가 없으며, 동일한 리소스를 새 nodeType에서 재사용할 수 있습니다.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
