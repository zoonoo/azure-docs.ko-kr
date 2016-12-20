---
title: "Azure Service Fabric 재해 복구 | Microsoft Docs"
description: "Azure 서비스 패브릭은 모든 유형의 재해를 처리하는 데 필요한 기능을 제공합니다. 이 문서에서는 발생할 수 있는 재해의 유형과 처리하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/29/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ff3e8fe622bdd6ecba01bc08b26a243c592c3c8b


---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Azure 서비스 패브릭에서 재해 복구
고가용성 클라우드 응용 프로그램을 제공하는 중요한 부분은 통제 범위를 벗어난 오류를 비롯하여 모든 형식의 오류를 극복할 수 있는 것입니다. 이 문서는 잠재적 재해의 맥락에서 Azure 서비스 패브릭 클러스터의 물리적 레이아웃을 설명하고 가동 중지 시간 또는 데이터 손실의 위험을 제한하거나 제거하기 위해 이러한 재해를 처리하는 방법에 대한 지침을 제공합니다.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Azure에서 서비스 패브릭 클러스터의 물리적 레이아웃
다양한 유형의 오류가 발생시키는 위험을 이해하려면 Azure에서 클러스터를 물리적으로 레이아웃하는 방법을 아는 것이 유용합니다.

Azure에서 서비스 패브릭 클러스터를 만들 때 호스트될 지역을 선택해야 합니다. 그러면 Azure 인프라는 요청된 VM(가상 컴퓨터)의 수와 같은 가장 주목할 만한 사항을 비롯하여 지역 내에서 해당 클러스터에 대한 리소스를 프로비전합니다. 해당 VM을 프로비전하는 방법 및 위치를 더 자세히 살펴보겠습니다.

### <a name="fault-domains"></a>장애 도메인
기본적으로 클러스터의 VM은 FD(장애 도메인)라는 논리 그룹 간에 균등하게 분산되며 이는 호스트 하드웨어에서 오류가 발생할 가능성에 따라 컴퓨터를 분할합니다. 특히, 두 대의 VM이 다른 두 개의 FD에 위치하는 경우 동일한 전력 공급 또는 네트워크 스위치를 공유하지 않도록 할 수 있습니다. 결과적으로, VM 한 대에 영향을 미치는 로컬 네트워크 또는 전력 공급 오류는 다른 컴퓨터에 영향을 주지 않으며 이는 서비스 패브릭이 클러스터 내에서 응답하지 않는 컴퓨터의 작업 부하를 다시 분산할 수 있도록 합니다.

[서비스 패브릭 탐색기](service-fabric-visualizing-your-cluster.md)에 제공된 클러스터 맵을 사용하여 장애 도메인에 클러스터의 레이아웃을 시각화할 수 있습니다.

![노드는 서비스 패브릭 탐색기에서 장애 도메인에 분산됩니다.][sfx-cluster-map]

> [!NOTE]
> 클러스터 맵의 른 축에서는 계획된 유지 관리 작업에 따라 노드를 논리적으로 그룹화하는 업그레이드 도메인을 보여줍니다. Azure의 서비스 패브릭 클러스터는 항상 5개의 업그레이드 도메인에 걸쳐 레이아웃됩니다.
> 
> 

### <a name="geographic-distribution"></a>지리적 배포
[전 세계에 걸쳐 현재 26개의 Azure 지역][azure-regions]이 있으며 추가로 지역을 발표했습니다. 개별 지역은 다른 요소 중에서 적합한 위치의 수요 및 가용성에 따라 하나 이상의 물리적 데이터 센터를 포함할 수 있습니다. 단, 여러 물리적 데이터 센터를 포함하는 지역이라도 클러스터의 VM이 해당하는 물리적 위치에 균일하게 분산된다는 보장은 없습니다. 실제로 현재 지정된 클러스터에 대한 모든 VM은 단일 물리적 사이트 내에서 프로비전됩니다.

## <a name="dealing-with-failures"></a>오류 처리
자체 해결 방법으로 각 클러스터에 영향을 줄 수 있는 오류의 몇 가지 유형이 있습니다. 해당 작업이 발생한 순서에 따라 살펴보도록 하겠습니다.

### <a name="individual-machine-failures"></a>개별 컴퓨터 오류
앞서 언급했듯이 VM 내에서 발생한 개별 컴퓨터 오류나 장애 도메인 내에서 VM을 호스팅하는 하드웨어 또는 소프트웨어에서 발생한 개별 컴퓨터 오류 자체는 위험하지 않습니다. 서비스 패브릭에서는 일반적으로 몇 초 내에 오류를 검색하고 클러스터의 상태에 따라 적절하게 응답합니다. 예를 들어 노드가 파티션에 기본 복제본을 호스팅하는 경우 파티션의 보조 복제본에서 새로운 기본 복제본을 선택합니다. Azure에서 실패한 컴퓨터를 백업할 경우 클러스터를 자동으로 다시 연결하고 다시 워크로드의 공유를 수행합니다.

### <a name="multiple-concurrent-machine-failures"></a>여러 컴퓨터 동시 오류
장애 도메인이 컴퓨터 동시 오류의 위험을 크게 줄이는 반면 여러 가지 임의 오류로 인해 클러스터의 몇몇 컴퓨터를 동시에 종료시킬 가능성이 있습니다.

일반적으로 대부분의 노드가 사용할 수 있는 상태로 남아 있는 한, 적은 수의 컴퓨터 집합 및 상태 비저장 인스턴스에 압축된 상태 저장 복제본이 부하 분산에 사용할 수 있는 만큼 용량이 적더라도 클러스터는 계속 작동합니다.

#### <a name="quorum-loss"></a>쿼럼 손실
상태 저장 서비스의 파티션에 대한 다수의 복제본이 작동을 멈추면 해당 파티션은 "쿼럼 손실"이라는 상태를 입력합니다. 이 시점에서 서비스 패브릭은 상태가 일관적이고 신뢰할 수 있게 유지되도록 해당 파티션에 쓰기를 허용하지 않습니다. 사용할 수 없는 기간을 수용하도록 선택하여 데이터가 실제로 저장되지 않았을 때 클라이언트에 해당 데이터가 저장되었다고 전하지 않아야 합니다. 해당 상태 저장 서비스에 대한 보조 복제본에서 읽기를 허용하도록 옵트인한 경우 이 상태에서 해당 읽기 작업을 계속 수행할 수 있습니다. 복제본의 충분한 수가 돌아올 때 또는 클러스터 관리자가 [Repair-ServiceFabricPartition API][repair-partition-ps]를 사용하여 시스템을 진행하도록 강제할 때까지 파티션은 쿼럼 손실에 남아 있습니다.

> [!WARNING]
> 기본 복제본이 종료되는 경우 이 작업을 수행하면 데이터 손실이 발생합니다.
> 
> 

또한 시스템 서비스에서 쿼럼 손실이 발생할 수 있으며 이 영향으로 서비스에 특정되는지가 불확실해 집니다. 예를 들어 장애 조치(Failover) 관리자 서비스의 쿼럼 손실이 새 서비스 만들기 및 장애 조치를 차단하는 반면 서비스 명명의 쿼럼 손실은 이름 확인에 영향을 줍니다. 고유한 서비스와 달리 시스템 서비스를 복구하는 시도는 권장되지 *않습니다* . 대신, 중지된 복제본이 돌아올 때까지 대기하는 것이 좋습니다.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>쿼럼 손실의 위험 최소화
서비스에 대해 대상 복제본 세트 크기를 늘려 쿼럼 손실의 위험을 최소화할 수 있습니다. 한 번에 허용할 수 있는 사용할 수 있는 노드 수의 면에서 필요한 복제본의 수를 생각하는 것이 유용합니다. 나머지가 쓰기에 사용할 수 있는 반면 해당 응용 프로그램 또는 클러스터 업그레이드로 인해 하드웨어 오류 뿐만 아니라 노드를 일시적으로 사용할 수 없게 만들 수 있습니다.

다음 예제에서는 프로덕션 서비스에 대한 최소 권장 수인 세 개의 MinReplicaSetSize를 서비스가 포함하도록 구성했다고 가정합니다. 세 개(한 개는 기본, 두 개는 보조)의 TargetReplicaSetSize를 사용하여 업그레이드 중에(두 개의 복제본 중지) 발생한 하드웨어 오류는 쿼럼 손실로 이어지고 서비스는 읽기 전용으로 설정됩니다. 또는 5개의 복제본이 있으면 나머지 두 개의 복제본이 여전히 최소 복제본 세트 내에서 쿼럼을 구성할 수 있기에 업그레이드 중에(세 개의 복제본 중지) 두 개의 오류가 발생해도 문제가 발생하지 않을 수 있습니다.

### <a name="data-center-outages-or-destruction"></a>데이터 센터 중단 또는 소멸
드문 경우지만 실제 데이터 센터가 전원 또는 네트워크 연결의 손실로 인해 일시적으로 사용할 수 없게 될 수 있습니다. 이러한 경우에 서비스 패브릭 클러스터와 응용 프로그램은 마찬가지로 사용할 수 없게 되지만 데이터는 유지됩니다. Azure에서 실행 중인 클러스터의 경우 [Azure 상태 페이지][azure-status-dashboard]의 가동 중단에 대한 업데이트를 확인할 수 있습니다.

발생할 가능성이 거의 없는 전체 물리적 데이터 센터가 소멸되는 이벤트에서 호스팅되는 서비스 패브릭 클러스터는 상태와 함께 손실됩니다.

이러한 가능성을 방지하기 위해 주기적으로 지리적 중복 저장소에 [상태를 백업](service-fabric-reliable-services-backup-restore.md) 하고 이를 복원하는 유효한 기능이 있는지 확인합니다. 백업을 수행하는 빈도는 RPO(복구 지점 목표)에 따라 다릅니다. 백업을 완벽히 구현하고 복원하지 못했더라도 다음과 같은 상황이 발생한 경우 기록할 수 있도록 `OnDataLoss` 이벤트에 대한 처리기를 구현해야 합니다.

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(false);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>소프트웨어 오류 및 데이터 손실의 기타 소스
서비스의 코드 오류, 인적 작동 오류 및 보안 위반은 광범위한 데이터 센터 오류보다 일반적인 데이터 손실의 발생 원인입니다. 그러나, 모든 경우에 복구 전략은 동일하게 모든 상태 저장 서비스의 정기 백업을 수행하고 해당 상태를 복원하는 기능을 실행하는 것입니다.

## <a name="next-steps"></a>다음 단계
*  [테스트 용이성 프레임워크](service-fabric-testability-overview.md)
* 다른 재해 복구 및 고가용성 리소스를 참고합니다. Microsoft는 이 항목에 많은 양의 지침을 게시했습니다. 이러한 문서 중 일부가 다른 제품에서 사용하는 특정 기술을 가리키지만 서비스 패브릭 컨텍스트에서 적용할 수 있는 많은 일반적인 모범 사례를 포함합니다.
  * [가용성 검사 목록](../best-practices-availability-checklist.md)
  * [재해 복구 훈련 수행](../sql-database/sql-database-disaster-recovery-drills.md)
  * [Azure 응용 프로그램에 대한 재해 복구 및 고가용성][dr-ha-guide]

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png



<!--HONumber=Nov16_HO3-->


