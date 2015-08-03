<properties
   pageTitle="리소스 분산 장치 서비스 설명"
   description="사용할 리소스 분산 장치에 대한 구성 서비스 설명에 대한 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# 서비스 설명 개요

## 배치 제약 조건
서비스에 대한 [배치 제약 조건](../service-fabric-placement-constraint)은 특정 서비스 인스턴스에서 필요한 노드 속성을 선택하는 메커니즘입니다. 노드 속성과 마찬가지로, 배치 제약 조건은 값에 대한 서비스의 요구 사항과 속성 이름을 설명하는 키/값 쌍입니다. 개별 문을 간단한 부울 논리로 함께 그룹화하여 필요한 제약 조건을 만들 수 있습니다. 서비스 패브릭 리소스 분산 장치는 제약 조건을 해석합니다.

서비스 배치 제약 조건은 서비스 또는 응용 프로그램 매니페스트를 통해 정의하거나 코드에서 직접 정의할 수 있습니다.

서비스 매니페스트는 ServiceTypes 정의를 제공합니다.

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

응용 프로그램 매니페스트는 ServiceTemplates 또는 DefaultServices 정의를 제공합니다.

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

다음 다이어그램의 노드가 있는 클러스터에서 유일하게 이 서비스 배치에 유효한 노드는 N5입니다.

![배치 제약 조건][Image1]

서비스가 노드에 정의되지 않은 속성에 대한 제약 조건을 지정하면 해당 노드는 지정된 값에 상관없이 일치로 간주되지 않습니다.

## 서비스 선호도
특수한 형태의 배치 제약 조건인 서비스 선호도는 다양한 이유로 인해 서로 로컬 종속성을 갖는(따라서 양쪽 서비스 모두 작동하려면 동일한 노드에서 실행되어야만 하는) 다른 서비스에 대한 설명을 가능하게 합니다. 이런 종류의 계층 구조가 서비스 패브릭 응용 프로그램에 일반적일 것으로 예상되지는 않지만 서비스 패브릭은 이것을 특정한 유형의 레거시 응용 프로그램에 대해 과도기적으로 가능한 계층 구조로 인식하며, 따라서 이 기능을 사용할 수 있도록 만듭니다.

두 서비스 간에 서비스 선호도를 만들면 이들 서비스의 주 복제본이 항상 같은 노드에 수집되도록 합니다.

선호도 관계는 부모-자식 계층 구조로 표시되며, 따라서 “부모"와 “자식"이 있습니다. 관계 내에서 첫 번째 수립되는 서비스는 부모이고, 두 번째 수립되는 서비스는 자식입니다. 관계는 “하드" 제약 조건으로 모델링됩니다.

서비스 선호도에는 현재 다음과 같은 제한 사항이 있습니다.

- 상태 비저장 및 상태 저장 서비스에 함께 사용될 수 없습니다.
- 인스턴스 개수가 다른 상태 비저장 서비스에 사용될 수 없습니다. 예를 들어 두 개의 상태 비저장 서비스가 각각 생성될 때 동일한 InstanceCount 속성을 가져야 합니다.
- 복제본 개수가 다른 상태 저장 일시 서비스 또는 지속 서비스에 함께 사용될 수 없습니다. 예를 들어, 두 가지 서비스 모두에 동일한 Target 및 Min ReplicaSetSizes 값이 지정되어 있어야 합니다.
- 분할된 서비스에 사용될 수 없습니다. 각 서비스에는 FABRIC_PARTITION_SCHEME_SINGLETON 파티션 체계가 있어야 합니다.
- 서비스 설명의 다른 속성과 같이, 선호도 관계는 서비스가 생성되면 설정되고, 수정될 수 없습니다.
- 연속적인 서비스는 허용되지 않습니다. 다수의 서비스를 선호도 관계로 가져와야 하는 경우, “스타" 모델을 사용해야 합니다.

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

코드 사례는 DefaultServices 정의의 대체 사용을 보여줍니다.

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

코드 사례는 포함하는 응용 프로그램이 생성된 후에 선호도 관계를 생성하는 방법을 보여줍니다.

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## 리소스 분산 메트릭
서비스 배치 제약 조건과 노드 속성은 어떤 서비스 배열이 유효한가를 설명하는 하드 규칙과 정책을 나타내는 한편 리소스 균형 조정 메트릭은 일반적으로 클러스터 내 서비스에 대한 최선의 배열을 설명하도록 돕습니다. 서비스에 의해 정의되는 메트릭에는 클러스터 내에서 속성이 사용되는 방법을 설명하는 몇 가지 다른 속성이 있습니다.

### 기본 메트릭 사용
첫 번째로 이해할 것은 리소스 메트릭을 다루는 것이 서비스에 대해 완전히 선택적이며, 기본적으로 서비스 패브릭 리소스 분산 장치는 일부 "기본 제공" 시스템 메트릭을 사용하여 서비스나 클러스터 관리자에게 조치를 취하도록 요청하지 않고도 기본적인 리소스 분산을 수행한다는 것입니다. 서비스 패브릭 리소스 분산 장치가 클러스터 내에서 관찰하고 조정하는 기본 메트릭은 PrimaryCount, ReplicaCount, Count입니다. PrimaryCount는 노드의 상태 저장 서비스 주 복제본의 개수입니다. ReplicaCount는 노드의 모든 상태 저장 복제본의 개수입니다. Count는 상태 비저장 및 상태 저장 서비스를 포함하는 노드의 모든 서비스 개체의 총 개수입니다. 일반적으로 PrimaryCount 메트릭은 가장 중요하게 간주되며 따라서 다음 섹션에 설명된 바와 같이 최고의 가중치를 갖습니다. PrimaryCount 메트릭 다음에는 ReplicaCount 메트릭이 뒤따르고, 마지막으로 Count 메트릭이 나옵니다.

다음 다이어그램은 각각의 복제본 세 개와 세 가지 상태 저장 서비스의 기본적인 분산의 사례를 보여줍니다.

![기본 메트릭][Image2]

일반적으로 기본 메트릭은 많은 서비스에 충분하며 추가적인 메트릭 또는 기능에 대한 특정 요구 사항이 있지 않는 한 사용되어야 합니다.

### 사용자 지정 메트릭 정의
기본 메트릭이 특정 서비스에 대해 충분하지 않거나 디스크 공간이나 메모리와 같은 특정 리소스 관련 요구 조건에 대해 알려진 요구 조건이 있는 경우에는 사용자 지정 메트릭이 사용되어야 합니다. 사용자 지정 메트릭은 서비스에 경합을 방지하기 위하여 균형을 잘 조정해야 하는 하나 이상의 리소스가 있는 경우에 유용합니다. 메트릭의 사용은 클러스터 내 복제본 사이에 상당히 다를 수 있습니다. 예를 들어, 주 복제본은 메트릭을 100% 사용하는 반면 다른 복제본은 20%만 사용할 수 있습니다. 사용자 지정 메트릭을 사용하여 메모리, 디스크 공간 또는 연결을 비롯하여 컴퓨터의 심각하게 제한된 리소스를 캡처하는 것이 유용합니다. 노드에서 상한이 없는 메트릭을 캡처하거나 나타내지만 서비스 관점에서 작업 및 리소스 사용(예: "현재 두드러진 transactions" 메트릭)을 나타내는 추가적인 사용자 지정 메트릭이 생성되는 경우도 있습니다. 노드의 “용량"에 대한 제한이 없을 수 있지만, 이 메트릭이 클러스터 전체에 고르고 배포되도록 하면 성능 이점이 있습니다.

서비스가 사용자 지정 메트릭을 정의하는 경우, 서비스는 기본 시스템 제공 메트릭을 사용하지 않습니다. 기본 메트릭은 여전히 사용될 수 있지만 생성 시 서비스의 메트릭 목록에 명시적으로 다시 포함되어야 합니다.

### 메트릭 이름
사용자 지정 메트릭은 서비스를 생성할 때 메트릭 이름을 정의하여 생성될 수 있습니다. 서비스 패브릭 리소스 분산 장치는 이름을 통해 메트릭과 연결되므로, 메트릭이 노드 정의 또는 서비스 내에서 용량으로 사용되는 경우에, 서비스 패브릭 리소스 분산 장치가 연관 지을 수 있도록 메트릭 이름이 정확히 일치해야 합니다.

### 메트릭 가중치
서비스가 다수의 메트릭을 정의하는 경우, 메트릭에 대한 가중치도 정의하는 것이 유용합니다. 다른 메트릭 가중치는 서비스 패브릭 리소스 분산 장치에게 서비스 기능을 위해 어떤 메트릭이 더 중요한지에 대한 지침을 제공합니다. 예를 들어, 메모리 내 큐는 네트워크 대역폭에 의해 영향을 받을 수 있지만 아마도 노드의 실제 메모리 사용에 의해 가장 많은 제약을 받을 것입니다. 따라서, 큐는 다수의 메트릭을 가질 수 있고, 메모리 사용을 나타내는 메트릭이 최고 가중치를 갖습니다. 유사하게, 영구 데이터베이스는 메모리와 디스크 사용에 의존하지만 제한된 메모리는 복잡한 쿼리를 처리할 수 있는 능력을 감소시키고 디스크 공간 부족은 더 이상의 저장소 작업을 막을 것이며, 이것은 보다 심각한 상황입니다. 따라서 영구적인 저장소는 디스크 공간을 가중치가 높은 메트릭으로 선택합니다.

가중치는 서비스 패브릭 리소스 분산 장치가 메트릭의 균형을 완전히 맞출 수 없는 경우에 서비스 패브릭 리소스 분산 장치에서만 사용되며, 따라서 하나의 메트릭이 다른 메트릭보다 전반적으로 덜 분산되는 솔루션을 찾아야 합니다. 이런 경우 가중치가 낮은 메트릭의 분산은 가중치가 높은 메트릭보다 우선 순위가 낮게 결정됩니다. 따라서 서비스 패브릭 리소스 분산 장치에는 가중치가 높은 메트릭의 분산 장치에 유리한 제안이 제시됩니다.

또한 서비스 패브릭 리소스 분산 장치는 다수의 서비스에 이름이 같은 메트릭이 정의된 것을 감지하고 그러한 메트릭은 동일한 메트릭으로 간주합니다. 메트릭은 동일하지만 가중치는 다른 경우, 리소스 분산 장치는 지정된 가중치의 평균을 내어 런타임 중에 사용될 실제 가중치를 결정합니다. 따라서, 서비스 하나에 가중치를 0으로 정의하면, 이것은 분산이 필요 없다는 것을 나타내지만 다른 서비스에서 동일한 메트릭을 가중치가 높은 것으로 정의하면 실제 가중치는 그 사이 값으로 마무리됩니다.

![메트릭 가중치][Image3]

이 사례는 두 개의 다른 메트릭 가중치 시나리오가 두 개의 다른 분산 결과(즉, 우선 순위가 상대적으로 높게 책정된 메트릭이 전체적으로 더 잘 배포된)로 나오는 방법을 보여줍니다. 왼쪽 예에서 B가 분산이 더 잘되어 있는 반면, 오른쪽은 A가 분산이 더 잘 되어 있습니다. 이 예에서 양 쪽 메트릭의 균형을 맞추는 분산 솔루션이 없기 때문에 서비스 패브릭 분산 장치는 메트릭의 가중치를 사용하여 무엇이 더 중요한지를 결정하고 따라서 분산을 수행하는 동안 선호할 메트릭이 무엇인지를 결정합니다.

서비스에서 메트릭을 조사하지만 이 메트릭을 기반으로 하는 분산을 필요로 하지 않는 경우 메트릭 가중치 0이 제공됩니다. 예를 들어, "노드 간 고른 사용률"을 위해 몇 개 노드에 명확한 제한이 있는 메트릭은 정상적인 리소스 분산 보장이 중요하지 않습니다. 분산을 필요로 하지 않지만 노드에서 제한 설정의 적용을 필요로 하는 이런 종류의 메트릭은, 분산 가중치가 0으로 정의될 수 있습니다. 런타임 중 서비스 패브릭 분산 장치는 용량을 강제 적용하지만 노드 전역에 걸쳐 메트릭 불균형이 매우 심하더라도 메트릭 분산을 사전에 시도하지 않습니다.

### 주 역할 또는 보조 역할을 위한 메트릭 기본 부하
서비스에 대한 메트릭을 정의할 때 서비스가 주 역할 또는 보조 역할인 경우에 대한 예상 사용을 제공합니다. 이 정보는 리소스 분산 장치가 효율적인 방법으로 서비스를 초기 배치하는데 도움이 될 뿐만 아니라 수명 기간 내내 메트릭에 대한 실제 서비스 사용과 유사한 추정치 역할을 할 수 있습니다. 서비스 패브릭 분산 장치는 서비스를 배치할 때뿐만 아니라 분산 또는 클러스터 내의 다른 변화를 위해 복제본을 이동시켜야 할 때마다 메트릭 소비를 자동으로 고려합니다. 서비스 부하가 예측 가능하고 안정적이면 이러한 값을 설정함으로써 서비스는 런타임 중에 부하를 보고해야 하는 상황을 피할 수 있습니다.

다음 코드 예제는 두 개의 사용자 지정 메트릭(메모리 사용을 위한 메트릭과 디스크 공간을 위한 메트릭)을 완전히 정의한 서비스를 보여줍니다.

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

코드 예제는 서비스 매니페스트를 통한 ServiceTypes 정의를 보여줍니다.

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

코드 예제는 응용 프로그램 매니페스트를 통한 ServiceTemplates 정의를 보여줍니다.

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
코드 예제는 응용 프로그램 매니페스트를 통한 DefaultServices 정의를 보여줍니다.``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

코드를 통해 런타임 시 부하를 보고하도록 서비스에 명시되어 있지 않는 한 기본 부하 값이 업데이트되지 않기 때문에 이 값은 "용량 예약" 리소스 분산 모델을 더 많이 구현하는데 사용될 수 있습니다. 예를 들어, 작업이 몇 개의 사이즈 버킷으로 나뉘고 알려진 수의 작업 단위가 노드에 언제든 배치될 수 있다면 "단위"의 사용자 지정 메트릭이 생성될 수 있고 단위와 관련하여 노드 용량 및 서비스 기본 부하가 정의됩니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

자세한 내용은 [리소스 분산 장치 아키텍처](service-fabric-resource-balancer-architecture.md), [배치 제약 조건](service-fabric-placement-constraint.md)을 참조하세요.

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png
 

<!---HONumber=July15_HO4-->