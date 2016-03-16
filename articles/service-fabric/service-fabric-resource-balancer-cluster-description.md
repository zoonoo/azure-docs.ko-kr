<properties
   pageTitle="리소스 분산 장치 클러스터 설명 | Microsoft Azure"
   description="리소스 분산 장치에 대해 장애 도메인, 업그레이드 도메인, 노드 속성, 노드 용량을 지정하여 서비스 패브릭 클러스터를 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# 서비스 패브릭 클러스터 설명

Azure 서비스 패브릭의 리소스 분산 장치는 클러스터를 설명하는 몇 가지 메커니즘을 제공합니다. 런타임 동안 리소스 분산 장치는 이 정보를 사용하여 클러스터 리소스의 활용도를 극대화하고 클러스터에서 실행되는 서비스의 높은 가용성을 보장할 수 있는 방식으로 서비스를 배치합니다. 클러스터를 설명하는 리소스 분산 장치 기능은 장애 도메인, 업그레이드 도메인, 노드 속성, 노드 용량입니다. 또한 리소스 분산 장치에는 성능을 조정할 수 있는 몇 가지 구성 옵션이 있습니다.

## 주요 개념

### 장애 도메인

장애 도메인은 클러스터 관리자가 전력 및 네트워킹 소스와 같이 공유하는 물리적 종속성으로 인하여 동시에 장애를 경험할 가능성이 있는 물리 노드를 정의할 수 있도록 합니다. 장애 도메인은 대체적으로 장애 도메인 트리 내의 상위 지점에서부터 함께 장애를 겪을 가능성이 있는 더 많은 노드를 포함하는 공유 종속성과 관련된 계층 구조를 나타냅니다. 다음 그림은 계층적 장애 도메인을 통해 데이터 센터, 랙, 블레이드 순으로 구성된 몇 가지 노드를 보여줍니다.

![장애 도메인을 통해 구성된 노드][Image1]

 런타임 중에 Azure 리소스 관리자는 지정된 서비스의 모든 복제본이 각각 별도의 장애 도메인에 위치하도록 클러스터 내의 장애 도메인을 고려하여 서비스 복제본을 퍼트리려고 시도합니다. 이 프로세스는 장애 도메인 중 하나에 실패가 발생하는 경우, 해당 서비스의 가용성을 확보하고 서비스 상태가 손상되지 않았음을 확인하는데 도움이 됩니다. 다음 그림은 서비스 복제본을 한두 개의 도메인에 몰아서 배치해도 될 만큼 충분한 공간이 있지만 여러 장애 도메인에 퍼트려 놓은 서비스 복제본을 보여줍니다.

![장애 도메인에 분산된 서비스 복제본][Image2]

장애 도메인은 클러스터 매니페스트 내에 구성됩니다. 각 노드는 특정 장애 도메인 내에 위치하도록 정의됩니다. 런타임 중에 리소스 관리자는 모든 노드에서 보내온 보고서를 결합하여 시스템의 모든 장애 도메인에 대한 요약 보고서를 작성합니다.

### 업그레이드 도메인

업그레이드 도메인은 리소스 관리자가 사용하는 또 다른 정보입니다. 업그레이드 도메인은 장애 도메인과 같이 업그레이드를 위해 거의 같은 시간에 종료된 노드를 설명합니다. 업그레이드 도메인은 계층적이지 않으며 태그로 생각할 수 있습니다.

장애 도메인은 클러스터 내 노드의 물리적인 레이아웃을 통해 정의할 수 있지만 업그레이드 도메인은 정책을 기반으로 클러스터 관리자가 결정합니다. 정책은 클러스터 내 업그레이드와 관련이 있습니다. 업그레이드 도메인이 많으면 실행 중인 클러스터와 서비스에 미치는 영향을 제한하고 장애 발생 시 여러 서비스에 영향을 미치지 않도록 방지하여 보다 세분화된 업그레이드를 수행할 수 있습니다. 또한 업그레이드 도메인이 많으면 다른 정책(예: 서비스 패브릭이 도메인 업그레이드 후 다음 도메인으로 이동하기 전까지 대기해야 하는 시간)에 따라 클러스터 내 업그레이드를 완료하는 시간을 늘릴 수 있습니다.

이러한 이유로 리소스 관리자는 장애 도메인과 마찬가지로 업그레이드 도메인 정보를 수집하고 클러스터 내 업그레이드 도메인에 복제본을 배포합니다. 업그레이드 도메인은 장애 도메인과 일대일로 통신을 하거나 하지 않을 수 있지만, 일반적으로는 일대일 통신을 하지 않을 것으로 예상됩니다. 다음 그림은 이전에 정의된 장애 도메인 위의 몇 가지 업그레이드 도메인 계층을 보여줍니다. 리소스 관리자는 클러스터 내에서 업그레이드나 장애가 있더라도 서비스의 높은 가용성을 확보하기 위하여 장애 도메인 또는 업그레이드 도메인에 복제본이 집중되지 않도록 모든 도메인에 복제본을 배포합니다.

![업그레이드 도메인][Image3]

업그레이드 도메인과 장애 도메인 모두 아래와 같이 클러스터 매니페스트 내에서 노드 정의의 일부로 구성됩니다.

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
> [AZURE.NOTE]Azure 배포에서 장애 도메인과 업그레이드 도메인은 Azure에 의해 할당되기 때문에 Azure에 대한 인프라 옵션 내 노드 및 역할 정의에 장애 도메인 또는 업그레이드 도메인 정보가 포함되지 않습니다.

### 노드 속성
노드 속성은 주어진 노드에 대한 여분의 메타데이터를 제공하는 사용자 정의 키/값 쌍입니다. 노드 속성의 예로는 노드에 하드 드라이브 또는 비디오 카드가 있는지 여부, 하드 드라이브, 코어에 포함된 스핀들 수, 기타 물리 속성이 포함됩니다.

노드 속성은 정책 결정을 배치하는데 도움을 주기 위한 요약 속성을 지정하는데 사용될 수도 있습니다. 예를 들면, 클러스터 내 몇 개의 노드에 클러스터를 다른 섹션으로 구분하는 수단으로 “color(색)”이 할당될 수 있습니다. 아래의 코드 예제는 노드 유형 정의의 일부로 클러스터 매니페스트를 통해 노드에 대한 노드 속성이 정의되는 것을 보여줍니다. 그 후 이 노드 속성을 클러스터의 여러 노드에 적용할 수 있습니다.

NodeName, NodeType, FaultDomain, 그리고 UpgradeDomain 배치 속성에는 기본값이 있습니다. 서비스 패브릭은 사용자가 서비스를 만들 때 사용할 수 있도록 기본값을 자동으로 제공합니다. 사용자는 이것과 동일한 이름으로 자신의 배치 속성을 지정하지 말아야 합니다.

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

런타임 중에 리소스 분산 장치는 노드 속성 정보를 사용하여 특정 기능을 필요로 하는 서비스가 적절한 노드에 배치되도록 보장합니다.

### 노드 용량
노드 용량은 특정 노드가 사용할 수 있는 특정 리소스의 이름과 양을 정의하는 키/값 쌍입니다. 아래의 코드 예제는 "MemoryInMB"라는 메트릭에 대한 용량을 소유하는 동시에 기본값으로 사용할 수 있는 메모리가 2,048MB인 노드를 보여줍니다. 용량은 노드 속성과 매우 유사하게, 클러스터 매니페스트를 통해 정의됩니다.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![노드 용량][Image4]

노드에서 실행되는 서비스는 부하를 보고하여 용량 요구 사항을 업데이트할 수 있습니다. 따라서 리소스 분산 장치는 노드가 메트릭의 용량을 초과하는지 주기적으로 확인합니다. 만약 초과할 경우 리소스 분산 장치는 리소스 경합을 줄이고 전반적인 성능과 사용률을 향상시키기 위해 부하가 적은 노드로 서비스를 이동할 수 있습니다.

특정 메트릭을 노드 유형의 속성 섹션에 표시할 수도 있지만 런타임 중에 사용할 수 있는 노드의 속성인 경우에는 용량으로 표시하는 것이 좋습니다. 특정 메트릭을 추가적으로 속성으로 표시하면 "최소 하드웨어 요구 사항"에 의존하는 서비스가 배치 제약 조건이 있는 노드를 쿼리할 수 있습니다. 런타임 중에 다른 서비스에서 리소스를 사용하는 경우 이 조치가 필요할 수 있습니다. 리소스 분산 장치가 추가 조치를 취할 수 있도록 특정 메트릭을 용량으로 포함할 것을 권장합니다.

새 서비스가 생성되면 서비스 패브릭 리소스 분산 장치는 기존 노드의 용량 및 기존 서비스의 사용량에 대한 정보를 사용하여 새 서비스 전체를 배치하기에 용량이 충분한지 여부를 판단합니다. 용량이 충분하지 않으면 용량이 부족하다는 오류 메시지와 함께 서비스 만들기 요청이 거부됩니다.


### 리소스 분산 장치 구성

클러스터 매니페스트 내에는 리소스 분산 장치의 전반적인 동작을 정의하는 다음과 같은 구성값이 있습니다.

*분산 임계값*은 특정 메트릭과 관련하여 리소스 분산 장치가 대응을 시작하는 클러스터의 불균형 상태를 제어합니다. 분산 임계값은 리소스 분산 장치에서 허용하는 최대 사용 노드와 최소 사용 노드 사이의 최대 비율로, 이 값을 초과하면 리소스 분산 장치가 클러스터 균형 조정을 시작합니다.

다음 그림은 주어진 메트릭에 대한 분산 임계값이 10인 두 가지 예를 보여줍니다.

![분산 임계값][Image5]

이런 경우 노드 "사용률"은 노드 용량에 의해 결정되는 노드의 크기를 고려하지 않고 지정된 메트릭에 대해 노드에 현재 보고되는 절대 사용량만을 고려합니다.

다음 코드 예제는 메트릭에 대한 분산 임계값이 클러스터 매니페스트 내의 FabricSettings 요소를 통해 메트릭마다 구성되는 것을 보여줍니다.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

*활동 임계값*은 상당한 절대 부하량이 존재할 때 리소스 분산 장치가 대응하는 상황을 제한함으로써 리소스 분산 장치의 실행 빈도를 조절하는 역할을 합니다. 이런 방식으로 특정 메트릭 때문에 클러스터에 걸리는 부하가 매우 높지만 않으면 클러스터 내 몇몇 메트릭이 매우 불균형하더라도 리소스 분산 장치가 실행되지 않습니다. 이러한 조치는 실질적으로 득이 없는 클러스터 균형 조정으로 인한 리소스 낭비를 방지합니다. 다음 그림에서 메트릭에 대한 분산 임계값은 4이며 활동 임계값은 1536입니다.

![활동 임계값][Image6]

활동 임계값과 분산 임계값이 동일한 메트릭을 모두 초과해야만 리소스 분산 장치가 실행됩니다. 별개의 메트릭 두 개를 트리거해도 리소스 분산 장치가 실행되지 않습니다.

다음 코드 예제는 분산 임계값과 마찬가지로 클러스터 매니페스트 내 FabricSettings 요소를 통해 메트릭마다 활동 임계값을 구성하는 방법을 보여줍니다.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval – 리소스 분산 장치가 제약 조건 위반을 확인하기 위해 정보를 스캔하는 빈도를 제어합니다. 제약 조건 위반에는 충족되지 않은 배치 제약 조건, 인스턴스 또는 복제본의 수가 필요한 양보다 적은 서비스, 일부 메트릭의 용량을 초과하는 노드, 과부하가 걸린 장애 또는 업그레이드 도메인 또는 분산 임계값이 포함됩니다. 리소스 분산 장치는 분산 임계값 및 활동 임계값과 클러스터 내 노드의 부하에 대한 현재 정보를 사용하여 제약 조건 위반을 식별합니다. 새로 고침 간격은 초 단위로 정의되며 기본값은 1입니다. 제약 조건 확인 및 배치 빈도는 두 가지 새로운 간격(MinConstraintCheckInterval 및 MinPlacementInterval)으로 제어할 수 있습니다. 새 매개 변수가 정의되면 PLBRefreshInterval은 사용되지 않고 정의할 수 없습니다.

- MinConstraintCheckInterval - 분산 제어 장치가 제약 조건 위반에 대해 확인해야 하는 정보를 스캔하는 빈도를 결정합니다. 제약 조건 위반에는 충족되지 않은 배치 제약 조건, 인스턴스 또는 복제본의 수가 필요한 양보다 적은 서비스, 일부 메트릭의 용량을 초과하는 노드, 과부하가 걸린 장애 또는 업그레이드 도메인 또는 분산 임계값이 포함됩니다. 리소스 분산 장치는 분산 임계값 및 활동 임계값과 클러스터 내 노드의 부하에 대한 현재 정보를 사용하여 제약 조건 위반을 식별합니다. 제약 조건 확인 간격은 초 단위로 정의됩니다. 제약 조건 확인 간격이 정의되지 않으면 기본값은 PLBRefreshInterval의 값과 동일합니다. 두 값은 동시에 지정할 수 없습니다.

- MinPlacementInterval – 분산 제어 장치가 배치가 필요한 새 인스턴스 또는 복제본이 있는지를 확인하고 배치를 시도하는 빈도를 결정합니다. 배치 간격은 초 단위로 정의됩니다. 배치 간격이 정의되지 않으면 기본값은 PLBRefreshInterval의 값과 동일합니다. 두 값은 동시에 지정할 수 없습니다.

- MinLoadBalancingInterval – 리소스 분산 라운드 사이의 최소 시간을 설정합니다. 리소스 분산 장치가 마지막 PLBRefreshInterval 간격 중에 수행된 스캔을 통해 확보한 정보를 기반으로 조치를 취하는 경우 적어도 이 시간 동안은 다시 실행되지 않습니다. 초 단위로 정의되며 기본값은 5입니다.

이러한 값은 적극적 조치이지만, 특정 메트릭에 대한 분산 임계값과 활동 임계값이 만족되는 경우에만 클러스터 내에서 전반적인 부하 분산이 발생합니다. 다음 코드 예제는 특정 클러스터에 대해 정확하고 적극적인 부하 분산이 필요하지 않는 경우에는 FabricSettings 요소 내의 구성을 통해 이러한 값을 약간 소극적으로 설정할 수 있음을 보여줍니다. 이 구성 예에서 두 제약 조건 확인 사이의 최소 시간은 10초, 최소 배치 시간은 5초인 반면, 부하 분산은 5분마다 발생합니다. 이런 경우 PLBRefreshInterval은 정의되어 있지 않습니다.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

아래의 두 번째 구성 예에서는 PLBRefreshInterval 및 MinLoadBalancingInterval이 정의되어 있습니다. PLBRefreshInterval이 2초로 설정되어 있기 때문에 MinPlacementInterval 및 MinConstraintCheckInterval 양쪽 모두 각각의 값을 2초로 설정합니다.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

자세한 내용은 [리소스 분산 장치 아키텍처](service-fabric-resource-balancer-architecture.md)를 참조하세요.


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png

<!---HONumber=AcomDC_1223_2015-->