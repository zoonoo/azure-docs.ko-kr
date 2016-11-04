---
title: Azure 서비스 패브릭 클러스터 리소스 관리자를 사용한 메트릭 관리 | Microsoft Docs
description: 서비스 패브릭에서 메트릭을 구성하고 사용하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# 메트릭을 사용하여 서비스 패브릭에서 리소스 부하 및 소비 관리
메트릭은 서비스가 고려하고, 클러스터의 노드에서 제공하는 리소스에 대한 서비스 패브릭 내의 일반적인 용어입니다. 일반적으로 메트릭은 서비스의 성능을 다루기 위해 관리하려는 모든 항목입니다.

메모리, 디스크, CPU 사용량 등이 모두 메트릭의 예입니다. 앞서 언급한 항목이 관리되어야 하는 노드에서 실제 리소스에 해당하는 실제 메트릭인 리소스입니다. 메트릭은 응용 프로그램이 정의하고 리소스 사용의 일정 수준에 해당하는 "MyWorkQueueDepth"와 같은 논리 메트릭일 수도 있습니다(일반적으로 논리 메트릭임). 그렇지만 응용 프로그램이 실제로 알지 못하거나 측정 방법을 모를 수 있습니다. 사람들이 사용하는 대부분의 메트릭은 논리 메트릭입니다. 이에 대한 다양한 이유가 있지만 가장 일반적인 이유는 오늘날 대부분의 고객들이 서비스를 관리된 코드로 작성하기 때문이며 지정된 상태 비저장 서비스 인스턴스 또는 상태 저장 서비스 복제본 개체 내에서 실제 물리적 리소스의 소비를 측정하고 보고하는 데 실제로 상당히 까다롭기 때문입니다. 고유 메트릭 보고의 복잡성은 기본적으로 몇 가지 기본 메트릭을 제공하는 이유이기도 합니다.

## 기본 메트릭
시작하려고 하지만 어떤 리소스를 사용할지 또는 어떤 리소스가 중요한지에 대해 알지 못한다고 가정해 보겠습니다. 그러면 구현으로 이동하여 다음 메트릭을 지정하지 않고 서비스를 만듭니다. 이것으로 끝입니다. 몇 가지 메트릭을 선택해 드립니다. 사용자 고유의 메트릭 중 하나를 지정하지 않으면 사용자를 위해 PrimaryCount, ReplicaCount 및 (대략 깨달은) 횟수라는 기본 메트릭을 사용합니다. 아래 테이블에서는 각 서비스 개체와 연결된 이러한 각 메트릭에 대한 부하의 양을 보여 줍니다.

| 메트릭 | 상태 비저장 인스턴스 부하 | 상태 저장 보조 부하 | 상태 저장 기본 부하 |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| 개수 |1 |1 |1 |

네, 따라서 이러한 기본 메트릭을 사용하여 무엇을 가져올 수 있나요? 기본 워크로드에 작업을 잘 배포하게 됩니다. 아래 예제에서 세 개의 파티션과 세 개의 대상 복제본 세트 크기가 있는 하나의 상태 저장 서비스 및 세 개의 인스턴스가 있는 단일 상태 비저장 서비스를 만드는 경우 어떤 상황이 발생하는지 확인하면 다음과 같습니다.

![기본 메트릭으로 클러스터 레이아웃][Image1]

이 예제에서는 다음을 확인합니다

* 상태 저장 서비스에 대한 기본 복제본은 단일 노드에 저장되지 않습니다
* 동일한 파티션에 대한 복제본은 동일한 노드에 있지 않습니다
* 기본 복제본 및 보조 복제본의 총 수는 클러스터에 잘 배포됩니다
* 서비스 개체(상태 비저장 및 상태 저장)의 총 수는 각 노드에 균등하게 할당됩니다.

유용합니다.

이는 다음에 대해 생각하기 시작할 때까지 잘 작동합니다. 선택한 파티션 구성표가 시간에 따라 모든 파티션에서 완벽하게 고른 사용률을 보일 가능성은 무엇입니까? 이와 연결해서 지정된 서비스에 대한 부하가 시간에 따라 상수가 되거나 지금과 동일할 가능성은 있나요? 심각한 워크로드의 경우 모든 복제본이 동일할 가능성은 실제로 낮습니다. 따라서 클러스터를 최대한 활용하려면 사용자 지정 메트릭을 살펴볼 수 있습니다.

현실적으로 기본 메트릭으로 당연히 실행할 수 있지만 그렇게 하는 경우 (보고하는 작업이 적응형이 아니며 모두가 동일하다고 가정되기 때문에) 클러스터 사용률은 원하는 것보다 낮아집니다. 최악의 경우 성능 문제를 일으키는 일정이 지연된 노드를 발생시킬 수 있습니다. 다음에 다루게 될 사용자 지정 메트릭 및 동적 부하 보고서를 더 잘 수행할 수 있습니다.

## 사용자 지정 메트릭
물리적 및 논리적 메트릭이 모두 있을 수 있으며 사용자는 고유한 메트릭을 정의할 수 있다고 이미 설명했습니다. 잘하셨습니다. 방법이 무엇일까요? 실제로 매우 쉽습니다! 서비스를 만들 때 메트릭과 기본 초기 부하를 구성하기만 하면 됩니다. 서비스를 만드는 경우 지정된 서비스 인스턴스 기준으로, 일련의 메트릭 및 서비스가 소비할 것으로 예상되는 서비스 양을 나타내는 기본값을 구성할 수 있습니다.

사용자 지정 메트릭을 정의하기 시작하는 경우 서비스의 부하를 분산하는 데 사용하려면 기본 메트릭에 다시 명시적으로 추가해야 합니다. 기본 메트릭과 사용자 지정 메트릭 간의 관계를 명확하게 해야 하기 때문입니다. 아마도 기본 분포보다 메모리 소비 또는 WorkQueueDepth 방식을 더 고려할 수도 있습니다.

(기본 메트릭에 이외에)"메모리"라는 메트릭을 보고하는 서비스를 구성하려는 경우를 가정해 보겠습니다. 메모리의 경우 기본 측정 단위를 수행했으며 일반적으로 동일한 서비스의 보조 복제본이 5Mb를 차지하는 반면 해당 서비스의 기본 복제본이 일반적으로 20Mb를 차지한다고 하겠습니다. 메모리가 이 특정 서비스의 성능 관리 측면에서 가장 중요한 메트릭이지만 일부 노드 또는 장애 도메인의 손실이 그에 따라 기본 복제본을 과도하게 사용하지 않도록 기본 복제본의 부하를 적절하게 분산합니다. 이외에는 기본값을 사용합니다.

다음 항목을 수행합니다.

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(기본 메트릭을 사용하려는 경우 서비스를 만들 때 메트릭 컬렉션을 사용하거나 특별한 작업을 수행할 필요가 없습니다.)

고유의 메트릭을 정의하는 방법을 보여주었으므로 메트릭이 가질 수 있는 다양한 속성에 대해 살펴보겠습니다. 이미 보여주었지만 실제 의미에 대해 알아보겠습니다. 현재 메트릭이 가질 수 있는 네 개의 다른 속성이 있습니다.

* 메트릭 이름: 메트릭의 이름입니다. 리소스 관리자의 관점에서 클러스터 내의 메트릭에 대한 고유한 식별자입니다.
* 기본 부하: 기본 부하는 서비스가 상태 비저장 서비스인지 또는 상태 저장 서비스인지에 따라 다르게 표현됩니다.
  * 상태 비저장 서비스의 경우 각 메트릭에는 기본 부하라는 단일 속성만 있습니다.
  * 상태 저장 서비스의 경우 다음을 정의합니다.
    * PrimaryDefaultLoad: 해당 서비스가 이 메트릭에 대해 기본으로 행사하는 부하의 기본 크기입니다.
    * SecondaryDefaultLoad: 해당 서비스가 이 메트릭에 대해 보조 복제본으로 행사하는 부하의 기본 크기입니다.
* 무게: 이 메트릭이 이 서비스에 대해 구성된 다른 메트릭에 가지는 중요도입니다.

## 로드
부하는 지정된 메트릭이 지정된 노드에서 일부 서비스 인스턴스 또는 복제본에 의해 소비되는 양의 일반적인 개념입니다.

## 기본 부하
기본 부하는 클러스터 리소스 관리자가 각 서비스 인스턴스 또는 이 서비스의 복제본에서 실제 서비스 인스턴스 또는 복제본의 업데이트를 수신할 때까지 사용한다고 가정해야 하는 부하의 양입니다. 간단한 서비스의 경우 동적으로 업데이트되지 않고 따라서 서비스의 수명 동안 사용할 수 있는 정적 정의가 됩니다. 특정 워크로드에 특정 리소스를 지정하는 작업은 익숙하게 수행해왔기 때문에 간단한 용량 계획을 정확히 수행합니다. 이점은 적어도 이제 마이크로 서비스 사고 방식으로 작업하여 리소스가 특정 작업 부하에 실제로 정적으로 할당되지 않고 사람들이 의사 결정 루프에 머물지 않는다는 점입니다.

상태 저장 서비스가 기본 복제본 및 보조 복제본에 기본 부하를 지정할 수 있습니다. 현실적으로 상당수의 서비스에서 이러한 숫자가 기본 복제본과 보조 복제본에 의해 수행되는 다른 워크로드로 인해 달라지고 기본 복제본이 일반적으로 읽기 및 쓰기를 모두 제공(뿐만 아니라 많은 컴퓨팅 부담도 있음)하므로 기본 복제본에 대한 기본 부하가 보조 복제본보다 높기 때문입니다

하지만 서비스를 한동안 실행했고 서비스의 일부 인스턴스 또는 복제본이 다른 인스턴스 또는 복제본보다 더 많은 리소스를 사용하거나 시간에 따라 소비량이 달라진다고 가정하겠습니다. 아마도 해당 항목이 특정 고객과 연결되어 있거나 메시징 트래픽, 전화 통화 또는 주식 거래와 같이 하루에 따라 달라지는 워크로드에 해당할 수 있습니다. 어쨌든 적어도 특정 기간에 상당한 양에도 꺼지지 않고 부하에 사용할 수 있는 "단일 숫자"는 없습니다. 또한 초기 예상 결과에서 "꺼짐"은 클러스터 Resource Manager가 서비스에 초과 또는 미만으로 리소스를 할당하게 되며, 결과적으로 노드가 초과 또는 미달 수준으로 활용되게 됩니다.

그렇다면 어떻게 해야 할까요? 물론 서비스가 즉석에서 부하를 보고할 수 있습니다!

## 동적 부하
동적 부하 보고서를 사용하면 복제본 또는 인스턴스가 수명에 걸쳐 클러스터에서 메트릭의 해당 할당/보고된 사용을 조정할 수 있습니다. 사용 중인 복제본 또는 인스턴스 보고서는 많은 리소스를 사용한다고 보고하는 반면 콜드 상태이고 작업을 수행하지 않는 서비스 복제본 또는 인스턴스는 일반적으로 적은 양의 지정된 메트릭을 사용 중이라고 보고합니다. 이 클러스터의 일반적인 변동 수준을 통해 서비스 복제본 및 인스턴스가 필요한 리소스를 가져오도록 하기 위해 클러스터에서 서비스 복제본 및 인스턴스를 즉시 다시 구성할 수 있습니다. 이 영향으로 사용 중인 서비스가 현재 콜드 또는 더 적은 작업을 수행하는 복제본 또는 인스턴스에서 리소스를 회수할 수 있게 됩니다. 즉석에서 부하를 보고하는 작업은 Reliable Services 프로그래밍 모델을 통해 기본 StatefulService 또는 StatelessService 클래스에서 속성으로 사용할 수 있는 ServicePartition에서 사용 가능한 ReportLoad 메서드를 통해 수행할 수 있습니다. 서비스 내의 코드는 다음과 같습니다.

코드:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("metric1", 42) });
```

서비스 복제본 또는 인스턴스는 메트릭에 대해 부하가 사용되도록 구성되었다고 보고할 수 있습니다. 메트릭 목록은 각 서비스를 만들 때 설정되며 나중에 업데이트할 수 있습니다. 서비스 복제본 또는 인스턴스가 메트릭에 대한 부하가 현재 사용되도록 구성되지 않았다고 보고하려는 경우 서비스 패브릭 보고서는 보고서를 기록하지만 무시하며 이는 클러스터의 상태에 대해 계산하거나 보고하는 경우 사용하지 않겠다는 의미입니다. 큰 실험에서 허용되기 때문에 깔끔합니다. 코드는 방법을 알고 있는 모든 것에 대해 측정하고 보고할 수 있습니다. 또한 연산자는 코드를 변경하지 않고 즉시 해당 서비스에 대한 규칙을 분산하는 리소스의 부하를 분산하고 조정하며 업데이트할 수 있습니다. 예를 들어, 버그가 있는 보고서로 메트릭을 비활성화하거나 동작을 기반으로 메트릭의 가중치를 다시 구성하거나 코드가 이미 배포되고 다른 메커니즘을 통해 유효성을 검사한 후에 새 메트릭을 사용하는 작업을 포함할 수 있습니다.

## 기본 부하 값 및 동적 부하 보고서 혼합
부하를 동적으로 보고하려는 서비스에 지정된 기본 부하가 있는 것이 합리적인가요? 그렇습니다. 이 경우에 실제 보고서가 실제 서비스 복제본 또는 인스턴스에서 표시되기 시작할 때까지 기본 부하는 예상치로 사용됩니다. 이를 통해 클러스터 Resource Manager는 유용한 정보를 얻게 됩니다. 기본 부하 예상치를 사용하면 서비스 인스턴스 또는 복제본을 처음부터 적절한 위치에 배치할 수 있게 됩니다. 기본 부하 정보가 제공되지 않으면 생성 시에 서비스의 배치가 실제로 무작위로 진행되며, 나중에 로드가 변경될 경우 클러스터 Resource Manager는 거의 확실하게 항목을 이동해야 합니다.

이전 예제를 사용하여 일부 사용자 지정 부하를 추가한 다음 서비스를 만든 후에 업데이트를 동적으로 가져올 때 어떻게 되는지 확인합니다. 이 예제에서는 "메모리"를 예로 사용하고 다음 명령을 사용하여 상태 저장 서비스를 처음 만들었다고 가정하겠습니다.

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

이 구문은 이전에 다루었지만(MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad) 가중치에 대한 특정 값의 의미는 나중에 자세히 설명합니다.

가능한 클러스터 레이아웃의 모양을 살펴보겠습니다.

![기본 및 사용자 지정 메트릭으로 클러스터 부하 분산][Image2]

주목할 만한 몇 가지가 있습니다.

* 복제본 또는 인스턴스가 고유한 부하를 보고할 때까지 서비스의 기본 부하를 사용하기 때문에 상태 저장 서비스의 파티션 1 내부에 있는 복제본은 자체적으로 부하를 보고하지 않았습니다.
* 파티션 내 보조 복제본은 고유한 부하를 가질 수 있습니다.
* 전반적인 메트릭이 매우 훌륭합니다. 1.75(메모리에 대한 최대 노드를 가진 노드는 N3이며 최소는 N2, 또한 28/16 = 1.75임)의 요인인 노드(메모리의 경우 - 가장 고려한다고 언급한 사용자 지정 메트릭)의 최대 및 최소 부하 간의 차이도 균형 잡혀 있습니다.

설명해야 하는 몇 가지가 있습니다

* 1\.75의 비율이 합리적인지를 무엇이 결정했나요? 충분한지 아니면 수행할 작업이 있는지 어떻게 아나요?
* 언제 부하가 분산되나요?
* 메모리의 가중치가 "높음"은 무슨 의미인가요?

## 메트릭 가중치
메트릭 가중치는 두 개의 서비스가 동일한 메트릭을 보고하도록 하지만 해당 메트릭의 부하를 분산하는 중요성을 다르게 볼 수 있습니다. 예를 들어 메모리 내 분석 엔진 및 영구 데이터베이스를 가정합니다. 아마도 둘 다 "메모리" 메트릭을 고려하지만 메모리 내 서비스는 "디스크" 메트릭을 신경 쓰지 않을 것입니다. 일부를 소비할 수 있지만 서비스의 성능에 중요하지 않기 때문입니다. 따라서 보고조차 하지 않을 수 있습니다. 클러스터 Resource Manager가 클러스터에서 실제 소비를 추적하고 노드가 용량을 초과하지 않도록 하는 것이므로 다른 서비스에서 동일한 메트릭을 추적할 수 있는 편이 유용합니다.

메트릭 가중치를 통해 클러스터 Resource Manager는 완벽한 응답이 없을 때 클러스터의 부하를 분산하는 방법에 대한 결정을 내릴 수 있습니다(시간이 오래 걸림). 메트릭은 0, 낮음, 보통, 높음 등 네 가지 가중치 수준을 사용할 수 있습니다. 가중치가 0인 메트릭은 부하가 분산되는지 여부를 고려하는 경우 영향을 주지 않지만 해당 부하는 용량 측정과 같은 항목에 영향을 줍니다.

클러스터의 다른 메트릭 가중치는 다른 서비스를 정렬하는 경우 영향을 미칩니다. 클러스터 Resource Manager에서 언급했듯이 집계에서 특정 메트릭이 다른 메트릭보다 더 중요합니다. 이러한 사실을 알기 때문에 다른 가중치를 갖는 메트릭이 다른 메트릭과 충돌할 때 클러스터 Resource Manager는 더 높은 가중치가 부여되는 메트릭의 부하를 더 잘 분산하는 솔루션을 선호할 수 있습니다.

일부 부하 보고서의 간단한 여제 및 클러스터에서 다른 할당에 대한 여러 메트릭 가중치의 결과를 살펴보겠습니다. 이 예제에서 메트릭의 상대적 가중치를 전환하면 서비스의 다른 계약을 만들어서 특정 솔루션을 참조하는 리소스 관리자를 발생시킵니다.

![메트릭 무게 예제 및 부하 분산 솔루션에 미치는 영향][Image3]

이 예제에는 네 개의 서로 다른 서비스가 있으며 모두 두 개의 다른 메트릭 A와 B에 대해 보고합니다. 어떤 경우에 모든 서비스는 메트릭 A를 가장 중요하며(가중치 = 높음) MetricB를 비교적 중요하지 않은 것으로(가중치 = 낮음) 정의하고 실제로 클러스터 Resource Manager는 MetricA가 MetricB보다 잘 분산되도록(낮은 표준 편차를 가짐) 서비스를 배치합니다. 두 번째 경우에 메트릭 가중치를 취소하면 MetricA 보다 MetricB의 부하가 잘 분산된 할당을 만들기 위해 클러스터 Resource Manager가 서비스 A와 B를 교환하는 것을 확인할 수 있습니다.

### 전역 메트릭 가중치
그렇다면 ServiceA가 MetricA를 가장 중요하다고 정의하고 ServiceB가 이를 신경 쓰지 않는 경우 최종적으로 사용되는 실제 가중치는 무엇인가요?

모든 메트릭에 대해 추적하는 두 개의 가중치가 있으며 이는 서비스 자체가 정의된 가중치 및 해당 메트릭을 고려하는 모든 서비스에 걸친 모든 전역 평균 가중치입니다. 생성한 솔루션의 점수를 계산할 때 서비스가 자체 우선 순위와 관련해서 모두 균형을 이룰 뿐만 아니라 전체 클러스터가 올바르게 할당되는 것이 중요하기 때문에 이러한 두 가지 가중치를 모두 사용합니다.

전역 및 지역 분산에 대해 고려하지 않는다면 어떤 상황이 발생하나요? 물론 전역적으로 분산된 솔루션을 구축하는 작업은 사소하지만 이로 인해 개별 서비스에 대한 균형 및 리소스 할당이 무너집니다. 아래 예제에서는 상태 저장 서비스를 구성하는 기본 메트릭, PrimaryCount, ReplicaCount 및 Count를 고려하고 전역 분산을 고려하는 경우 발생하는 일을 확인하겠습니다.

![전역에만 해당하는 솔루션의 영향][Image4]

글로벌 균형 조정을 살펴본 맨 위 예제에서 전체 클러스터는 부하가 분산되어 모든 노드에는 동일한 수의 기본 및 총 복제본이 있습니다. 그러나 이 할당의 실제 영향은 좋지 않습니다. 기본 복제본을 모두 사용하기 때문에 노드의 손실이 특정 워크로드에 불균형적으로 영향을 주게 됩니다. 예를 들어 첫 번째 노드를 손실하게 되는 경우를 봅니다. 이 경우 원형 서비스의 세 가지 다른 파티션에 세 가지 기본 복제본은 동시에 손실될 수 있습니다. 반대로, 다른 두 가지 서비스(삼각형 및 육각형)를 사용하면 파티션에서 복사본을 손실하며 (아래쪽 복제본을 복구하는 것 이외의) 중단은 없습니다.

아래 예제에서는 전역 및 서비스별 부하 분산에 따라 복제본을 배포했습니다. 솔루션의 점수를 계산할 때 대부분 가중치를 전역 솔루션에 제공하지만 어떤 (구성 가능한)부분은 서비스가 가능한 한 자체 내에서 분산되도록 작동합니다. 결과적으로 동일한 첫 번째 노드를 손실했다면 기본 복제본(및 보조 복제본)의 손실이 모든 서비스의 파티션 전체에 분산되고 각각에 동일한 영향을 줍니다.

메트릭 가중치를 고려해서 전역 부하 분산은 각 서비스에 대해 구성된 메트릭 가중치의 평균에 따라 계산됩니다. 고유하게 정의된 메트릭 가중치와 관련하여 서비스를 부하를 분산합니다.

## 다음 단계
* 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)에서 다른 클러스터 Resource Manager 구성에 대한 항목을 확인하세요.
* 조각 모음 메트릭 정의는 노드의 부하를 분배하는 대신 통합하는 한 가지 방법입니다. 조각 모음을 구성하는 방법에 대해 알아보려면 [이 문서](service-fabric-cluster-resource-manager-defragmentation-metrics.md)를 참조하세요.
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)에 대한 문서를 확인하세요.
* 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)
* 이동 비용은 특정 서비스가 다른 서비스에 비해 이동하는 데 비용이 더 많이 드는 것을 클러스터 리소스 관리자에게 알리는 한 가지 방법입니다. 이동 비용에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)를 참조하세요.

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0824_2016-->