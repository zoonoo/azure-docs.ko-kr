---
title: Azure Service Fabric에서 노드 유형 제거 | Microsoft Docs
description: Azure에서 실행 중인 Service Fabric 클러스터에서 노드 유형을 제거하는 방법을 알아봅니다.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969398"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>서비스 패브릭 노드 유형을 제거하는 방법
이 문서에서는 클러스터에서 기존 노드 유형을 제거하여 Azure Service Fabric 클러스터의 크기를 조정하는 방법을 설명합니다. Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 가상 머신 확장 집합은 가상 머신의 모음을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 컴퓨팅 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설정](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다. Service Fabric 클러스터를 만든 후에 노드 유형(가상 머신 확장 집합) 및 모든 노드를 제거하여 클러스터를 수평 확장할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

> [!WARNING]
> 이 방법을 사용하여 프로덕션 클러스터에서 노드 유형을 제거하는 것은 자주 사용하지 않는 것이 좋습니다. 노드 유형 뒤의 가상 머신 확장 집합 리소스가 삭제되므로 위험한 명령입니다. 

## <a name="durability-characteristics"></a>내구성 특징
안전은 제거-AzServiceFabricNodeType을 사용할 때 속도보다 우선 순위가 지정됩니다. 다음과 같은 이유로 노드 유형은 실버 또는 골드 [내구성 수준](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)이어야 합니다.
- Bronze는 상태 정보 저장이 전혀 보장되지 않습니다.
- 실버 및 골드 내구성은 확장 집합에 대한 모든 변경 내용을 트래핑합니다.
- 골드의 경우 확장 집합 아래에 있는 Azure 업데이트를 제어할 수도 있습니다.

Service Fabric은 데이터가 손실되지 않도록 내부 변경 및 업데이트를 "오케스트레이션"합니다. 그러나 브론즈 내구성이 있는 노드 유형을 제거하면 상태 정보가 손실될 수 있습니다. 기본 노드 유형을 제거하고 응용 프로그램이 상태 비저장인 경우 브론즈가 허용됩니다. 프로덕션에서 상태 저장 워크로드를 실행하는 경우에는 최소 구성이 Silver여야 합니다. 마찬가지로 프로덕션 시나리오의 경우 주 노드 유형은 항상 Silver 또는 Gold여야 합니다.

### <a name="more-about-bronze-durability"></a>Bronze 내구성에 대한 자세한 정보

Bronze 노드 유형을 제거하면 노드 유형의 모든 노드가 즉시 다운됩니다. Service Fabric은 브론즈 노드 확장 집합 업데이트를 트래핑하지 않기 때문에 모든 VM이 즉시 다운됩니다. 해당 노드에 상태 저장 항목이 있으면 데이터가 손실됩니다. 이제 상태 비저장이더라도 Service Fabric의 모든 노드가 링에 참여하기 때문에 전체 환경이 손실되어 클러스터 자체가 불안정해질 수 있습니다.

## <a name="remove-a-node-type"></a>노드 형식 제거

1. 프로세스를 시작하기 전에 이 필수 구성 사항을 처리하십시오.

    - 클러스터가 정상입니다.
    - 예를 들어 노드 유형을 제거한 후에도 충분한 용량이 유지됩니다. 필요한 복제본 수를 배치할 노드 수입니다.

2. 배치 제약 조건이 있는 모든 서비스를 이동하여 노드 유형에서 노드 유형을 사용합니다.

    - 더 이상 노드 유형을 참조하지 않는 응용 프로그램 / 서비스 매니페스트를 수정합니다.
    - 변경 을 배포합니다.

    그런 다음 다음 유효성을 검사합니다.
    - 위에서 수정한 모든 서비스는 노드 유형에 속하는 노드에서 더 이상 실행되지 않습니다.
    - 모든 서비스는 건전합니다.

3. 노드 유형을 기본이 아닌 것으로 표시 해제(기본이 아닌 노드 유형에 대해 건너뛰기)

    - 배포에 사용되는 Azure 리소스 관리자 템플릿을 찾습니다.
    - 서비스 패브릭 섹션에서 노드 유형과 관련된 섹션을 찾습니다.
    - 변경은 기본 속성을 false로 변경합니다. ** 이 작업의 노드 유형과 관련된 섹션을 제거하지 마십시오.
    - 수정된 Azure 리소스 관리자 템플릿을 배포합니다. ** 클러스터 구성에 따라 이 단계는 시간이 걸릴 수 있습니다.
    
    그런 다음 다음 유효성을 검사합니다.
    - 포털의 서비스 패브릭 섹션은 클러스터가 준비중임을 나타냅니다.
    - 클러스터가 정상입니다.
    - 노드 유형에 속하는 노드중 어느 노드도 시드 노드로 표시되지 않습니다.

4. 노드 유형에 대한 데이터를 사용하지 않도록 설정합니다.

    PowerShell을 사용하여 클러스터에 연결한 다음 다음 단계를 실행합니다.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - 브론즈 내구성을 위해 모든 노드가 비활성화된 상태로 갈 때까지 기다립니다.
    - 은 과 금 내구성의 경우 일부 노드는 비활성화되고 나머지는 비활성화 상태가 됩니다. 비활성화 상태의 노드의 세부 정보 탭을 확인하여 인프라 서비스 파티션에 대한 쿼럼을 보장하는 데 모두 걸린 경우 계속하는 것이 안전합니다.

5. 노드 유형에 대한 데이터를 중지합니다.

    PowerShell을 사용하여 클러스터에 연결한 다음 다음 단계를 실행합니다.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    노드 유형에 대한 모든 노드가 아래로 표시될 때까지 기다립니다.
    
6. 노드 유형에 대한 데이터를 제거합니다.

    PowerShell을 사용하여 클러스터에 연결한 다음 다음 단계를 실행합니다.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    클러스터에서 모든 노드가 제거될 때까지 기다립니다. 노드는 SFX에 표시되지 않아야 합니다.

7. 서비스 패브릭 섹션에서 노드 유형을 제거합니다.

    - 배포에 사용되는 Azure 리소스 관리자 템플릿을 찾습니다.
    - 서비스 패브릭 섹션에서 노드 유형과 관련된 섹션을 찾습니다.
    - 노드 유형에 해당하는 섹션을 제거합니다.
    - Silver 및 더 높은 내구성 클러스터에 대해서만 템플릿에서 클러스터 리소스를 업데이트하고 아래와 같이 `applicationDeltaHealthPolicies` 클러스터 `properties` 리소스 아래에 추가하여 패브릭:/System 응용 프로그램 상태를 무시하도록 상태 정책을 구성합니다. 아래 정책은 기존 오류를 무시해야 하지만 새 상태 오류를 허용하지 않아야 합니다. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - 수정된 Azure 리소스 관리자 템플릿을 배포합니다. ** 이 단계는 보통 최대 2시간 정도 걸립니다. 이 업그레이드는 인프라 서비스로 설정을 변경하므로 노드를 다시 시작해야 합니다. 이 경우 `forceRestart` 무시됩니다. 
    매개 `upgradeReplicaSetCheckTimeout` 변수는 서비스 패브릭이 파티션이 안전한 상태가 될 때까지 기다리는 최대 시간을 지정합니다(아직 안전한 상태가 아닌 경우). 안전 검사가 노드의 모든 파티션을 통과하면 서비스 패브릭은 해당 노드의 업그레이드를 진행합니다.
    매개 변수의 `upgradeTimeout` 값을 6시간으로 줄일 수 있지만 최대 안전을 위해 최대 12시간을 사용해야 합니다.

    그런 다음 다음 유효성을 검사합니다.
    - 포털의 서비스 패브릭 리소스가 준비되었습니다.

8. 노드 유형과 관련된 리소스에 대한 모든 참조를 제거합니다.

    - 배포에 사용되는 Azure 리소스 관리자 템플릿을 찾습니다.
    - 템플릿에서 가상 시스템 확장 집합 및 노드 유형과 관련된 기타 리소스를 제거합니다.
    - 변경 내용을 배포합니다.

    그렇다면
    - 배포가 완료될 때까지 기다립니다.

## <a name="next-steps"></a>다음 단계
- 클러스터 [내구성 특징](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)에 대해 자세히 알아봅니다.
- [노드 유형과 Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md)에 대해 자세히 알아봅니다.
- [Service Fabric 클러스터 크기 조정](service-fabric-cluster-scaling.md)에 대해 자세히 알아봅니다.
