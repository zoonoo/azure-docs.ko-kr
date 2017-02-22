---
title: "메트릭을 사용하여 Azure 마이크로 서비스 부하 관리 | Microsoft 문서"
description: "서비스 리소스 소비를 관리하기 위해 Service Fabric에서 메트릭을 구성하고 사용하는 방법에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>메트릭을 사용하여 서비스 패브릭에서 리소스 부하 및 소비 관리
메트릭은 서비스가 고려하고, 클러스터의 노드에서 제공하는 리소스에 대한 서비스 패브릭 내의 일반적인 용어입니다. 일반적으로 메트릭은 서비스의 성능을 다루기 위해 관리하려는 모든 항목입니다.

메모리, 디스크, CPU 사용량 등이 모두 메트릭의 예입니다. 앞서 언급한 항목이 관리되어야 하는 노드에서 실제 리소스에 해당하는 실제 메트릭인 리소스입니다. 메트릭은 논리 메트릭이 될 수도 있으며 이것이 일반적인 경우이기도 합니다. “MyWorkQueueDepth”, "MessagesToProcess" 또는 "TotalRecords" 등이 논리 메트릭입니다. 논리 메트릭은 응용 프로그램에 정의되며 일부 물리적 리소스 소비에 해당하지만, 응용 프로그램은 실제로 이를 측정하는 방법을 알지 못합니다. 이것이 일반적입니다. 사람들이 사용하는 대부분의 메트릭은 논리 메트릭입니다. 대부분은 현재 많은 서비스가 관리되는 코드로 작성되기 때문입니다. 관리되는 코드란 호스트 프로세스 안에서 측정하기 어렵고 단일 서비스 개체의 물리적 리소스 사용을 보고한다는 뜻입니다. 고유 메트릭 측정 및 보고의 복잡성은 기본적으로 몇 가지 기본 메트릭을 제공하는 이유이기도 합니다.

## <a name="default-metrics"></a>기본 메트릭
시작하려고 하지만 어떤 리소스를 사용할지 또는 어떤 리소스가 중요한지에 대해 알지 못한다고 가정해 보겠습니다. 그러면 구현으로 이동하여 다음 메트릭을 지정하지 않고 서비스를 만듭니다. 이것으로 끝입니다. Service Fabric Cluster Resource Manager는 몇 가지 간단한 메트릭을 선택합니다. 하나를 지정하지 않는 경우 지금 사용하려는 기본 메트릭은 PrimaryCount, ReplicaCount 및 Count입니다. 아래 표에서는 각 서비스 개체와 연결된 이러한 각 메트릭에 대한 부하 크기를 보여 줍니다.

| 메트릭 | 상태 비저장 인스턴스 부하 | 상태 저장 보조 부하 | 상태 저장 기본 부하 |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| 개수 |1 |1 |1 |

네, 따라서 이러한 기본 메트릭을 사용하여 무엇을 가져올 수 있나요? 기본 워크로드에서 작업을 잘 배포하게 됩니다. 다음 예제에서는 두 서비스를 만들 때 어떻게 되는지 확인해 봅니다. 첫 번째 서비스는 파티션&3;개와,&3;개 크기의 대상 복제본 세트가 있는 상태 저장 서비스입니다. 두 번째는 파티션&1;개와, 인스턴스 개수가&3;인 상태 비저장 서비스입니다.

<center>
![기본 메트릭으로 클러스터 레이아웃][Image1]
</center>

다음을 알 수 있습니다.

* 상태 저장 서비스에 대한 기본 복제본은 단일 노드에 저장되지 않습니다
* 동일한 파티션에 대한 복제본은 동일한 노드에 있지 않습니다
* 기본 복제본 및 보조 복제본의 총 수는 클러스터에 배포됩니다
* 서비스 개체의 총 수는 각 노드에 균등하게 할당됩니다.

좋습니다.

이 구성은 대규모 실제 워크로드를 실행하지 않는 한 잘 작동합니다. 선택한 파티션 구성표가 모든 파티션에서 완벽하게 고른 사용률을 보일 가능성은 어떻게 될까요? 지정된 서비스에 대한 부하가 시간에 따라 일정하거나 지금과 동일할 가능성은 있나요?

현실적으로 기본 메트릭만으로 실행할 수도 있지만 그럴 경우 보통은 클러스터 사용률이 기대보다 낮아집니다. 기본 메트릭 보고가 조절되지 않으며 모든 것이 같다고 가정하기 때문입니다. 기본값만 사용하게 되면 최악의 경우 노드의 과도한 예약으로 성능 문제를 초래할 수 있습니다. 다음에 다루게 될 사용자 지정 메트릭 및 동적 부하 보고서를 더 잘 수행할 수 있습니다. 모든 실제 워크로드에서 모든 서비스가 영원히 동등할 가능성은 낮습니다. 클러스터를 최대한 활용하고 성능 문제를 방지하는 데 관심이 있다면 사용자 지정 메트릭을 살펴보고자 할 것입니다.

## <a name="custom-metrics"></a>사용자 지정 메트릭
메트릭은 서비스를 만들 때 지정된 서비스 인스턴스별로 구성됩니다.

모든 메트릭에는 자신을 설명하는 이름, 기본 부하, 가중치 등의 몇 가지 속성이 있습니다.

* 메트릭 이름: 메트릭의 이름. 메트릭 이름은 리소스 관리자의 관점에서 클러스터 내의 메트릭에 대한 고유한 식별자입니다.
* 기본 부하: 기본 부하는 서비스가 상태 비저장 서비스인지 또는 상태 저장 서비스인지에 따라 다르게 표현됩니다.
  * 상태 비저장 서비스의 경우 각 메트릭에는 DefaultLoad라는 단일 속성만 있습니다.
  * 상태 저장 서비스의 경우 다음을 정의합니다.
    * PrimaryDefaultLoad: 이 서비스가 기본일 때 소비하는 이 메트릭의 기본 크기입니다.
    * SecondaryDefaultLoad: 이 서비스가 보조일 때 소비하는 이 메트릭의 기본 크기입니다.
* Weight: 메트릭 가중치는 이 서비스에 대한 다른 메트릭에 상대적으로 이 메트릭이 갖는 중요도를 정의합니다.

사용자 지정 메트릭을 정의하고 기본 메트릭도 사용하려면 명시적으로 다시 추가해야 합니다. 기본 메트릭과 사용자 지정 메트릭 간의 관계를 명확히 하기 위해서입니다. 예를 들어, 기본 배포보다 메모리 소비나 WorkQueueDepth 방식에 대해 더 신경쓸 수 있습니다.

### <a name="defining-metrics-for-your-service---an-example"></a>이 서비스를 예로 메트릭 정의
"MemoryInMb"라는 메트릭을 보고하는 서비스를 구성하고 기본 메트릭도 사용하려 합니다. 기본 측정을 수행했으며 일반적으로 해당 서비스의 주 복제본이 20단위의 "MemoryInMb"를 차지하고 보조 복제본은 5단위를 차지한다고 가정해 보겠습니다. 이 특정 서비스의 관리에서는 메모리가 가장 중요한 메트릭이지만 주 복제본의 분산도 수행하려 합니다. 주 복제본을 분산하면 일부 노드나 장애 도메인의 손실이 그에 따른 대다수의 주 복제본에 영향을 미치지 않게 되므로 좋은 생각입니다. 이러한 조정 외에도 기본 메트릭을 사용하려 합니다.

이러한 메트릭 구성이 있는 서비스를 만들기 위한 코드는 다음과 같습니다.

코드:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(기본 메트릭을 사용하려는 경우 서비스를 만들 때 메트릭 컬렉션을 사용하거나 특별한 작업을 수행할 필요가 없습니다.)

지금까지 소개와 예제를 살펴보았으니 이제 각각의 설정을 더 상세히 확인하고 결과에 대해 알아보겠습니다.

## <a name="load"></a>로드
메트릭 정의의 핵심은 일부 부하에 관한 것입니다. 부하는 특정 노드에서 일부 서비스 인스턴스 또는 복제본이 해당 메트릭을 소비하는 정도입니다. 예상 부하는 서비스를 만들 때 구성하고, 서비스를 만든 뒤 업데이트하며, 서비스 개체별로 보고할 수 있고 또는 이 세 가지 모두가 가능합니다.

## <a name="default-load"></a>기본 부하
기본 부하는 이 서비스의 각 서비스 개체(인스턴스 또는 복제본)가 기본적으로 소비하는 부하의 크기입니다. 간단한 서비스의 경우 기본 부하가 절대 업데이트되지 않으며 서비스의 수명 동안 사용할 수 있는 정적 정의가 됩니다. 기본 부하는 특정 크기의 리소스가 서로 다른 워크로드를 전담하는 단순한 용량 계획 상황에 적합합니다.

Cluster Resource Manager에서는 상태 저장 서비스에서 기본 및 보조 모두에 서로 다른 기본 부하를 지정할 수 있는 반면 상태 비저장 서비스에서는 한 값만 지정할 수 있습니다. 상태 저장 서비스의 경우 기본 및 보조 복제본에 대한 기본 부하는 일반적으로 다릅니다. 복제본이 각 역할에서 서로 다른 종류의 작업을 수행하기 때문입니다. 예를 들어, 기본 복제본은 보통 읽기와 쓰기(및 대부분의 연산 작업)를 모두 수행하지만 보조 복제본은 다릅니다. 기본 복제본의 기본 부하가 보조 복제본보다 클 것으로 예상되지만 실제 수치는 자체 측정에 따라 달라질 것입니다.

## <a name="dynamic-load"></a>동적 부하
한동안 서비스를 실행했다고 가정해 보겠습니다. 일부 모니터링에서 다음을 확인했습니다.

1. 일부 파티션 또는 특정 서비스의 인스턴스가 다른 항목보다 더 많은 리소스를 소비합니다.
2. 일부 서비스에 시간 경과에 따라 달라지는 부하가 있습니다.

많은 요소가 부하 변동을 야기할 수 있습니다. 서비스 또는 파티션은 특정 고객과 연결되거나, 일과 중 변동하는 워크로드에 상응할 수 있습니다. 어떤 이유로든 기본 부하에 사용할 수 있는 간단한 수치는 없습니다. 기본 부하에 선택한 값은 어느 시점에는 맞지 않게 됩니다. 잘못된 기본 부하는 Cluster Resource Manager가 서비스에 리소스를 과다 또는 부족하게 할당하는 결과를 초래하기 때문에 문제가 됩니다. 이 때문에 Cluster Resource Manager가 클러스터의 부하가 분산되었다고 간주하는 상황에서도 노드가 과다 또는 부족하게 사용됩니다. 기본 부하는 일부 정보를 제공하기 때문에 여전히 유효하나 대부분의 시간에서 실제 워크로드를 완전히 알려주지는 못합니다. 따라서 Cluster Resource Manager에서는 런타임 중에 각 서비스 개체가 자체 부하를 업데이트할 수 있습니다. 이를 동적 부하 보고라 합니다.

동적 부하 보고서를 사용하면 복제본 또는 인스턴스가 자신의 수명 동안 메트릭의 할당/보고된 부하를 조정할 수 있습니다. 콜드 상태이며 아무 작업도 수행하지 않는 서비스 복제본 도는 인스턴스는 보통 특정 메트릭을 적게 사용했다고 보고합니다. 사용 중인 복제본 또는 인스턴스는 더 많은 양을 사용 중이라 보고할 것입니다.

복제본 또는 인스턴스별 보고를 통해 Cluster Resource Manager는 클러스터에서 개별 서비스 개체를 재구성함으로써 서비스가 필요한 리소스를 확보하게 할 수 있습니다. 사용 중인 서비스는 현재 콜드 상태이거나 작업이 적은 다른 복제본 또는 인스턴스로부터 리소르를 효과적으로 "회수"할 수 있습니다.

신뢰할 수 있는 서비스 안에서 부하를 동적으로 보고하는 코드는 다음과 같습니다.

코드:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

서비스 복제본 또는 인스턴스는 만들어질 때 사용하도록 구성된 메트릭에 대한 부하만 보고할 수 있습니다. 서비스가 보고할 수 있는 메트릭 목록은 서비스를 만들 때 지정한 것과 동일한 집합입니다. 서비스와 연결된 메트릭 목록은 동적으로 업데이트할 수 있습니다. 서비스 복제본 또는 인스턴스가 메트릭에 대한 부하가 현재 사용되도록 구성되지 않았다고 보고하려는 경우 Service Fabric은 이 보고서를 기록하지만 무시합니다. 동일한 API 호출에서 유효한 다른 메트릭을 보고한 경우 이 보고서를 수락하여 사용합니다. 더 큰 규모의 테스트가 가능하므로 깔끔합니다. 이 코드는 알고 있는 모든 메트릭을 측정하여 보고할 수 있으며, 작업자는 코드를 변경하지 않고도 해당 서비스에 대한 메트릭 구성을 지정하고 업데이트할 수 있습니다. 예를 들어, 관리자나 운영 팀은 특정 서비스에 대한 버그 보고서를 통해 베트릭을 비활성화하고, 작동을 기준으로 메트릭의 가중치를 재구성하거나, 코드가 이미 배포되어 다른 방식으로 검증된 후에 새 메트릭을 활성화할 수 있습니다.

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>기본 부하 값 및 동적 부하 보고서 혼합
기본 부하로는 부족하고 동적 부하 보고가 더 낫다면 두 가지를 모두 사용할 수 있을까요? 예! 사실 이것이 일반적으로 권장되는 구성입니다. 두 기본 부하를 모두 설정하고 동적 부하 보고서를 사용할 경우 기본 부하는 동적 보고서가 나타날 때까지 추정치 역할을 합니다. Cluster Resource Manager에게 작업 정보를 제공하므로 좋은 방법입니다. 기본 부하는 Cluster Resource Manager가 서비스 개체를 만들 때 적절한 위치에 배치할 수 있습니다. 기본 부하 정보가 없으면 서비스 배치가 임의로 수행됩니다. 부하 보고서가 나중에 오면 Cluster Resource Manager는 거의 항상 서비스를 이동해야 합니다.

이전 예제에서 몇 가지 사용자 지정 메트릭과 동적 부하 보고를 추가하면 어떻게 되는지 살펴보겠습니다. 이 예제에서는 "메모리"를 예제 메트릭으로 사용합니다. 다음 명령을 사용하여 상태 저장 서비스를 처음 만들었다고 가정하겠습니다.

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

참고로, 이 구문은 ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad")입니다.

가능한 클러스터 레이아웃의 모양을 살펴보겠습니다.

<center>
![기본 및 사용자 지정 메트릭으로 클러스터 부하 분산][Image2]
</center>

주목할 만한 몇 가지가 있습니다.

* 복제본 또는 인스턴스가 고유한 부하를 보고할 때까지 서비스의 기본 부하를 사용하기 때문에 상태 저장 서비스의 파티션 1 내부에 있는 복제본은 동적으로 부하를 보고하지 않았습니다.
* 파티션 내 보조 복제본은 고유한 부하를 가질 수 있습니다.
* 전반적으로 메트릭은 분산된 것처럼 보입니다. 메모리의 경우 최대 부하와 최소 부하 간 비율은 1.75(부하가 가장 높은 노드 N3, 가장 적은 노드 N2, 28/16 = 1.75)입니다.

다음과 같은 몇 가지에 대한 설명이 필요합니다.

* 1.75의 비율이 합리적인지를 무엇이 결정했나요? 클러스터 리소스 관리자는 충분한지 또는 추가 작업이 필요한지를 어떻게 확인하나요?
* 언제 부하가 분산되나요?
* 메모리의 가중치가 "높음"은 무슨 의미인가요?

## <a name="metric-weights"></a>메트릭 가중치
서로 다른 서비스에서 동일한 메트릭을 추적할 수 있게 하는 것이 중요합니다. 이 보기를 통해 클러스터 리소스 관리자가 클러스터에서의 소비를 추적하고, 노드 전체에서 소비의 균형을 맞추며, 노드의 용량 초과를 방지합니다. 그러나 서비스에서 동일한 메트릭의 중요도에 따라 서로 다른 보기가 있을 수 있습니다. 또한 메트릭과 서비스가 많은 클러스터에서는 모든 메트릭에 대해 완벽하게 분산된 솔루션이 없을 수도 있습니다. Cluster Resource Manager는 이러한 상황을 어떻게 처리해야 할까요?

메트릭 가중치를 통해 Cluster Resource Manager는 완벽한 답이 없을 때 클러스터를 분산하는 방법을 결정할 수 있습니다. 메트릭 가중치를 통해 Cluster Resource Manager가 특정 서비스를 다르게 분산할 수도 있습니다. 메트릭은&0;, 낮음, 보통, 높음 등 네 가지 가중치 수준을 사용할 수 있습니다. 가중치가&0;인 메트릭은 부하가 분산되는지 여부를 고려하는 경우 영향을 주지 않지만 해당 부하는 용량 등의 항목에 영향을 줍니다.

클러스터에서 서로 다른 메트릭 가중치의 실제 영향은 Cluster Resource Manager가 서로 다른 솔루션을 생성한다는 것입니다. 메트릭 가중치는 Cluster Resource Manager에게 특정 메트릭이 다른 메트릭보다 더 중요함을 알려 줍니다. 완벽한 솔루션이 없으면 Cluster Resource Manager는 가중치가 더 높은 메트릭을 더 잘 분산하는 솔루션을 선호합니다. 한 서비스에서 메트릭이 중요하지 않다고 판단되면 해당 메트릭의 사용이 분산되지 않았다고 생각할 수 있습니다. 이를 통해 다른 서비스에서 자신에게 중요한 고른 배포를 확보할 수 있습니다.

일부 부하 보고서의 예제와, 클러스터에서 서로 다른 메트릭 가중치가 어떤 결과를 낼 수 있는지 살펴보겠습니다. 이 예제에서 메트릭의 상대 가중치를 전환하면 Cluster Resource Manager가 서로 다른 솔루션을 선호하고 서비스의 서로 다른 배열을 만드는 것을 확인하게 됩니다.

<center>
![메트릭 가중치 예제 및 부하 분산 솔루션에 미치는 영향][Image3]
</center>

이 예제에는 모두 두 가지 메트릭 A와 B에 대해 서로 다른 값을 보고하는&4;가지 서비스가 있습니다. 한 경우에서는 모든 서비스에 메트릭 A가 가장 중요하고(Weight = High) 메트릭 B는 중요하지 않다고(Weight = Low) 정의합니다. 이 경우 Cluster Resource Manager는 메트릭 A가 메트릭 B보다 더 잘 분산되도록(표준편차가 낮음) 서비스를 배치합니다. 두 번째 경우는 메트릭 가중치를 반대로 합니다. 결과적으로 MetricA 보다 MetricB가 더 잘 분산된 할당을 만들기 위해 Cluster Resource Manager가 서비스 A와 B를 교환하게 됩니다.

### <a name="global-metric-weights"></a>전역 메트릭 가중치
그렇다면 ServiceA가 MetricA를 가장 중요하다고 정의하고 ServiceB가 이를 신경 쓰지 않는 경우 최종적으로 사용되는 실제 가중치는 무엇인가요?

모든 메트릭에 대해 실질적으로 여러 가중치가 추적됩니다. 첫 번째 집합은 메트릭에 대해 각각의 서비스를 정의하는 가중치입니다. 다른 가중치는 전역 가중치로, 해당 메트릭을 보고하는 모든 서비스의 평균입니다. Cluster Resource Manager는 솔루션의 점수를 계산할 때 두 가중치를 모두 사용합니다. 이것은 서비스가 자체 우선 순위에 따라 분산되는 것뿐 아니라 클러스터 전체가 정확하게 할당되는 것도 중요하기 때문입니다.

Cluster Resource Manager가 전역 및 로컬 분산에 대해 고려하지 않는다면 어떤 상황이 발생하나요? 전역적으로 분산된 솔루션의 구축에서는 큰 문제가 없지만 개별 서비스에 대한 리소스 할당이 제대로 되지 못하는 결과가 발생합니다. 다음 예제에서는 상태 저장 서비스가 구성된 기본 메트릭에서 전역 분산만 고려할 경우 어떻게 되는지 살펴보겠습니다.

<center>
![전역에만 해당하는 솔루션의 영향][Image4]
</center>

전역 균형 조정을 살펴본 맨 위 예제에서 전체 클러스터는 부하가 분산되었습니다. 모든 노드에서 기본 복제본의 수와 총 복제본의 수가 동일합니다. 그러나 이 할당의 실제 영향은 그렇게 좋지 않습니다. 기본 복제본을 모두 사용하기 때문에, 노드 손실이 특정 워크로드에 불균형을 초래합니다. 예를 들어, 첫 번째 노드가 실패할 경우 원형 서비스의&3;가지 파티션에 대한&3;가지 기본 복제본이 모두 손실될 수 있습니다. 반대로, 다른 두 가지 서비스(삼각형 및 육각형)를 사용하면 파티션에서 복사본을 손실하며 (아래쪽 복제본을 복구하는 것 이외의) 중단은 없습니다.

아래쪽 예제에서는 Cluster Resource Manager가 전역 및 서비스별 부하 분산에 따라 복제본을 배포했습니다. 솔루션의 점수를 계산할 때 대부분의 가중치를 전역 솔루션에 부여했고 (구성 가능한) 일부를 개별 서비스에 할당했습니다. 전역 부하 분산은 각 서비스에 대해 구성된 메트릭 가중치의 평균에 따라 계산됩니다. 각 서비스는 자체 정의된 메트릭 가중치에 따라 분산됩니다. 이렇게 하면 가능한 한 자체 필요에 따라 자체적으로 서비스가 분산됩니다. 결과적으로 같은 첫 번째 노드가 실패하면 기본(및 보조) 복제본의 손실이 모든 서비스의 모든 파티션에 배분됩니다. 각각에 대한 영향이 같습니다.

## <a name="next-steps"></a>다음 단계
* 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)에서 제공하는 다른 Cluster Resource Manager 구성을 참조하세요.
* 조각 모음 메트릭 정의는 노드의 부하를 분배하는 대신 통합하는 한 가지 방법입니다. 조각 모음을 구성하는 방법에 대해 알아보려면 [이 문서](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
* 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)
* 이동 비용은 특정 서비스가 다른 서비스에 비해 이동하는 데 비용이 더 많이 드는 것을 클러스터 리소스 관리자에게 알리는 한 가지 방법입니다. 이동 비용에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


