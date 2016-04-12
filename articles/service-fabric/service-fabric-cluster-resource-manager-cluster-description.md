<properties
   pageTitle="리소스 분산 장치 클러스터 설명 | Microsoft Azure"
   description="클러스터 리소스 관리자에 대해 장애 도메인, 업그레이드 도메인, 노드 속성, 노드 용량을 지정하여 서비스 패브릭 클러스터를 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# 서비스 패브릭 클러스터 설명
서비스 패브릭 클러스터 리소스 관리자는 클러스터를 설명하는 몇 가지 메커니즘을 제공합니다. 런타임 중에 리소스 관리자는 이 정보를 제공하여 클러스터에서 실행되는 서비스의 높은 가용성을 보장하고 동시에 클러스터의 리소스가 적절하게 사용되도록 합니다.

## 주요 개념
클러스터를 설명하는 클러스터 리소스 관리자 기능은 다음과 같습니다.

- 장애 도메인
- 업그레이드 도메인
- 노드 속성
- 노드 용량

## 장애 도메인
장애 도메인은 통합된 오류는 영역입니다. 단일 컴퓨터는 장애 도메인입니다(전원 공급 장치 오류, 드라이브 오류, 잘못된 NIC 펌웨어 등 여러 이유로 멈출 수 있기 때문). 동일한 이더넷 스위치에 연결된 다수의 컴퓨터는 하나의 전원에 연결되어 있는 컴퓨터처럼 동일한 장애 도메인에 있는 것입니다.

사용자 고유의 클러스터를 설정 된 경우 모든 실패 영역을 고려하여 장애 도메인이 올바르게 설정되었는지 확인하여 서비스 패브릭이 서비스를 안전하게 배치했던 위치를 알 수 있도록 합니다. "안전하게"는 실제로 스마트하게 라는 의미로, 장애 도메인이 손실되어 서비스가 정지하는 위치에 서비스를 배치하면 안 됩니다. Azure 환경에서, 사용자 대신 클러스터의 노드를 올바르게 구성하기 위해 Azure 패브릭 컨트롤러/리소스 관리자가 제공하는 장애 도메인 정보를 활용하겠습니다. 아래 그림(그림 7)에서는 간단한 예로서 장애 도메인을 발생시키는 모든 엔터티에 색을 넣고 발생한 여러 장애 도메인을 모두 나열합니다. 이 예제에는 데이터센터(DC), 랙(R) 및 블레이드(B)가 있습니다. 각 블레이드에 하나 이상의 가상 컴퓨터가 있는 경우 장애 도메인 계층 구조에 또 다른 계층이 있다고 볼 수 있습니다.

![장애 도메인을 통해 구성된 노드][Image1]

 런타임 중에 서비스 패브릭 클러스터 리소스 분산 장치는 클러스터 내의 장애 도메인을 고려하여 지정된 서비스의 복제본을 개별적인 장애 도메인에 모두 두기 위하여 서비스 복제본을 퍼트리기 위한 시도를 합니다. 이 프로세스는 장애 도메인 중 하나에 실패가 발생하는 경우, 해당 서비스의 가용성을 확보하고 서비스가 손상되지 않았음을 확인하는데 도움이 됩니다.

 서비스 패브릭의 클러스터 리소스 관리자는 계층 구조에 얼마나 많은 계층이 있는지에 대해 관심이 없지만, 계층의 어느 한 부분의 손실이 클러스터 또는 클러스터에서 실행되는 서비스에 영향을 주지 않도록 하려 하기 때문에 장애 도메인의 각 깊이 수준에 동일한 수의 컴퓨터가 있는 경우에 가장 좋습니다. 이는 하루 일정이 끝날 때 계층 구조의 한 부분이 다른 계층 구조보다 더 많은 서비스를 포함하지 않도록 합니다.

 장애 도메인의 "트리" 구조가 불균형되도록 클러스터를 구성하면 리소스 관리자가 복제본을 최상으로 할당하는 데 어려움을 겪게 되며, 특히 특정 도메인의 손실이 클러스터의 가용성에 과도하게 영향을 미칠 수 있기 때문에 리소스 관리자는 "무거운" 도메인에 있는 컴퓨터의 효율적인 사용과 도메인의 손실이 문제를 일으키지 않는 서비스 배치 사이에서 고민하게 됩니다.

 아래 다이어그램에서는 서로 다른 두 클러스터를 설정하는 데, 하나는 노드가 장애 도메인 전체에 잘 분산된 클러스터이고, 다른 하나는 하나의 장애 도메인이 더 많은 노드가 되는 클러스터입니다. Azure에서 사용자를 위해 어떤 장애 및 업그레이드 도메인에 어떤 노드를 포함시킬지에 대한 선택을 처리하기 때문에 사용자는 이러한 유형의 불균형을 볼 수 없습니다. 그러나 자체 클러스터 온-프레미스 또는 다른 환경에서 구축하는 경우 이는 고려해야 할 문제입니다.

 ![두 개의 다른 클러스터 레이아웃][Image2]

## 업그레이드 도메인
업그레이드 도메인은 서비스 패브릭 리소스 관리자가 실패에 대해 계획을 세울 수 있도록 클러스터의 레이아웃을 이해하는 데 도움을 주는 또 다른 기능입니다. 업그레이드 도메인은 업그레이드함과 동시에 작동이 멈추는 영역(실제 노드 설정)을 정의합니다.

업그레이드 도메인은 장애 도메인과 많이 닮았지만 몇 가지 주요 차이점이 있습니다. 첫째, 업그레이드 도메인은 일반적으로 정책에 의해 정의되는 반면, 장애 도메인은 조정된 오류의 영역에서 엄격하게 정의됩니다(주로 환경의 하드웨어 레이아웃). 그러나 업그레이드 도메인의 경우 몇 개나 필요한지 결정하게 됩니다. 또 다른 차이점으로는 적어도 지금은 업그레이드 도메인은 계층 구조가 아닙니다. 계층 구조라기보다는 간단한 태그에 더 가깝습니다.

아래 그림은 세 개의 업그레이드 도메인이 세 개의 장애 도메인을 가로지르는 가상 설치를 보여줍니다. 또한 상태 저장 서비스의 세 개의 서로 다른 복제본을 배치하는 한 가지 방법을 보여줍니다. 이들 모두는 여러 장애 및 업그레이드 도메인에 있습니다. 즉, 서비스 업그레이드 도중 장애 도메인을 잃을 수 있고 클러스터에 코드 및 데이터의 한 복사본이 여전히 실행 중일 수 있습니다. 필요에 따라 이것으로 충분할 수도 있지만, 이 복사본은 오래된 것일 수 있습니다(서비스 패브릭은 쿼럼 기반 복제를 사용). 두 개의 오류를 실제로 해결하기 위해 더 많은 복제본(최소 5개)이 필요할 수 있습니다.

![장애 및 업그레이드 도메인으로 배치][Image3]

많은 수의 업그레이드 도메인에 장단점이 있습니다. 장점으로는 업그레이드의 각 단계가 보다 세분화되어 더 적은 수의 노드 또는 서비스에 영향을 줍니다. 이로 인해 한 번에 이동해야 할 서비스가 적어져 시스템 내부로 이탈이 줄고 전반적인 안정성이 향상됩니다(문제에 영향을 받는 서비스가 감소하기 때문). 여러 업그레이드 도메인을 가질 때의 단점은 서비스 패브릭이 업그레이드될 때 각 업그레이드 도메인의 상태를 확인하고 다음 업그레이드 도메인으로 이동하기 전에 업그레이드 도메인의 상태를 정상으로 유지하는 것입니다. 이러한 검사의 목적은 서비스를 안정화하고 업그레이드가 진행되기 전에 문제가 있는지 서비스 상태를 확인하기 위한 것입니다. 이러한 단점은 한 번에 너무 많은 서비스에 영향을 주는 잘못된 변경 사항을 방지하기 때문에 받아들일 만합니다.

업그레이드 도메인 수가 너무 적어도 부작용이 있습니다. 개별 업그레이드 도메인이 다운된 후 업그레이드하는 동안 전체 용량의 상당 부분을 사용할 수 없습니다. 예를 들어, 세 개의 업그레이드 도메인만 있는 경우 전체 서비스 또는 클러스터 용량은 약 1/3로 저하됩니다. 이는 워크로드를 감당하기 위해 클러스터의 나머지 부분에 용량이 충분해야 하기 때문에 바람직하지 않으며, 정상적인 경우 COGS가 증가하지 않도록 이러한 노드에 부하를 줄여야 함을 의미합니다.

환경에서 장애 또는 업그레이드 도메인의 총 수는 실제 제한이나 중첩 방법에 대한 제약이 없습니다. 이미 살펴본 일반적인 구조로는 1:1(각각의 고유한 장애 도메인이 자체 업그레이드 도메인에 매핑), 노드당 업그레이드 도메인(실제 또는 가상 OS 인스턴스), 장애 도메인 및 업그레이드 도메인이 일반적으로 컴퓨터가 대각선으로 가로지르는 행렬을 형성하는 "스트라이프" 또는 "행렬" 모델이 있습니다.

![장애 및 업그레이드 도메인 레이아웃][Image4]

어떤 레이아웃을 선택하느냐에 대한 정답은 없으며, 각각 몇 가지 장단점이 있습니다. 예를 들어, 1FD:1UD 모델은 매우 간단히 설정할 수 있는 반면, 노드당 1UD 모델은 과거 각각 별도로 해체할 수 있는 소수의 컴퓨터를 관리했던 사람들이 가장 좋아하는 모델입니다.

가장 일반적인 모델(및 호스팅된 Azure 서비스 패브릭 클러스터에 사용하는 모델)은 FD/UD 행렬로, FD와 UD는 테이블을 형성하고 노드는 대각선을 따라 배치됩니다. 이는 스파스가 될지 압축될지 여부는 FD와 UD의 수와 비교하여 총 노드 수에 달려 있습니다(다시 말하면, 그림 10의 맨 아래 오른쪽 옵션에 나와있는 대로 충분히 큰 클러스터의 경우 거의 모든 항목이 조밀한 행렬 패턴처럼 보입니다).

## 장애 및 업그레이드 도메인 구성
장애 도메인 및 업그레이드 도메인 정의는 Azure 호스티드 서비스 패브릭 배포에서 자동으로 수행되며, 서비스 패브릭은 Azure의 환경 정보를 선택합니다. 그런 다음 사용자는 도메인 개수를 선택할 수 있습니다. Azure에서 장애 및 업그레이드 도메인 정보 모두 "단일 수준"처럼 보이지만, 실제로 Azure 스택의 낮은 계층의 정보를 캡슐화하고 사용자의 관점에서 논리적 장애 및 업그레이드 도메인을 나타냅니다.

사용자의 클러스터를 구축하는 경우(또는 개발 컴퓨터에서 특정 토폴로지를 실행하려는 경우) 장애 도메인 및 업그레이드 도메인 정보를 사용자가 직접 제공해야 합니다. 이 예에서는 세 개의 "데이터센터"(각각 세 개의 랙 포함)에 걸쳐 확장된 9 노드 클러스터와 그 세 개의 데이터센터를 가로지르는 업그레이드 도메인을 정의합니다. 클러스터 매니페스트에서 이는 다음과 같이 보입니다.

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [AZURE.NOTE] Azure 배포에서 장애 도메인과 업그레이드 도메인은 Azure에 의해 할당되기 때문에 Azure에 대한 인프라 옵션 내 노드 및 역할 정의에 장애 도메인 또는 업그레이드 도메인 정보가 포함되지 않습니다.

## 배치 제약 조건 및 노드 속성
종종(실제로 대부분의 경우) 특정 워크로드가 특정 노드 또는 특정 노드 집합에서만 실행되도록 하려 할 것입니다. 예를 들어, 일부 워크로드는GPU 또는 SSD가 필요할 수 있습니다. 이에 대한 좋은 예는 거의 모든 n 계층 아키텍처로, 특정 컴퓨터는 응용 프로그램의 서비스 프런트 엔드/인터페이스의 역할을 하고 다른 컴퓨터(종종 다른 하드웨어 리소스 포함)는 계산 또는 저장소 계층의 작업을 처리합니다. 서비스 패브릭은 마이크로 서비스 환경에서 특정 워크로드가 특정 하드웨어 구성에서 실행될 것을 기대합니다. 예를 들어,

- 기존 n 계층 응용 프로그램은 서비스 패브릭 환경으로 "리프트 및 이동"되었습니다.
- 성능, 규모 또는 보안상의 이유 등으로 특정 하드웨어에서 워크로드를 실행하려고 합니다.
-	한 워크로드는 정책 또는 리소스 소비 이유로 다른 워크로드로부터 격리되어야 합니다.

이러한 유형의 구성을 지원하기 위해 서비스 패브릭에는 배치 제약 조건이라고 부르는 첫 번째 클래스 개념이 있습니다. 제약 조건은 특정 서비스를 실행할 위치를 나타내는 데 사용할 수 있습니다. 제약 조건 집합은 사용자에 의해 확장 가능하기 때문에, 사람들이 사용자 지정 속성이 있는 노드를 태그한 다음 선택할 수 있습니다.

![클러스터 레이아웃 다양한 워크로드][Image5]

노드의 서로 다른 키/값 태그는 노드 배치 속성(또는 노드 속성)으로 알려져 있으며, 서비스의 문은 배치 제약 조건이라고 합니다. 노드 속성에서 지정된 값은 문자열, 부울 또는 기호가 있는 long이 될 수 있습니다. 제약 조건은 클러스터의 여러 노드 속성에서 작동하는 모든 부울 문이 될 수 있으며, 문이 "True"로 평가된 노드만 배치되는 서비스를 가질 수 있습니다. 정의된 속성이 없는 노드는 해당 속성이 포함된 배치 제약 조건과 일치하지 않습니다. 또한 서비스 패브릭은 사용자가 정의할 필요 없이 자동으로 사용할 수 있는 몇 가지 기본 속성을 정의합니다. 이 문서를 작성하는 현재 각 노드에서 정의된 기본 속성은 NodeType 및 NodeName입니다. 일반적으로 NodeType는 가장 흔히 사용되는 속성 중 하나이며, 컴퓨터 유형과 1:1로 일치시킨 다음 기존 n 계층 응용 프로그램 아키텍처의 워크로드 형식과 일치시킵니다.

![배치 제약 조건 및 노드 속성][Image6]

다음과 같은 노드 속성이 제시된 노드 형식에 대해 정의되었다고 가정해 보겠습니다. ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasDisk" Value="true"/>
        <Property Name="Value" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

다음과 같은 서비스에 대 한 서비스 배치 제약 조건을 만들 수 있습니다.

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true && Value >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasDisk == true && Value >= 4"
```

NodeType01의 모든 노드에 올바르다고 판단한 경우, 위 그림과 같이 배치 제약 조건을 사용하여 해당 노드 유형을 선택할 수도 있습니다.

서비스 배치 제약 조건에 대한 멋진 기능 중 하나는 런타임 도중 동적으로 업데이트할 수 있다는 것입니다. 따라서 필요한 경우 클러스터에서 서비스를 이동할 수 있으며, 요구 사항 등을 추가 및 제거할 수 있습니다. 서비스 패브릭은 이러한 유형의 변경이 진행 중일 때도 서비스를 유지하고 사용할 수 있도록 관리합니다.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

배치 제약 조건(곧 설명할 여러 속성과 포함)은 모든 여러 가지 서비스 인스턴스에 대해 지정됩니다. 업데이트는 이전에 지정된 것에 대해 항상 수행(덮어쓰기)됩니다.

## 용량
모든 조정자의 가장 중요한 작업 중 하나는 클러스터에서 리소스 소비를 관리할 수 있도록 돕는 일입니다. 서비스를 효율적으로 실행하려는 경우 마지막으로 해야 할 일은 자주 사용되는 노드(리소스 경합 및 성능 저하로 이어짐)와 자주 사용되지 않는 노드(리소스 낭비)를 구분하는 것입니다. 하지만 분산(곧 다룰 예정)보다 좀더 기본적인 것을 생각해 보겠습니다. 먼저 노드가 리소스를 소모하지 않는 경우는 어떨까요?

서비스 패브릭은 "메트릭"이라는 리소스를 나타냅니다. 메트릭은 서비스 패브릭에 대해 설명하려는 논리적 또는 물리적 리소스입니다. 메트릭의 예로는 "WorkQueueDepth" 또는 "MemoryInMb" 등이 있습니다. 메트릭은 해당 노드 속성의 제약 조건 및 노드 속성과 다르며 일반적으로 노드 자체의 정적인 설명자이고, 동시에 노드에서 실행되고 있을 때 서비스가 소비하는 물리적 리소스에 관한 것입니다. 속성은 HasSSD와 같은 것으로 True 또는 False로 지정할 수 있지만, 해당 SSD에 서 사용 가능한 공간(서비스에서 사용)의 크기는 "DriveSpaceInMb"와 같은 메트릭일 것입니다. 노드 용량은 "DriveSpaceInMb"를 드라이브의 예약되지 않은 공간의 총 크기로 설정하고 서비스는 런타임 시 메트릭의 사용량을 보고합니다.

모든 리소스를 해제한 경우 서비스 패브릭의 리소스 관리자를 분산시키면 여전히 노드가 해당 용량을 초과하지 않도록 할 수 있습니다(전체 클러스터가 너무 꽉 차지 않은 경우 제외). 용량은 서비스 패브릭이 노드의 리소스 크기가 얼만지 파악하는 데 사용하는 메커니즘입니다. 용량이 얼마나 남았는지 알기 위해 여러 서비스(나중에 설명)가 소비하는 양을 빼서 파악합니다. 서비스 수준에서 용량과 소비량은 메트릭을 기준으로 표현됩니다.

런타임 중에 리소스 관리자는 각 노드(해당 용량에 의해 정의됨)에 각 리소스가 얼마나 있는지 그리고 얼마나 남았는지(각 서비스에서 선언된 모든 사용량 뺌) 추적합니다. 이 정보를 통해 서비스 패브릭 리소스 관리자는 노드가 용량을 초과하지 않도록 복제본을 어디에 배치하거나 이동시킬지 알 수 있습니다.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "Memory";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 1024;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,1024,1024”)
```

![클러스터 노드 및 용량][Image7]

클러스터 매니페스트에서 이를 확인할 수 있습니다.

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="Memory" Value="10"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

또한 서비스의 부하를 동적으로 변경할 수도 있습니다. 이 경우 해당 노드의 모든 복제본 및 인스턴스의 총 사용량이 해당 노드의 용량을 초과하여 복제본 또는 인스턴스가 현재 배치된 위치를 사용할 수 없게 될 수 있습니다. 부하가 동적으로 변경되는 이 시나리오에 대한 나중에 자세히 설명할 것이지만, 용량에 관한 한 동일하게 처리됩니다. 즉, 서비스 패브릭 리소스 관리는 자동으로 시작되고 하나 이상의 복제본 또는 인스턴스를 해당 노드에서 다른 노드로 이동시켜 노드의 최대 용량 아래로 낮춥니다. 이 작업을 수행하는 경우 리소스 관리자는 모든 움직임에 따른 비용을 최소화하려 합니다(비용의 개념은 나중에 설명).

##클러스터 용량
그렇다면 전체 클러스터 용량이 꽉 차지 않도록 하려면 어떻게 해야 합니까? 동적 부하의 경우 실제로 할 수 있는 일이 별로 없지만(리소스 관리자에서 수행된 작업과 관계 없이 서비스는 부하가 최대로 증가할 수 있습니다. 즉, 오늘은 여유가 많은 클러스터가 내일은 수요가 많아져 전력이 부족해질 수 있기 때문), 기본적인 오류를 방지하기 위해 내장된 컨트롤이 몇 가지 있습니다. 가장 먼저 할 수 있는 작업은 클러스터가 꽉 차도록 만드는 워크로드가 생성되지 않도록 하는 것입니다.

간단한 상태 비저장 서비스를 만들기로 이동하고 이와 관련된 일부 부하가 있다고 가정해 보겠습니다(기본 및 동적 부하 보고에 대해서는 나중에 자세히 설명). 이 서비스의 경우, 일부 리소스(DiskSpace라고 가정)를 관리하고 서비스의 모든 인스턴스에 대해 기본적으로 5단위의 DiskSpace를 소비한다고 가정해 보겠습니다. 3개의 서비스 인스턴스를 만들려고 합니다. 잘하셨습니다. 즉, 이들 서비스 인스턴스를 만들기 위해서는 클러스터에 15단위의 DiskSpace가 필요하다는 의미입니다. 서비스 패브릭은 각 메트릭의 전체 용량 및 소비량을 계속 계산하여 쉽게 결정할 수 있으며 공간이 부족한 경우 서비스 호출을 거부할 수 있습니다.

요구 사항은 15단위만 사용할 수 있기 때문에, 이 공간은 여러 방식으로 할당할 수 있습니다. 15개의 노드에 나머지 용량 단위를 할당하거나 5개의 노드에 나머지 3단위 용량을 할당할 수 있습니다. 세 개의 서로 다른 노드에 용량이 충분하지 않은 경우 서비스 패브릭은 필요한 노드 세 개에 공간을 확보하기 위해 클러스터에 이미 있는 서비스를 다시 구성합니다. 전체 클러스터가 거의 꽉 차지 않은 한 이러한 재배열은 거의 항상 가능합니다.

전체 클러스터 용량 관리를 돕기 위해 수행한 또 다른 작업은 각 노드에서 지정된 용량에 일부 예약된 버퍼의 개념을 추가하는 것이었습니다. 이 설정은 선택 사항이지만, 전체 노드 용량의 일부분을 지정하여 업그레이드 도중이나 클러스터 용량이 감소하여 실패한 경우 서비스를 배치하는 데에만 사용할 수 있습니다. 현재 ClusterManifest를 통해 모든 노드에 대해 메트릭을 기준으로 버퍼가 전역에 지정되어 있습니다. 예약 용량에 대해 선택한 값은 서비스를 더욱 제한하는 리소스의 함수이자 클러스터의 장애 및 업그레이드 도메인 수입니다. 일반적으로 더 많은 장애 및 업그레이드 도메인은 버퍼링된 용량에 대해 낮은 값을 선택할 수 있다는 의미이며, 업그레이드 도중 그리고 실패 시 사용할 수 없는 클러스터 양을 줄여야 합니다. 메트릭에 대해 노드 용량을 지정한 경우 버퍼율을 지정할 수도 있습니다.

ClusterManifest.xml

```xml
        <Section Name=" NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="10" />
            <Parameter Name="Memory" Value="15" />
            <Parameter Name="SomeOtherMetric" Value="20" />
        </Section>
```
클러스터의 버퍼링된 용량이 부족하면 새 서비스에 만들기 위한 호출은 실패합니다. 업그레이드 및 실패로 인해 노드가 용량을 초과하지 않도록 클러스터가 예비 공간을 유지해야 합니다. 리소스 관리자는 PowerShell 및 쿼리 API를 통해 이 정보를 많이 공개하기 때문에, 버퍼링된 용량 설정, 총 용량, 주어진 모든 메트릭에 대한 현재 소비량을 볼 수 있습니다. 여기에서 출력의 예제를 볼 수 있습니다.

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## 다음 단계
- 클러스터 리소스 관리자 내의 아키텍처 및 정보 흐름에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-architecture.md)를 확인하세요.
- 조각 모음 메트릭 정의는 노드의 부하를 분배하는 대신 통합하는 한 가지 방법입니다. 조각 모음을 구성하는 방법에 대해 알아보려면 [이 문서](service-fabric-cluster-resource-manager-defragmentation-metrics.md)를 참조하세요.
- 처음부터 시작 및 [서비스 패브릭 클러스터 리소스 관리자 소개](service-fabric-cluster-resource-manager-introduction.md)
- 클러스터 리소스 관리자가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)에 대한 문서를 확인하세요.

[Image1]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

<!---HONumber=AcomDC_0323_2016-->