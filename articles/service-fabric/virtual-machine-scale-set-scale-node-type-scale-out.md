---
title: Virtual Machine Scale Set을 추가하여 Azure Service Fabric 클러스터 규모 확장 | Microsoft Docs
description: Virtual Machine Scale Set을 추가하여 Service Fabric 클러스터를 확장하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2018
ms.author: ryanwi
ms.openlocfilehash: 8f460b41cd2ce62b7a3e0138caa25f68e2fd22ad
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156496"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Virtual Machine Scale Set을 추가하여 Service Fabric 클러스터 확장
이 문서에서는 기존 클러스터에 새 가상 머신 확장 집합을 추가하여 Azure Service Fabric 클러스터를 확장하는 방법을 설명합니다. Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 가상 머신 확장 집합은 가상 머신의 모음을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설정](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다. Service Fabric 클러스터가 만들어지면 클러스터 노드 유형을 수직으로 확장하거나(노드의 리소스 변경), 노드 유형 VM의 운영 체제를 업그레이드하거나, 기존 클러스터에 새 가상 머신 확장 집합을 추가할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 응용 프로그램 크기도 자동으로 조정됩니다.

> [!WARNING]
> 클러스터 상태가 비정상인 경우 주 노드 형식 VM SKU 변경을 시작하지 마세요. 클러스터 상태가 비정상인 경우 VM SKU를 변경하려고 시도하면 클러스터만 더 불안정하게 됩니다.
>
> [실버 내구성 이상](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)에서 실행되는 경우가 아니면 확장 집합/노드 형식의 VM SKU를 변경하지 않는 것이 좋습니다. VM SKU 크기 변경은 데이터 파괴적 내부 인프라 작업입니다. 이러한 변경을 지연하거나 모니터링하는 기능이 없으면 이러한 작업으로 인해 상태 저장 서비스에 대해 데이터 손실이 발생하거나 상태 비저장 워크로드의 경우에도 예기치 못한 다른 작동 문제가 발생할 수 있습니다. 즉, 상태 저장 서비스 패브릭 시스템 서비스를 실행하는 주 노드 유형 또는 상태 저장 응용 프로그램 작업을 실행하는 노드 유형이 로드됩니다.
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>주 노드 유형 VM의 크기 및 운영 체제 업그레이드
주 노드 유형 VM의 VM 크기 및 운영 체제를 업데이트하는 프로세스는 다음과 같습니다.  업그레이드 후에 주 노드 유형 VM은 표준 D4_V2 크기이고 컨테이너가 있는 Windows Server 2016 Datacenter를 실행합니다.

> [!WARNING]
> 프로덕션 클러스터에서 이 절차를 시도하기 전에 먼저 샘플 템플릿을 알아보고 테스트 클러스터에 대한 프로세스를 확인하는 것이 좋습니다. 또한 클러스터도 잠시 사용할 수 없습니다. 병렬로 동일 NodeType으로 선언된 여러 개의 VMSS를 변경할 수 없습니다. 각 NodeType VMSS에 변경 내용을 개별적으로 적용하려면 별도의 배포 작업을 수행해야 합니다.

1. 이러한 [템플릿](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) 및 [매개 변수](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) 샘플 파일을 사용하여 노드 유형과 확장 집합(노드 유형당 하나의 확장 집합)이 각각 두 개씩 있는 초기 클러스터를 배포합니다.  두 개의 확장 집합은 모두 표준 D2_V2 크기이며 Windows Server 2012 R2 Datacenter를 실행합니다.  클러스터에서 기준 업그레이드를 완료할 때까지 기다리세요.   
2. 선택 사항 - 클러스터에 상태 저장 샘플을 배포합니다.
3. 주 노드 유형 VM을 업그레이드하도록 결정한 후 주 노드 유형에 두 개의 확장 집합이 있으므로 이러한 [템플릿](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) 및 [매개 변수](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) 샘플 파일을 사용하여 주 노드 유형에 새 확장 집합을 추가합니다.  시스템 서비스 및 사용자 응용 프로그램은 두 개의 서로 다른 확장 집합의 VM 간에 마이그레이션할 수 있습니다.  새 확장 집합 VM은 표준 D4_V2 크기이며 Windows Server 2016 Datacenter with Containers를 실행합니다.  새 부하 분산 장치와 공용 IP 주소도 새 확장 집합과 함께 추가됩니다.  
    템플릿에서 새 확장 집합을 찾으려면 *vmNodeType2Name* 매개 변수로 명명된 "Microsoft.Compute/virtualMachineScaleSets" 리소스를 검색합니다.  새 확장 집합은 속성 -> virtualMachineProfile -> extensionProfile -> 확장 -> 속성 -> 설정 -> nodeTypeRef 설정을 차례로 사용하여 주 노드 유형에 추가됩니다.
4. 클러스터 상태를 확인하고 모든 노드가 정상인지 확인합니다.
5. 노드를 제거하려는 의도로 주 노드 유형의 이전 확장 집합에 있는 노드를 사용하지 않도록 설정합니다. 한 번에 모두 사용하지 않도록 설정할 수 있으며 작업이 큐에서 대기합니다. 약간의 시간이 걸릴 수도 있지만 모든 노드가 사용되지 않도록 설정될 때까지 기다리세요.  노드 유형의 이전 노드가 사용되지 않도록 설정되면 시스템 서비스 및 시드 노드에서 주 노드 유형에 설정된 새 확장 집합의 VM으로 마이그레이션합니다.
6. 주 노드 유형에서 이전 확장 집합을 제거합니다.
7. 이전의 확장 집합과 연결된 부하 분산 장치를 제거합니다. 새 확장 집합에 대한 새 공용 IP 주소 및 부하 분산 장치가 구성되는 동안에는 클러스터를 사용할 수 없습니다.  
8. 변수에 설정된 이전의 주 노드 유형 확장 집합과 연결된 공용 IP 주소의 DNS 설정을 저장하고 해당 공용 IP 주소를 제거합니다.
9. 새 주 노드 유형 확장 집합과 연결된 공용 IP 주소의 DNS 설정을 삭제된 공용 IP 주소의 DNS 설정으로 바꿉니다.  이제 클러스터에 다시 연결할 수 있습니다.
10. 클러스터에서 노드에 대한 노드 상태를 제거합니다.  이전의 확장 집합에 대한 내구성 수준이 은색 또는 금색으로 표시되면 시스템에서 이 단계를 자동으로 수행합니다.
11. 이전 단계에서 상태 저장 응용 프로그램을 배포한 경우 응용 프로그램이 작동하는지 확인합니다.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="adding-an-additional-scale-set-to-an-existing-cluster"></a>기존 클러스터에 확장 집합 추가
기존 클러스터에 새 NodeType Virtual Machine Scale Set 추가는 NodeTypeRef를 사용하지 않는다는 점을 제외하고 앞서 언급한 주 노드 형식 업그레이드와 비슷합니다. 적극적으로 사용되는 Virtual Machine Scale Set을 비활성화하지 않으며, 주 노드 형식을 업데이트하지 않아도 클러스터 가용성을 잃지 않습니다. 

NodeTypeRef 속성은 Virtual Machine Scale Set Service Fabric 확장 속성 내에서 선언됩니다.
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

또한 이 새 노드 형식에 Service Fabric 클러스터 리소스를 추가해야 합니다.

```json
<snip>
"nodeTypes": [
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
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>다음 단계
* [응용 프로그램 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
* [Azure 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-tutorial-scale-cluster.md)합니다.
* 유용한 Azure Compute SDK를 사용하여 [Azure 클러스터의 크기를 프로그래밍 방식으로 조정](service-fabric-cluster-programmatic-scaling.md)합니다.
* [독립 실행형 클러스터 스케일 인 또는 스케일 아웃](service-fabric-cluster-windows-server-add-remove-nodes.md).

