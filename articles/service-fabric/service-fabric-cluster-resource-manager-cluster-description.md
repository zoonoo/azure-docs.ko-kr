---
title: "리소스 분산 장치 클러스터 설명 | Microsoft Docs"
description: "Cluster Resource Manager에 대한 장애 도메인, 업그레이드 도메인, 노드 속성, 노드 용량을 지정하여 Service Fabric 클러스터를 설명합니다."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: cfc38cecfaf0a0bdaae043fc35dcfed743459823
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="describing-a-service-fabric-cluster"></a>서비스 패브릭 클러스터 설명
서비스 패브릭 클러스터 리소스 관리자는 클러스터를 설명하는 몇 가지 메커니즘을 제공합니다. 런타임 중에 클러스터 Resource Manager는 이 정보를 사용하여 클러스터에서 실행되는 서비스의 높은 가용성을 보장합니다. 또한 이러한 주요 규칙을 적용하는 동시에 클러스터의 리소스가 최적화되도록 시도합니다.

## <a name="key-concepts"></a>주요 개념
클러스터 Resource Manager는 클러스터를 설명하는 다음과 같은 몇 가지 기능을 지원합니다.

* 장애 도메인
* 업그레이드 도메인
* 노드 속성
* 노드 용량

## <a name="fault-domains"></a>장애 도메인
장애 도메인은 통합된 오류는 영역입니다. 단일 컴퓨터는 장애 도메인입니다(전원 공급 장치 오류, 드라이브 오류, 잘못된 NIC 펌웨어 등 다양한 이유로 자체 실패할 수 있기 때문). 동일한 이더넷 스위치에 연결된 컴퓨터는 하나의 전원을 공유하는 해당 컴퓨터처럼 동일한 장애 도메인에 있습니다. 하드웨어 오류가 중복되는 것이 당연하므로 장애 도메인은 기본적으로 계층 구조를 가지며 Service Fabric에서 URI로 표시됩니다.

사용자 고유의 클러스터를 설정하는 경우 이러한 오류의 여러 영역을 검토해야 합니다. Service Fabric이 이러한 정보를 사용하여 서비스를 안전하게 배치하기 때문에 장애 도메인을 올바르게 설정하는 것이 중요합니다. Service Fabric은 일부 구성 요소의 오류에 의해 발생한 장애 도메인의 손실로 인해 서비스가 중지되지 않도록 서비스를 배치하려고 합니다. Service Fabric은 Azure 환경에서 사용자 대신 클러스터의 노드를 올바르게 구성하기 위해 작업 환경에 제공되는 장애 도메인 정보를 사용합니다.

아래 그래픽에서는 장애 도메인에 적용되고 발생한 모든 다른 장애 도메인을 나열하는 모든 엔터티에 색을 지정했습니다. 이 예제에는 데이터센터("DC"), 랙("R") 및 블레이드("B")가 있습니다. 각 블레이드에 하나 이상의 가상 컴퓨터가 있는 경우 장애 도메인 계층 구조에 또 다른 계층이 있다고 볼 수 있습니다.

<center>
![장애 도메인을 통해 구성된 노드][Image1]
</center>

런타임 시 Service Fabric Cluster Resource Manager는 클러스터에서 장애 도메인을 고려하여 레이아웃을 계획합니다.  지정 된 서비스에 대한 상태 저장 복제본 또는 상태 비저장 인스턴스가 별도의 장애 도메인에 위치하도록 분산하려고 합니다. 이 프로세스는 계층 구조의 수준에서 장애 도메인 중 하나에 오류가 발생하는지, 해당 서비스의 가용성이 손상되지 않았는지를 확인하는 데 도움이 됩니다.

Service Fabric의 Cluster Resource Manager는 장애 도메인 계층 구조에 있는 계층 수를 고려하지 않습니다. 그러나 계층의 일부를 손실하는 경우 이를 기반으로 실행되는 서비스에 영향을 주지 않도록 하려고 합니다. 이로 인해 장애 도메인 계층 구조라는 면의 각 수준에서 노드 수가 동일한 것이 좋습니다. 수준의 균형을 유지하게 되면 계층 구조의 한 부분이 다른 부분보다 더 많은 서비스를 포함하지 않도록 방지합니다. 그렇지 않으면 개별 노드의 부하에서 불균형이 발생하고 특정 도메인의 오류가 다른 오류보다 더 심각해 집니다.

장애 도메인의 "트리"가 클러스터에서 분산되지 않은 경우 Cluster Resource Manager가 서비스를 가장 잘 할당할 수 없게 됩니다. 장애 도메인 레이아웃이 불균형 상태인 경우 특정 도메인의 손실이 다른 손실보다 더 많은 클러스터의 가용성에 영향을 줄 수 있게 됩니다. 즉, Cluster Resource Manager는 두 가지 목표 사이에서 고민합니다. "사용량이 많은" 해당 도메인에 서비스를 배치하여 컴퓨터를 사용했지만 도메인의 손실로 인해 문제가 발생하지 않도록 서비스를 배치하고자 합니다. 이것은 어떻게 표시되나요?

<center>
![두 개의 다른 클러스터 레이아웃][Image2]
</center>

위의 다이어그램에서는 클러스터 레이아웃의 두 가지 예제를 보여 줍니다. 첫 번째 예에서 노드는 장애 도메인 간에 균등하게 분산됩니다. 다른 예제에서는 하나의 장애 도메인에 많은 노드가 배치됩니다. 고유한 클러스터 온-프레미스 또는 다른 환경에서 구축하는 경우 이는 고려해야 할 문제입니다.

Azure에서 노드를 배치할 장애 도메인을 선택하는 작업은 사용자를 위해 관리됩니다. 그러나 프로비전하는 노드의 수에 따라 여전히 장애 도메인에 다른 항목보다 더 많은 노드가 배치될 수 있습니다. 예를 들어, 5개의 장애 도메인이 있지만 지정된 NodeType에 대해 7개의 노드를 프로비전합니다. 이 경우에 처음 두 개의 장애 도메인에 결국 많은 노드가 배치됩니다. 몇 가지 인스턴스를 사용하여 더 많은 NodeTypes만 계속 배포하는 경우 문제가 악화됩니다.

## <a name="upgrade-domains"></a>업그레이드 도메인
업그레이드 도메인은 Service Fabric Cluster Resource Manager가 실패에 대해 계획을 세울 수 있도록 클러스터의 레이아웃을 이해하는 데 도움을 주는 또 다른 기능입니다. 업그레이드 도메인은 동시에 업그레이드된 노드 집합을 정의합니다.

업그레이드 도메인은 장애 도메인과 많이 닮았지만 몇 가지 주요 차이점이 있습니다. 첫째, 장애 도메인은 조정된 하드웨어 오류의 영역에서 엄격하게 정의되는 반면 업그레이드 도메인은 정책에 의해 정의됩니다. 업그레이드 도메인 수는 환경에 의해 결정되지 않고 사용자가 결정하게 됩니다. 또 다른 차이점으로는 적어도 지금은 업그레이드 도메인은 계층 구조가 아닙니다. 간단한 태그에 더 가깝습니다.

다음 다이어그램에서는 3개의 업그레이드 도메인이 세 개의 장애 도메인에 스트라이프되어 있음을 보여 줍니다. 또한 상태 저장 서비스의 세 개의 서로 다른 복제본을 배치하는 한 가지 방법을 보여줍니다. 여기서 각각 다른 장애 도메인 및 업그레이드 도메인에 배치됩니다. 이 배치를 사용하면 서비스를 업그레이드하는 중에 장애 도메인을 손실하고도 코드와 데이터의 복사본이 있을 수 있습니다.

<center>
![장애 도메인 및 업그레이드 도메인으로 배치][Image3]
</center>

많은 수의 업그레이드 도메인이 있는 경우 장단점이 있습니다. 업그레이드 도메인이 많으면 업그레이드의 각 단계가 보다 세분화되어 더 적은 수의 노드 또는 서비스에 영향을 줍니다. 이로 인해 한 번에 더 적은 서비스를 이동하여 시스템에 이탈이 적어집니다. 그러면 서비스가 업그레이드 중에 발생된 문제의 영향을 덜 받기 때문에 안정성을 향상시키는 경향이 있습니다. 업그레이드 도메인이 많으면 업그레이드의 영향을 처리하기 위해 다른 노드에서 사용할 수 있는 오버 헤드가 적게 필요합니다. 예를 들어 5개의 업그레이드 도메인이 있으면 각각의 노드는 트래픽의 약 20%를 처리합니다. 업그레이드할 업그레이드 도메인을 중단해야 하는 경우 해당 로드를 다른 곳에 이동시켜야 합니다. 업그레이드 도메인이 많으면 클러스터의 다른 노드에서 유지 관리해야 하는 오버 헤드가 적어집니다.

많은 업그레이드 도메인을 가진 경우의 단점은 업그레이드 시간이 오래 걸린다는 점입니다. Service Fabric은 계속 진행하기 전에 업그레이드 도메인을 완료한 후에 짧은 시간 동안 대기합니다. 이 지연으로 인해 업그레이드에서 도입된 문제를 표시하고 감지할 수 있게 됩니다. 이러한 단점은 한 번에 너무 많은 서비스에 영향을 주는 잘못된 변경 사항을 방지하기 때문에 받아들일 만합니다.

업그레이드 도메인 수가 너무 적어도 부작용이 있습니다. 개별 업그레이드 도메인이 중단되고 업그레이드되는 동안 전체 용량의 상당 부분을 사용할 수 없기 때문입니다. 예를 들어, 세 개의 업그레이드 도메인만 있는 경우 전체 서비스 또는 클러스터 용량은 약 1/3로 저하됩니다. 워크로드를 처리하는 클러스터의 나머지 부분에 용량이 충분해야 하기 때문에 한 번에 서비스의 상당 부분이 중단되는 것은 바람직하지 않습니다. 해당 버퍼를 유지하게 되면 일반적인 경우에 해당 노드가 로드할 수 있는 양이 평소보다 적어지는 동시에 서비스를 실행하는 비용이 증가합니다.

환경에서 장애 도메인 또는 업그레이드 도메인의 총 수에 대한 제한이 없고 중첩 방법에 대한 제약도 없습니다. 살펴본 일반적인 구조는 다음과 같습니다.

* 1:1 매핑된 장애 도메인 및 업그레이드 도메인
* 노드(실제 또는 가상 OS 인스턴스) 당 하나의 업그레이드 도메인
* 장애 도메인 및 업그레이드 도메인이 대각선으로 실행되는 컴퓨터를 사용하여 행렬을 형성하는 "스트라이프" 또는 "매트릭스" 모델

<center>
![장애 도메인 및 업그레이드 도메인 레이아웃][Image4]
</center>

어떤 레이아웃을 선택하느냐에 대한 정답은 없으며, 각각 몇 가지 장단점이 있습니다. 예를 들어 1FD:1UD 모델은 매우 간단하게 설정할 수 있습니다. 노드당 1UD 모델은 과거 각각 별도로 해체할 수 있는 소수의 컴퓨터를 관리했던 사람들이 가장 좋아하는 모델입니다.

가장 일반적인 모델 및 Azure에서 사용한 모델은 FD/UD 행렬로, 여기서 FD와 UD는 테이블을 형성하고 노드는 대각선을 따라 배치됩니다. 이를 스파스할지 혹은 압축할지 여부는 FD와 UD 수와 비교한 노드의 총수에 따라 달라집니다. 다시 말해, 클러스터가 충분히 큰 경우 거의 모든 항목은 밀도 매트릭스 패턴과 같이 표시되며 위 이미지의 하단 오른쪽 옵션에 표시됩니다.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>장애 도메인 및 업그레이드 도메인 제약 조건 및 결과 동작
Cluster Resource Manager는 장애 도메인 및 업그레이드 도메인에 분산된 서비스를 제약 조건으로 지정합니다. [이 문서](service-fabric-cluster-resource-manager-management-integration.md)에서 제약 조건에 대한 자세한 내용을 확인할 수 있습니다. 장애 도메인 및 업그레이드 도메인 제약 조건은 다음과 같습니다. "지정된 서비스 파티션에서 두 도메인 간의 서비스 개체(상태 비저장 서비스 인스턴스 또는 상태 저장 서비스 복제본) 수는 *1개 이상* 차이가 나지 않아야 합니다."  즉, 실질적으로 지정된 서비스를 이동하거나 정렬하는 것은 장애 도메인 또는 업그레이드 도메인 제약 조건을 위반하므로 유효하지 않을 수 있습니다.

한 가지 예를 살펴보겠습니다. 6개의 노드를 포함하며 5개의 장애 도메인과 5개의 업그레이드 도메인으로 구성된 클러스터가 있다고 가정해 보겠습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

이제 TargetReplicaSetSize가 5인 서비스를 만들어 보겠습니다. 복제본은 N1-N5에 생성됩니다. 실제로 N6는 만든 서비스 수와 관계 없이 절대 사용되지 않습니다. 그렇지만 그 이유는 무엇일까요? N6을 선택하는 경우 현재 레이아웃 및 발생하는 상황 간의 차이를 살펴보겠습니다.

다음은 장애 도메인 및 업그레이드 도메인에 따른 레이아웃 및 총 복제본 수입니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 및 업그레이드 도메인당 노드의 측면에서 균형을 이룹니다. 장애 도메인 및 업그레이드 도메인에 따른 복제본의 수 측면에서도 균형을 이룹니다. 각 도메인에는 동일한 수의 노드 및 동일한 수의 복제본이 있습니다.

이제 N2 대신 N6를 사용하는 경우 발생하는 결과를 살펴보겠습니다. 복제본은 어떻게 배포되나요?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

특이점이 있나요? 이 레이아웃은 장애 도메인 제약 조건에 대한 정의를 위반합니다. FD0에는 두 개의 복제본이 있는 반면 FD1에는 없으며 FD0 및 FD1이라는 총 두 번의 차이가 발생하게 됩니다. Cluster Resource Manager는 이 정렬을 허용하지 않습니다. 마찬가지로 N2 및 N6(N1 및 N2 대신)을 선택한 경우 다음에서 얻을 수 있습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃이 장애 도메인 측면에서 부하가 분산되는 반면 업그레이드 도메인 제약 조건을 위반합니다(UD1에는 복제본이 2개 있지만 UD0에는 복제본이 없기 때문). 이 레이아웃은 유효하지도 않습니다.

## <a name="configuring-fault-and-upgrade-domains"></a>장애 도메인 및 업그레이드 도메인 구성
장애 도메인 및 업그레이드 도메인 정의는 Azure 호스티드 Service Fabric 배포에서 자동으로 수행됩니다. Service Fabric은 Azure의 환경 정보를 선택하고 사용합니다.

사용자가 고유한 클러스터를 만들거나 개발 중인 특정 토폴로지를 실행하려면 장애 도메인 및 업그레이드 도메인 정보를 직접 제공합니다. 이 예제에서는 각각 세 개의 랙을 가진 세 가지 "데이터 센터"에 걸쳐 있는 9개의 노드 로컬 개발 클러스터를 정의합니다. 이 클러스터에는 세 개의 해당 데이터 센터에 스트라이프된 3개의 업그레이드 도메인도 있습니다. 클러스터 매니페스트 템플릿에서는 다음과 같이 나타납니다.

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

독립 실행형 배포의 경우 ClusterConfig.json을 통해

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Azure 배포에서 장애 도메인과 업그레이드 도메인은 Azure에 의해 할당됩니다. 따라서 Azure에 대한 인프라 옵션 내 노드 및 역할 정의에 장애 도메인 또는 업그레이드 도메인 정보가 포함되지 않습니다.
>
>

## <a name="placement-constraints-and-node-properties"></a>배치 제약 조건 및 노드 속성
종종(실제로 대부분의 경우) 특정 워크로드가 클러스터의 특정 노드 또는 특정 노드 집합에서만 실행되도록 하려 할 것입니다. 예를 들어, 일부 워크로드는GPU 또는 SSD가 필요할 수 있습니다. 특정 워크로드에 대한 하드웨어를 대상으로 하는 예제는 거의 모든 n계층 아키텍처입니다. 이러한 아키텍처에서 특정 컴퓨터는 응용 프로그램의 프런트 엔드/인터페이스 쪽으로 제공하고 따라서 인터넷에 노출될 가능성이 높습니다. 보통 다른 하드웨어 리소스를 가진 컴퓨터의 다른 집합은 계산 또는 저장소 계층의 작업을 처리하고 일반적으로 인터넷에 노출되지 않습니다. Service Fabric은 마이크로 서비스 환경에서 특정 워크로드가 특정 하드웨어 구성에서 실행될 것을 기대합니다. 예를 들어,

* 기존 n 계층 응용 프로그램은 서비스 패브릭 환경으로 "리프트 및 이동"되었습니다.
* 성능, 규모 또는 보안상의 이유 등으로 특정 하드웨어에서 워크로드를 실행하려고 합니다.
* 워크로드는 정책 또는 리소스 소비를 이유로 다른 워크로드로부터 격리되어야 합니다.

이러한 유형의 구성을 지원하기 위해 Service Fabric에는 노드에 적용될 수 있는 첫 번째 클래스 개념이 있습니다. 이를 배치 제약 조건이라고 합니다. 배치 제약 조건은 특정 서비스를 실행할 위치를 나타내는 데 사용할 수 있습니다. 제약 조건 집합은 확장 가능하며 모든 키/값 쌍을 사용할 수 있습니다.

<center>
![다양한 클러스터 레이아웃 워크로드][Image5]
</center>

노드에서 서로 다른 키/값 태그는 노드 배치 *속성* 또는 노드 속성이라고 합니다. 노드 속성에서 지정된 값은 문자열, 부울 또는 기호가 있는 long이 될 수 있습니다. 서비스가 클러스터에서 실행할 수 있는 위치를 제한하기 때문에 서비스의 문은 배치 *제약 조건*이라고 합니다. 이 제약 조건은 클러스터의 다른 노드 속성에 작동하는 부울 문일 수 있습니다. 이러한 부울 문의 유효한 선택기는 다음과 같습니다.

1) 특정 문을 만들기 위한 조건부 검사

| 문 | 구문 |
| --- |:---:|
| "같음" | "==" |
| "다음과 같지 않음" | "!=" |
| "다음보다 큼" | ">" |
| "다음보다 크거나 같음" | ">=" |
| "다음보다 작음" | "<" |
| "다음보다 작거나 같음" | "<=" |

2) 그룹화 및 논리 작업에 대한 부울 문

| 문 | 구문 |
| --- |:---:|
| "및" | "&&" |
| "또는" | "&#124;&#124;" |
| "아님" | "!" |
| "단일 문인 그룹" | "()" |

다음은 기본 제약 조건문의 몇 가지 예입니다.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

전체 문이 "True"로 평가되는 노드에만 서비스가 배치될 수 있습니다. 정의된 속성이 없는 노드는 해당 속성을 포함하는 배치 제약 조건과 일치하지 않습니다.

Service Fabric은 사용자가 정의할 필요 없이 자동으로 사용할 수 있는 몇 가지 기본 노드 속성을 정의합니다. 이 문서를 작성하는 현재 각 노드에서 정의된 기본 속성은 **NodeType** 및 **NodeName**입니다. 예를 들어 배치 제약 조건을 `"(NodeType == NodeType03)"`으로 작성할 수 있습니다. 일반적으로 NodeType이 가장 일반적으로 사용되는 속성임을 알게 됩니다. 컴퓨터의 종류와 1:1에 해당하기 때문에 유용합니다. 따라서 일반적인 n계층 응용 프로그램 아키텍처의 워크로드 형식에 해당합니다.

<center>
![배치 제약 조건 및 노드 속성][Image6]
</center>

다음과 같은 노드 속성이 제시된 노드 형식에 대해 정의되었다고 가정해 보겠습니다.

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

독립 실행형 배포의 경우 ClusterConfig.json, Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다. 클러스터의 Azure Resource Manager 템플릿에서 노드 형식 이름과 같은 항목을 매개 변수화하고 "NodeType01" 대신 "[parameters('vmNodeType1Name')]"와 같이 표시합니다.

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

다음과 같이 서비스에 대한 서비스 배치 *제약 조건*을 만들 수 있습니다.

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

NodeType01의 모든 노드가 유효함을 확인한 경우 해당 노드 유형을 선택할 수도 있습니다.

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

배치 제약 조건(곧 설명할 여러 다른 Orchestrator 컨트롤 포함)은 다른 모든 명명된 서비스 인스턴스에 대해 지정됩니다. 업데이트는 이전에 지정된 항목(덮어쓰기)에서 발생합니다.

노드의 속성은 클러스터 정의를 통해 정의되고 따라서 클러스터로 업그레이드하지 않고 업데이트될 수 없습니다. 노드의 속성을 업그레이드하려면 영향을 받는 각 노드를 중단한 다음 돌아와야 합니다.

## <a name="capacity"></a>용량
모든 조정자의 가장 중요한 작업 중 하나는 클러스터에서 리소스 소비를 관리할 수 있도록 돕는 일입니다. 서비스를 효율적으로 실행하려는 경우 일부 노드는 핫인 반면 다른 노드는 콜드 상태여야 합니다. 핫 노드는 리소스 경합 및 성능 저하를 일으키고 콜드 노드는 낭비된 리소스 및 증가한 비용을 나타냅니다. 분산에 대해 설명하기 전에 먼저 노드가 리소스를 소모하지 않도록 하는 방법에 대해 설명하겠습니다.

Service Fabric은 리소스를 `Metrics`으로 나타냅니다. 메트릭은 서비스 패브릭에 대해 설명하려는 논리적 또는 물리적 리소스입니다. 메트릭의 예로는 "WorkQueueDepth" 또는 "MemoryInMb" 등이 있습니다. 메트릭 및 해당 용도를 구성하는 정보는 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

메트릭은 배치 제약 조건 및 노드 속성이 서로 다릅니다. 노드 속성은 노드 자체의 정적인 설명자이고 반면 메트릭은 노드에서 실행되는 경우 노드에 포함되고 해당 서비스가 사용하는 리소스에 관한 정보입니다. 노드 속성은 "HasSSD"일 수 있고 true 또는 false로 설정될 수 있습니다. 해당 SSD에서 사용할 수 있고 서비스에서 사용하는 공간의 양은 "DriveSpaceInMb"와 같은 메트릭일 수 있습니다. 노드는 드라이브에서 예약되지 않은 총 공간의 양에 대해 "DriveSpaceInMb"의 용량을 가집니다. 서비스는 런타임 시 사용하는 메트릭의 양을 보고합니다.

배치 제약 조건 및 노드 속성의 경우와 같이 Service Fabric Cluster Resource Manager가 메트릭 이름의 의미를 이해하지 못한다는 점에 유의합니다. 메트릭 이름은 단순한 문자열입니다. 만든 메트릭 이름이 모호할 경우 단위를 그 일부로 선언하는 것이 좋습니다.

모든 리소스 *분산*을 해제한 경우에도 Service Fabric’s Cluster Resource Manager는 여전히 노드가 해당 용량을 초과하지 않도록 해야 합니다. 일반적으로 전체 클러스터가 가득차지 않으면 가능합니다. 용량은 Cluster Resource Manager가 노드에 있는 리소스의 양을 이해하기 위해 사용하는 또 다른 *제약 조건*입니다. 전체 클러스터에 대해 남은 용량을 추적합니다. 서비스 수준에서 용량과 소비량은 메트릭을 기준으로 표현됩니다. 예를 들어, 메트릭은 "MemoryInMb"이고 지정된 노드에는 2048이라는 "MemoryInMb"에 대한 용량이 있을 수 있습니다. 해당 노드에서 실행되는 일부 서비스는 현재 "MemoryInMb"의 64를 사용하고 있다고 볼 수 있습니다.

런타임 중에 Cluster Resource Manager는 각 노드에 있는 각 리소스의 양 및 남은 양을 추적합니다. 이 작업은 노드 용량의 해당 노드에서 실행되는 각 서비스의 선언된 사용량을 빼서 수행합니다. 이 정보를 통해 Service Fabric Cluster Resource Manager는 노드가 용량을 초과하지 않도록 복제본을 배치하거나 이동시킬 위치를 결정할 수 있습니다.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```
<center>
![클러스터 노드 및 용량][Image7]
</center>

클러스터 매니페스트에 정의된 용량을 확인할 수 있습니다.

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

독립 실행형 배포의 경우 ClusterConfig.json, Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다. 클러스터의 Azure Resource Manager 템플릿에서 노드 형식 이름과 같은 항목을 매개 변수화하고 "NodeType02" 대신 "[parameters('vmNodeType2Name')]"와 같이 표시합니다.

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

또한 서비스의 부하를 동적으로 변경할 수도 있으며 이것이 일반적입니다. 복제본의 부하가 64에서 1024로 변경되었으나 그 때 실행되던 노드에는 512("MemoryInMb" 메트릭)만 남아 있었습니다. 이제 해당 노드에 있는 충분한 공간이 없기 때문에 해당 복제본 또는 인스턴스의 배치가 유효하지 않습니다. 해당 노드에서 복제본 및 인스턴스의 결합된 사용량이 해당 노드 용량을 초과하는 경우 발생할 수도 있습니다. 두 경우 모두에 Cluster Resource Manager가 시작되고 용량 아래로 노드를 낮출 수 있습니다. 해당 노드에서 하나 이상의 복제본 또는 인스턴스를 다른 노드로 이동하여 이를 수행합니다. 복제본을 이동하는 경우 Cluster Resource Manager에서는 이러한 움직임의 비용을 최소화하려고 합니다. 이동 비용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)에서 설명합니다.

## <a name="cluster-capacity"></a>클러스터 용량
그렇다면 전체 클러스터 용량이 꽉 차지 않도록 하려면 어떻게 해야 합니까? 물론 동적 로드를 사용하는 경우 Cluster Resource Manager가 수행할 수 있는 작업은 많지 않습니다. 서비스에서는 Cluster Resource Manager에서 수행한 작업과 독립적으로 해당 부하가 급증할 수 있습니다. 즉, 오늘은 여유가 많은 클러스터가 내일은 수요가 많아져 전력이 부족해질 수 있습니다. 즉, 기본적인 문제를 방지하기 위해 반영된 몇 가지 컨트롤이 있습니다. 가장 먼저 할 수 있는 작업은 클러스터가 꽉 차도록 만드는 워크로드가 생성되지 않도록 하는 것입니다.

상태 비저장 서비스를 만들기로 이동하고 이와 관련된 일부 부하가 있다고 가정해 보겠습니다(기본 및 동적 부하 보고에 대해서는 나중에 자세히 설명). 서비스가 "DiskSpaceInMb" 메트릭을 고려하지 않는다고 가정해 봅니다. 서비스의 모든 인스턴스에 대해 5단위의 "DiskSpaceInMb"를 사용하게 된다고 가정하겠습니다. 3개의 서비스 인스턴스를 만들려고 합니다. 잘하셨습니다. 즉, 이러한 서비스 인스턴스를 만들기 위해서는 클러스터에서 15단위의 "DiskSpaceInMb"가 있어야 합니다. Cluster Resource Manager는 각 메트릭의 전체 용량 및 소비를 지속적으로 계산하므로 클러스터에 충분한 공간이 있는지 쉽게 확인할 수 있습니다. 공간이 충분하지 않으면 Cluster Resource Manager는 만들기 서비스 호출을 거부합니다.

요구 사항은 사용할 수 있는 15단위이기 때문에 여러 가지 방법으로 이 공간을 할당할 수 있습니다. 예를 들어, 다른 15개의 노드에서 남은 1단위의 용량이나 다른 5개의 노드에서 남은 3단위의 용량일 수 있습니다. 3개의 노드에서 5단위를 사용할 수 있도록 Cluster Resource Manager가 작업을 다시 정렬할 수 있다면 결과적으로 서비스를 배치하게 됩니다. 전체 클러스터가 거의 꽉 차거나, 서비스가 매우 "비대"하거나, 둘 모두인 경우가 아니면 이러한 재배열은 대부분 가능합니다.

## <a name="buffered-capacity"></a>버퍼링된 용량
Cluster Resource Manager에 포함된 전체 클러스터 용량 관리에 도움이 되는 또 다른 개념은 각 노드에서 지정된 용량에 일부 예약된 버퍼를 추가하는 것입니다. 버퍼링된 용량을 사용하면 업그레이드 및 노드 오류 중에 서비스를 배치하는 데에만 사용되도록 전체 노드 용량 중 일부를 예약할 수 있습니다. 현재 클러스터 정의를 통해 모든 노드에 대해 메트릭을 기준으로 버퍼가 전역적으로 지정됩니다. 예약된 용량에 선택한 값은 클러스터에 있는 장애 도메인 및 업그레이드 도메인 수와 원하는 오버헤드의 양의 함수입니다. 장애 도메인과 업그레이드 도메인이 증가하면 버퍼링된 용량에 대해 낮은 값을 선택할 수 있습니다. 더 많은 도메인이 있는 경우 업그레이드 및 오류 중 사용할 수 없는 클러스터의 수가 감소할 수 있습니다. 메트릭에 대해 노드 용량을 지정한 경우 버퍼율을 지정할 수도 있습니다.

버퍼링된 용량을 지정하는 방법의 예는 다음과 같습니다.

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

클러스터가 메트릭에 대해 버퍼링된 용량을 소모한 경우 새 서비스를 만드는 데 실패합니다. 이렇게 하면 클러스터가 충분한 예비 오버헤드를 유지하므로 업그레이드 및 오류로 인해 노드가 용량을 초과하지 않습니다. 버퍼링된 용량은 선택적이지만 메트릭에 대한 용량을 정의하는 모든 클러스터에 권장됩니다.

Cluster Resource Manager는 PowerShell 및 쿼리 API를 통해 이 정보를 노출합니다. 그러면 클러스터에서 사용 중인 모든 메트릭에 대한 버퍼링된 용량 설정, 총 용량 및 현재 사용량을 확인할 수 있습니다. 여기에서 출력의 예제를 볼 수 있습니다.

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
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

## <a name="next-steps"></a>다음 단계
* Cluster Resource Manager 내의 아키텍처 및 정보 흐름에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-architecture.md)를 확인하세요.
* 조각 모음 메트릭 정의는 노드의 부하를 분배하는 대신 통합하는 한 가지 방법입니다. 조각 모음을 구성하는 방법에 대해 알아보려면 [이 문서](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

