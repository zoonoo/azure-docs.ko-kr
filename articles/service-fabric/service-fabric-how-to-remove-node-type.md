---
title: Azure Service Fabric에서 노드 유형 제거 | Microsoft Docs
description: Azure에서 실행 중인 Service Fabric 클러스터에서 노드 유형을 제거하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 193a24aebff8f7de60752e53bbc1b18dd5c54f33
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482201"
---
# <a name="remove-a-service-fabric-node-type"></a>Service Fabric 노드 유형 제거
이 문서에서는 클러스터에서 기존 노드 유형을 제거하여 Azure Service Fabric 클러스터의 크기를 조정하는 방법을 설명합니다. Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 가상 머신 확장 집합은 가상 머신의 모음을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 계산 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설정](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다. Service Fabric 클러스터를 만든 후에 노드 유형(가상 머신 확장 집합) 및 모든 노드를 제거하여 클러스터를 수평 확장할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

사용 하 여 [제거 AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) Service Fabric 노드 유형을 제거 하려면.

제거-AzServiceFabricNodeType 호출 될 때 발생 하는 세 가지 작업에서는 다음과 같습니다.
1.  노드 유형 뒤의 가상 머신 확장 집합이 삭제됩니다.
2.  노드 유형이 클러스터에서 제거됩니다.
3.  해당 노드 유형에 포함된 각 노드의 전체 상태가 시스템에서 제거됩니다. 해당 노드에 서비스가 있으면 먼저 서비스가 다른 노드로 옮겨집니다. 클러스터 관리자가 복제본/서비스에 대한 노드를 찾을 수 없으면 작업이 지연/차단됩니다.

> [!WARNING]
> Remove-AzServiceFabricNodeType을 사용하여 프로덕션 클러스터에서 노드 유형을 제거하는 것은 자주 사용하지 않는 것이 좋습니다. 노드 유형 뒤의 가상 머신 확장 집합 리소스가 삭제되므로 위험한 명령입니다. 

## <a name="durability-characteristics"></a>내구성 특징
안전성은 AzServiceFabricNodeType 제거를 사용 하는 경우 속도 보다 우선 적용 됩니다. 다음과 같은 이유로 노드 유형은 실버 또는 골드 [내구성 수준](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)이어야 합니다.
- Bronze는 상태 정보 저장이 전혀 보장되지 않습니다.
- 실버 및 골드 내구성은 확장 집합에 대한 모든 변경 내용을 트래핑합니다.
- 골드의 경우 확장 집합 아래에 있는 Azure 업데이트를 제어할 수도 있습니다.

Service Fabric은 데이터가 손실되지 않도록 내부 변경 및 업데이트를 "오케스트레이션"합니다. 단, Bronze 내구성으로 노드를 제거하면 상태 정보가 손실될 수 있습니다. 주 노드 유형을 제거하는 경우 애플리케이션이 상태 비저장이면 Bronze가 허용됩니다. 프로덕션에서 상태 저장 워크로드를 실행하는 경우에는 최소 구성이 Silver여야 합니다. 마찬가지로 프로덕션 시나리오의 경우 주 노드 유형은 항상 Silver 또는 Gold여야 합니다.

### <a name="more-about-bronze-durability"></a>Bronze 내구성에 대한 자세한 정보

Bronze 노드 유형을 제거하면 노드 유형의 모든 노드가 즉시 다운됩니다. Service Fabric은 브론즈 노드 확장 집합 업데이트를 트래핑하지 않기 때문에 모든 VM이 즉시 다운됩니다. 해당 노드에 상태 저장 항목이 있으면 데이터가 손실됩니다. 이제 상태 비저장이더라도 Service Fabric의 모든 노드가 링에 참여하기 때문에 전체 환경이 손실되어 클러스터 자체가 불안정해질 수 있습니다.

## <a name="recommended-node-type-removal-process"></a>권장되는 노드 유형 제거 프로세스

노드 유형을 제거하려면 [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) cmdlet을 실행합니다.  cmdlet을 완료하는 데 시간이 걸립니다.  그런 다음, 제거할 각 노드에서 [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)를 실행합니다.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>다음 단계
- 클러스터 [내구성 특징](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)에 대해 자세히 알아봅니다.
- [노드 유형과 Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md)에 대해 자세히 알아봅니다.
- [Service Fabric 클러스터 크기 조정](service-fabric-cluster-scaling.md)에 대해 자세히 알아봅니다.
