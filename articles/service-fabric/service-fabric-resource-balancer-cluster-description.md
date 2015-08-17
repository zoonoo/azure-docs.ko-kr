<properties
   pageTitle="리소스 분산 장치 클러스터 설명"
   description="리소스 분산 장치에 대한 클러스터 설명 지정"
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

# 클러스터 설명

서비스 패브릭 리소스 분산 장치는 클러스터를 설명하는 몇 가지 메커니즘을 제공합니다. 런타임 동안 리소스 분산 장치는 클러스터 리소스의 최대 사용을 보장하는 한편 클러스터 내에 실행되는 서비스의 높은 가용성을 확보할 수 있는 방식으로 서비스를 배치하기 위하여 이 정보를 사용합니다. 클러스터를 설명하는 리소스 분산 장치 기능은 장애 도메인, 업그레이드 도메인, 노드 속성, 노드 용량입니다. 또한 리소스 분산 장치에는 성능을 조정할 수 있는 몇 가지 구성 옵션이 있습니다.

## 주요 개념

### 장애 도메인:

장애 도메인은 클러스터 관리자가 전력 및 네트워킹 소스와 같이 공유하는 물리적 종속성으로 인하여 동시에 장애를 경험할 가능성이 있는 물리 노드를 정의할 수 있도록 합니다. 장애 도메인은 대체적으로 장애 도메인 트리 내의 상위 지점에서부터 함께 장애를 겪을 가능성이 있는 더 많은 노드를 포함하는 공유 종속성과 관련된 계층 구조를 나타냅니다. 다음 그림은 계층적 장애 도메인을 통해 데이터 센터, 랙, 블레이드 순으로 구성된 몇 가지 노드입니다.

![장애 도메인][Image1]

 런타임 중에, 서비스 패브릭 리소스 분산 장치는 클러스터 내의 장애 도메인을 고려하여 지정된 서비스의 복제본을 개별적인 장애 도메인에 모두 두기 위하여 서비스 복제본을 퍼트리기 위한 시도를 합니다. 이 프로세스는 장애 도메인 중 하나에 실패가 발생하는 경우, 해당 서비스의 가용성을 확보하고 서비스 상태가 손상되지 않았음을 확인하는데 도움이 됩니다. 다음 그림은 비록 한두 개의 도메인에 집중시키기에 충분한 공간이 있지만 몇 개의 장애 도메인에 퍼져있는 서비스 복제본을 보여줍니다.

![장애 도메인][Image2]

장애 도메인은 클러스터 매니페스트 내에 구성됩니다. 각 노드는 특정 장애 도메인 내에 위치하도록 정의됩니다. 런타임 중에 리소스 관리자는 모든 노드에서 보내온 보고서를 결합하여 시스템에 포함된 모든 장애 도메인의 완전한 개요를 개발합니다.

### 업그레이드 도메인

업그레이드 도메인은 리소스 관리자가 사용하는 또 다른 정보입니다. 업그레이드 도메인은 장애 도메인과 같이 업그레이드를 위해 거의 같은 시간에 종료된 노드를 설명합니다. 업그레이드 도메인은 계층적이지 않으며 태그로 생각할 수 있습니다.

장애 도메인은 클러스터 내 노드의 물리적인 레이아웃에 의해 정의될 수 있지만 업그레이드 도메인은 정책을 기반으로 하는 클러스터 관리자에 의해 결정됩니다. 정책은 클러스터 내 업그레이드와 관련이 있습니다. 보다 많은 업그레이드 도메인에서, 실행 중인 클러스터와 서비스에 대한 영향을 제한하고, 다수의 서비스에 영향을 미치는 작업이 실패하는 것을 방지하기 위하여 보다 세분화된 업그레이드를 수행합니다. 다른 정책(예: 서비스 패브릭이 다음 도메인으로 이동하기 전에 서비스 패브릭이 도메인 업그레이드 후 대기해야 하는 시간)에 따라서 더 많은 업그레이드 도메인이 클러스터 내 업그레이드 완료에 소요되는 시간을 증가시킬 수 있습니다.

이러한 이유로 인해, 리소스 관리자는 업그레이드 도메인 정보를 수집하고 장애 도메인과 마찬가지로 클러스터 내 업그레이드 도메인에 복제본을 배포합니다. 업그레이드 도메인은 장애 도메인과 일대일로 통신을 하거나 하지 않을 수 있지만, 일반적으로는 일대일 통신을 하지 않을 것으로 예상됩니다. 다음 그림은 이전에 정의된 장애 도메인 위의 몇 가지 업그레이드 도메인 계층을 보여줍니다. 리소스 관리자는 클러스터 내에서 업그레이드나 장애가 있더라도 서비스의 높은 가용성을 확보하기 위하여, 복사본이 집중되는 장애 도메인이나 업그레이드 도메인이 발생하지 않도록 도메인 전역에 복제본을 배포합니다.

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
- Azure 배포에서 장애 도메인과 업그레이드 도메인은 Azure에 의해 할당되기 때문에 Azure에 대한 인프라 옵션 내 노드 및 역할에 대한 정의는 장애 도메인 또는 업그레이드 도메인 정보를 포함하지 않습니다.

### 노드 속성
노드 속성은 주어진 노드에 대한 여분의 메타데이터를 제공하는 사용자 정의 키/값 쌍입니다. 노드 속성의 예에는 노드에 하드 드라이브 또는 비디오 카드가 있는지 하드 드라이브, 코어에 포함된 스핀들의 개수 및 기타 물리 속성이 포함됩니다.

노드 속성은 정책 결정을 배치하는데 도움을 주기 위한 요약 속성을 지정하는데 사용될 수도 있습니다. 예를 들면, 클러스터 내 몇 개의 노드에 클러스터를 다른 섹션으로 구분하는 수단으로 “color(색)”이 할당될 수 있습니다. 코드 예제에서는 노드 속성이 클러스터 매니페스트를 통해 노드 유형 정의의 일부로 노드에 대해 정의되고, 그 후 클러스터 내에서 다수의 노드에 적용될 수 있음을 보여줍니다.

NodeName, NodeType, FaultDomain, 그리고 UpgradeDomain 배치 속성에는 기본값이 있습니다. 서비스 패브릭은 사용자가 서비스를 생성할 때 사용할 수 있도록 기본값을 자동으로 제공합니다. 사용자는 이것과 동일한 이름으로 자신의 배치 속성을 지정하지 말아야 합니다.

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

런타임 중에, 리소스 분산 장치는 속성 정보를 사용하여 특정 기능을 필요로 하는 서비스가 적절한 노드에 배치되도록 보장합니다.

### 노드 용량
노드 용량은 특정 노드가 사용할 수 있는 특정 리소스의 이름과 양을 정의하는 키/값 쌍입니다. 코드 사례는 "MemoryInMb"라는 메트릭에 대한 용량을 소유하는 동시에, 기본값으로 사용할 수 있는 메모리가 2048MB인 노드를 보여줍니다. 용량은 노드 속성과 매우 유사하게, 클러스터 매니페스트를 통해 정의됩니다.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![노드 용량][Image4]

노드에서 실행되는 서비스는 보고 부하를 통해 용량 요건을 업데이트할 수 있기 때문에, 리소스 분산 장치는 모든 메트릭에 대해 노드의 용량이 적정한지 초과되었는지를 주기적으로 확인할 수 있습니다. 리소스 분산 장치는 리소스 경합을 줄이고 전반적인 성능과 사용률을 향상시키기 위하여 서비스를 부하가 덜한 노드로 이동할 수 있습니다.

메트릭은 노드 유형의 속성 섹션에도 표시될 수 있으며, 런타임 중에 사용될 수 있는 노드의 속성인 경우에는 용량으로 표시하는 것이 좋습니다. 추가적인 속성으로 표시하면 "최소 하드웨어 요구 사항"에 의존하는 서비스가 노드의 배치 제약 조건을 쿼리할 수 있게 되는데, 이것은 런타임 중에 다른 서비스에서 리소스를 사용하는 경우 필요할 수 있습니다. 리소스 분산 장치가 추가적인 조치를 취할 수 있도록 하기 위하여 용량으로 포함시킬 것을 권장합니다.

새 서비스가 생성되면 서비스 패브릭 클러스터 리소스 분산 장치는 기존 노드의 용량 및 기존 서비스의 사용에 대한 정보를 사용하여 완전한 새 서비스를 배치하기에 충분한 용량을 사용할 수 있는지를 판단합니다. 용량이 충분하지 않으면 남아있는 클러스터 용량이 충분하지 않다는 것을 나타내는 오류 메시지와 함께 서비스 만들기 요청이 거부됩니다.


### 리소스 분산 장치 구성

클러스터 매니페스트 내에는, 리소스 분산 장치의 전반적인 동작을 정의하는 몇 가지 다른 구성값이 있습니다.

- 분산 임계값은 리소스 분산 장치가 대응하기 전에 특정 메트릭과 관련하여 불균형 클러스터가 어떻게 될지를 결정합니다. 분산 임계값은 클러스터 균형을 맞추기 전에 리소스 분산 장치에서 허용하는 최대 사용 노드와 최소 사용 노드 사이의 최대 비율입니다.

다음 그림은 메트릭에 대한 분산 임계값이 10인 두 가지 사례를 보여 줍니다.

![분산 임계값][Image5]

이런 경우, 노드 "사용률"은 노드 용량에 의해 결정되는 노드의 크기를 고려하지 않고, 지정된 메트릭에 대해 노드에 현재 보고되는 절대 사용만을 고려합니다.

코드 예제는 메트릭에 대한 분산 임계값이 클러스터 매니페스트 내의 FabricSettings 요소를 통해 메트릭마다 구성되는 것을 보여줍니다.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

- 활동 임계값은 상당한 절대 부하량이 존재할 때 리소스 분산 장치가 대응하는 경우를 제한함으로써 리소스 분산 장치의 실행 빈도에 관한 수문 역할을 합니다. 이런 방식으로 클러스터가 특정 메트릭에 대해 매우 바쁘지 않으면, 클러스터 내 소량의 메트릭이 불균형 상태가 심하더라도 리소스 분산 장치는 실행되지 않습니다. 이러한 조치는 실질적으로 득이 없는 클러스터 균형 조정으로 인한 리소스 낭비를 방지합니다. 다음 그림에서 메트릭에 대한 분산 임계값은 4이며 활동 임계값은 1536입니다.

![활동 임계값][Image6] 활동 임계값과 분산 임계값 모두 동일한 메트릭에 대해 리소스 분산 장치의 실행을 유발할 정도로 초과되어야 합니다. 별개의 메트릭 두 개 중 하나를 트리거해도 리소스 분산 장치는 실행되지 않습니다.

코드 예제는 분산 임계값과 마찬가지로 활동 임계값이 클러스터 매니페스트 내 FabricSettings 요소를 통해 메트릭 당 구성되는 것을 보여줍니다.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval – 리소스 분산 장치가 제약 조건 위반을 반드시 확인하기 위하여 정보를 스캔하는 빈도를 결정합니다. 제약 조건 위반에는 충족되지 않은 배치 제약 조건, 인스턴스 또는 복제본의 수가 요구되는 수보다 적은 서비스, 일부 메트릭에 대한 용량을 초과하는 노드, 오버로드 상태의 장애 또는 업그레이드 도메인, 또는 분산 및 활동 임계값 사용으로 인한 클러스터 불균형, 클러스터 내 노드의 부하에 대한 현재 보기가 포함됩니다. 새로 고침 간격은 초 단위로 정의되며 기본값은 1입니다. 제약 조건 확인 및 배치 빈도는 두 가지 다른 간격(MinConstraintCheckInterval 및 MinPlacementInterval)으로 제어할 수 있으며 새 매개 변수가 정의되면 PLBRefreshInterval은 사용되지 않고 정의될 수 없습니다.

- MinConstraintCheckInterval - 분산 제어 장치가 제약 조건 위반에 대해 반드시 확인해야 하는 정보를 스캔하는 빈도를 결정합니다. 제약 조건 위반에는 충족되지 않은 배치 제약 조건, 인스턴스 또는 복제본의 수가 요구되는 수보다 적은 서비스, 일부 메트릭에 대한 용량을 초과하는 노드, 오버로드 상태의 장애 또는 업그레이드 도메인, 또는 분산 및 활동 임계값 사용으로 인한 클러스터 불균형, 클러스터 내 노드의 부하에 대한 현재 보기가 포함됩니다. 제약 조건 확인 간격은 초 단위로 정의됩니다. 제약 조건 확인 간격이 정의되지 않는 경우, 기본값은 PLBRefreshInterval의 값과 동일합니다. (두 값은 동시에 지정될 수 없습니다.)

- MinPlacementInterval – 분산 제어 장치가 배치가 필요한 새 인스턴스 또는 복제본이 있는지를 확인하고 배치를 시도하는 빈도를 결정합니다. 배치 간격은 초 단위로 정의됩니다. 배치 간격이 정의되지 않은 경우, 기본값은 PLBRefreshInterval의 값과 동일합니다. (두 값은 동시에 지정될 수 없습니다.)

- MinLoadBalancingInterval – 리소스 분산 라운드 사이의 최소 시간을 설정합니다. 리소스 분산 장치가 마지막 PLBRefreshInterval 간격 중에 수행된 스캔을 통해 확보한 정보를 기반으로 조치를 취하는 경우, 적어도 동일한 시간 동안은 다시 실행되지 않습니다. 초 단위로 정의되며 기본값은 5입니다.

이 값은 적극적이지만, 메트릭에 대한 분산 임계값과 활동 임계값이 일치하는 경우에만 클러스터 내에서 전반적인 부하 분산이 발생합니다. 코드 예제는 클러스터에 대해 정확한 활성 부하 분산이 필요하지 않는 경우, 이 값은 FabricSettings 요소 내의 다음과 같은 구성을 통해 덜 적극적으로 설정될 수 있음을 보여줍니다. 이 예제 구성에서 두 제약 조건 확인 사이의 최소 시간 간격은 10초, 배치에 대해서는 5초인 반면, 부하 분산은 5분마다 발생합니다. 이런 경우 PLBRefreshInterval은 정의되어 있지 않습니다.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

아래의 두 번째 예제 구성에는 PLBRefreshInterval 및 MinLoadBalancingInterval이 정의되어 있습니다. PLBRefreshInterval이 2초로 설정되어 있기 때문에 MinPlacementInterval 및 MinConstraintCheckInterval 양쪽 모두 각각의 값을 2초로 설정합니다.

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
 

<!---HONumber=August15_HO6-->