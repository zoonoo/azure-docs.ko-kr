---
title: 클러스터 리소스 관리자를 사용하여 클러스터 설명
description: 클러스터 리소스 관리자에 대한 오류 도메인, 업그레이드 도메인, 노드 속성 및 노드 용량을 지정하여 서비스 패브릭 클러스터를 설명합니다.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258773"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>클러스터 리소스 관리자를 사용하여 서비스 패브릭 클러스터 설명
Azure 서비스 패브릭의 클러스터 리소스 관리자 기능은 클러스터를 설명하기 위한 몇 가지 메커니즘을 제공합니다.

* 장애 도메인
* 업그레이드 도메인
* 노드 속성
* 노드 용량

런타임 동안 클러스터 리소스 관리자는 이 정보를 사용하여 클러스터에서 실행되는 서비스의 가용성을 높입니다. 이러한 중요한 규칙을 적용하는 동시에 클러스터 내에서 리소스 소비를 최적화하려고 시도합니다.

## <a name="fault-domains"></a>장애 도메인
장애 도메인은 통합된 오류는 영역입니다. 단일 컴퓨터는 장애 도메인입니다. 전원 공급 장치 오류에서 드라이브 오류, 나쁜 NIC 펌웨어에 이르기까지 여러 가지 이유로 자체적으로 실패할 수 있습니다. 

동일한 이더넷 스위치에 연결된 컴퓨터가 동일한 오류 도메인에 있습니다. 단일 전원을 공유하거나 단일 위치에서 공유하는 컴퓨터도 마찬가지입니다. 

하드웨어 오류가 겹치는 것은 당연한 일이므로 오류 도메인은 본질적으로 계층적입니다. 서비스 패브릭에서 URI로 표시됩니다.

Service Fabric은 이 정보를 사용하여 서비스를 안전하게 배치하기 때문에 장애 도메인을 올바르게 설정하는 것이 중요합니다. 서비스 패브릭은 장애 도메인(일부 구성 요소의 오류로 인해 발생한)이 손실되어 서비스가 다운되도록 서비스를 배치하고 싶지 않습니다. 

Azure 환경에서 Service Fabric은 환경에서 제공하는 오류 도메인 정보를 사용하여 사용자 대신 클러스터의 노드를 올바르게 구성합니다. Service Fabric의 독립 실행형 인스턴스의 경우 클러스터가 설정될 때 장애 도메인이 정의됩니다. 

> [!WARNING]
> 서비스 패브릭에 제공된 장애 도메인 정보가 정확해야 합니다. 예를 들어 Service Fabric 클러스터의 노드가 5개의 실제 호스트에서 실행되는 10개의 가상 시스템 내에서 실행되고 있다고 가정해 보겠습니다. 이 경우 10개의 가상 머신이 있지만 별도의 5개(최상위 수준) 장애 도메인만 있습니다. 동일한 물리적 호스트를 공유하면 VM이 물리적 호스트에 실패할 경우 VM이 조정된 실패를 경험하기 때문에 VM이 동일한 루트 오류 도메인을 공유하게 됩니다.  
>
> 서비스 패브릭은 노드의 오류 도메인이 변경되지 않을 것으로 예상합니다. [HA-VM과](https://technet.microsoft.com/library/cc967323.aspx)같은 VM의 고가용성을 보장하는 다른 메커니즘은 서비스 패브릭과 충돌할 수 있습니다. 이러한 메커니즘은 한 호스트에서 다른 호스트로 VM을 투명하게 마이그레이션합니다. VM 내부의 실행 중인 코드를 다시 구성하거나 알리지 않습니다. 따라서 서비스 패브릭 클러스터를 실행하기 위한 환경으로 *지원되지 않습니다.* 
>
> Service Fabric은 사용되는 유일한 고가용성 기술이어야 합니다. 라이브 VM 마이그레이션 및 SA와 같은 메커니즘은 필요하지 않습니다. 이러한 메커니즘이 서비스 패브릭과 함께 사용되는 경우 응용 프로그램 가용성과 안정성을 _줄일_ 수 있습니다. 그 이유는 추가복잡성을 도입하고, 중앙 집중식 오류 소스를 추가하며, 서비스 패브릭의 안정성 및 가용성 전략과 충돌하는 안정성을 사용하기 때문입니다. 
>
>

다음 그래픽에서는 오류 도메인에 기여하는 모든 엔터티의 색상을 채점하고 그 결과로 발생하는 모든 다른 오류 도메인을 나열합니다. 이 예제에는 데이터센터("DC"), 랙("R") 및 블레이드("B")가 있습니다. 각 블레이드에 두 개 이상의 가상 시스템이 있는 경우 오류 도메인 계층 구조에 다른 계층이 있을 수 있습니다.

<center>

![장애 도메인을 통해 구성된 노드][Image1]
</center>

런타임 동안 Service Fabric 클러스터 리소스 관리자는 클러스터의 장애 도메인을 고려하고 레이아웃을 계획합니다. 서비스에 대한 상태 정보 복제본 또는 상태 비없는 인스턴스가 배포되어 별도의 오류 도메인에 있습니다. 장애 도메인 간에 서비스를 배포하면 계층 구조의 모든 수준에서 장애 도메인이 실패할 때 서비스 가용성이 손상되지 않습니다.

클러스터 리소스 관리자는 장애 도메인 계층 구조에 있는 레이어 수를 신경 쓰지 않습니다. 계층 구조의 한 부분이 손실되어 계층에서 실행 중인 서비스에 영향을 주지 않도록 합니다. 

오류 도메인 계층 구조의 각 깊이 수준에 동일한 수의 노드가 있는 것이 가장 좋습니다. 클러스터에서 장애 도메인의 "트리"가 불균형한 경우 클러스터 리소스 관리자가 최상의 서비스 할당을 파악하기가 더 어려워집니다. 불균형 한 오류 도메인 레이아웃 일부 도메인의 손실 다른 도메인 보다 더 많은 서비스의 가용성에 영향을 의미 합니다. 결과적으로 클러스터 리소스 관리자는 다음 두 가지 목표 사이에서 갈라져 있습니다. 

* 서비스를 배치하여 "무거운" 도메인에서 컴퓨터를 사용하려고 합니다. 
* 도메인이 손실되어 문제가 발생하지 않도록 다른 도메인에 서비스를 배치하려고 합니다. 

분산되지 않은 도메인은 어떤 모양일까요? 다음 다이어그램은 두 개의 서로 다른 클러스터 레이아웃을 보여 주십습니다. 첫 번째 예에서 노드는 장애 도메인에 균등하게 분산됩니다. 두 번째 예에서 한 오류 도메인에는 다른 오류 도메인보다 더 많은 노드가 있습니다. 

<center>

![두 개의 다른 클러스터 레이아웃][Image2]
</center>

Azure에서 노드를 포함하는 오류 도메인의 선택이 관리됩니다. 그러나 프로비저닝하는 노드 수에 따라 다른 노드보다 노드가 더 많은 오류 도메인으로 끝날 수 있습니다. 

예를 들어 클러스터에 5개의 장애 도메인이 있지만 노드**유형(NodeType)에**대해 7개의 노드를 프로비전한다고 가정해 보겠습니다. 이 경우 처음 두 개의 오류 도메인은 더 많은 노드로 끝납니다. 인스턴스가 몇 개만 있는 더 많은 **NodeType** 인스턴스를 계속 배포하면 문제가 더 악화됩니다. 따라서 각 노드 유형의 노드 수가 오류 도메인 수의 배수인 것이 좋습니다.

## <a name="upgrade-domains"></a>업그레이드 도메인
업그레이드 도메인은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 레이아웃을 이해하는 데 도움이 되는 또 다른 기능입니다. 업그레이드 도메인은 동시에 업그레이드되는 노드 집합을 정의합니다. 업그레이드 도메인은 클러스터 리소스 관리자가 업그레이드와 같은 관리 작업을 이해하고 오케스트레이션하는 데 도움이 됩니다.

업그레이드 도메인은 오류 도메인과 매우 유사하지만 몇 가지 주요 차이점이 있습니다. 첫째, 조정된 하드웨어 오류 영역은 장애 도메인을 정의합니다. 반면 업그레이드 도메인은 정책에 의해 정의됩니다. 환경이 숫자를 지정하도록 하는 대신 원하는 수를 결정할 수 있습니다. 노드만큼 많은 업그레이드 도메인을 가질 수 있습니다. 장애 도메인과 업그레이드 도메인의 또 다른 차이점은 업그레이드 도메인이 계층적이지 않다는 점입니다. 대신, 그들은 더 간단한 태그처럼. 

다음 다이어그램은 세 개의 장애 도메인에 스트라이프된 세 개의 업그레이드 도메인을 보여 주며 있습니다. 또한 상태 정리 서비스의 세 가지 다른 복제본에 대해 하나의 가능한 배치를 보여 주며, 여기서 각 복제본은 서로 다른 오류 및 업그레이드 도메인으로 끝납니다. 이 배치를 사용하면 서비스 업그레이드 중에 장애 도메인이 손실되고 코드와 데이터의 복사본이 하나만 있습니다.  

<center>

![결함이 있는 위치 및 업그레이드 도메인][Image3]
</center>

많은 수의 업그레이드 도메인을 갖는 데는 장단점이 있습니다. 업그레이드 도메인이 많을수록 업그레이드의 각 단계가 더 세분화되고 더 적은 수의 노드 또는 서비스에 영향을 미칩니다. 한 번에 이동하는 서비스가 적어 시스템에 이탈이 줄어듭니다. 이는 업그레이드 중에 발생하는 모든 문제의 영향을 덜 받는 서비스 이므로 안정성을 향상시키는 경향이 있습니다. 또한 업그레이드 도메인이 많을수록 업그레이드의 영향을 처리하기 위해 다른 노드에서 사용 가능한 버퍼가 덜 필요하다는 의미입니다. 

예를 들어 5개의 업그레이드 도메인이 있는 경우 각 노드가 트래픽의 약 20%를 처리합니다. 업그레이드를 위해 해당 업그레이드 도메인을 중단해야 하는 경우 해당 로드는 일반적으로 어딘가로 이동해야 합니다. 나머지 업그레이드 도메인이 4개이므로 각 도메인에는 전체 트래픽의 약 5%를 수용할 수 있는 공간이 있어야 합니다. 업그레이드 도메인이 많을수록 클러스터의 노드에 대한 버퍼가 줄어듭니다. 

대신 10개의 업그레이드 도메인이 있는지 고려합니다. 이 경우 각 업그레이드 도메인은 전체 트래픽의 약 10%만 처리합니다. 업그레이드가 클러스터를 통과하는 경우 각 도메인은 전체 트래픽의 약 1.1%에 대해서만 공간을 확보해야 합니다. 업그레이드 도메인이 많을수록 일반적으로 예약된 용량이 덜 필요하기 때문에 더 높은 사용률로 노드를 실행할 수 있습니다. 오류 도메인도 마찬가지입니다.  

많은 업그레이드 도메인을 갖는 단점은 업그레이드가 더 오래 걸리는 경향이 있다는 것입니다. 서비스 패브릭은 업그레이드 도메인이 완료된 후 잠시 기다렸다가 다음 업그레이드를 시작하기 전에 검사를 수행합니다. 이러한 지연은 업그레이드가 진행되기 전에 업그레이드로 인한 문제를 감지할 수 있게 합니다. 이러한 단점은 한 번에 너무 많은 서비스에 영향을 주는 잘못된 변경 사항을 방지하기 때문에 받아들일 만합니다.

너무 적은 업그레이드 도메인의 존재는 많은 부정적인 부작용이 있다. 각 업그레이드 도메인이 다운되어 업그레이드되는 동안 전체 용량의 상당 부분을 사용할 수 없습니다. 예를 들어 업그레이드 도메인이 세 개뿐인 경우 한 번에 전체 서비스 또는 클러스터 용량의 약 1/3을 줄입니다. 워크로드를 처리하기 위해 클러스터의 나머지 부분에 충분한 용량이 필요하기 때문에 서비스를 한 번에 너무 많이 중단하는 것은 바람직하지 않습니다. 해당 버퍼를 유지 관리한다는 것은 정상적인 작업 중에 해당 노드가 그렇지 않은 경우보다 로드가 적다는 것을 의미합니다. 이로 인해 서비스 실행 비용이 증가합니다.

환경에서 장애 또는 업그레이드 도메인의 총 수는 실제 제한이나 중첩 방법에 대한 제약이 없습니다. 그러나 일반적인 패턴이 있습니다.

- 장애 도메인 및 업그레이드 도메인 매핑 1:1
- 노드당 하나의 업그레이드 도메인(물리적 또는 가상 OS 인스턴스)
- 장애 도메인 및 업그레이드 도메인이 대각선 아래로 실행되는 컴퓨터가 있는 매트릭스를 형성하는 "스트라이프" 또는 "매트릭스" 모델

<center>

![오류 및 업그레이드 도메인의 레이아웃][Image4]
</center>

어떤 레이아웃을 선택할지 에 대한 최선의 대답은 없습니다. 각각에 장점 및 단점이 있습니다. 예를 들어 1FD:1UD 모델의 설치는 간단합니다. 노드 모델당 하나의 업그레이드 도메인 모델은 사람들이 사용하는 것과 가장 비슷합니다. 업그레이드 하는 동안 각 노드는 독립적으로 업데이트 됩니다. 이는 이전에 수동으로 작은 컴퓨터 집합을 업그레이드한 방법과 비슷합니다.

가장 일반적인 모델은 FD/UD 행렬로, 장애 도메인과 업그레이드 도메인이 테이블을 형성하고 노드가 대각선을 따라 시작됩니다. 이는 Azure의 Service Fabric 클러스터에서 기본적으로 사용되는 모델입니다. 노드가 많은 클러스터의 경우 모든 것이 조밀한 행렬 패턴처럼 보입니다.

> [!NOTE]
> Azure에서 호스팅되는 서비스 패브릭 클러스터는 기본 전략 변경을 지원하지 않습니다. 독립 실행형 클러스터만 이 사용자 지정을 제공합니다.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>장애 및 업그레이드 도메인 제약 조건 및 결과 동작
### <a name="default-approach"></a>기본 접근 방식
기본적으로 클러스터 리소스 관리자는 오류 및 업그레이드 도메인 간에 서비스 균형을 유지합니다. 이것이 [제약 조건](service-fabric-cluster-resource-manager-management-integration.md)으로 모델링됩니다. 오류 및 업그레이드 도메인에 대한 제약 조건은 "지정된 서비스 파티션의 경우 동일한 두 도메인 간에 서비스 개체 수(상태 비수기 서비스 인스턴스 또는 상태 제공 서비스 복제본)에 1개보다 큰 차이가 없어야 합니다. 계층 구조의 수준입니다."

이 제약 조건이 "최대 차이" 보장을 제공한다고 가정해 보겠습니다. 오류 및 업그레이드 도메인에 대한 제약 조건은 규칙을 위반하는 특정 이동 또는 배열을 방지합니다.

예를 들어 5개의 장애 도메인과 5개의 업그레이드 도메인으로 구성된 6개의 노드가 있는 클러스터가 있다고 가정해 보겠습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

이제 **TargetReplicaSetSize(또는** 상태 비수기 서비스의 경우 **InstanceCount)** 값이 5인 서비스를 만듭니다. 복제본은 N1-N5에 생성됩니다. 실제로 많은 서비스를 만들더라도 N6은 절대 사용되지 않습니다. 그렇지만 그 이유는 무엇일까요? N6을 선택하는 경우 현재 레이아웃 및 발생하는 상황 간의 차이를 살펴보겠습니다.

오류 및 업그레이드 도메인당 총 복제본 수와 레이아웃은 다음과 같습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 및 업그레이드 도메인당 노드 측면에서 균형을 이룹니다. 또한 오류 및 업그레이드 도메인당 복제본 수 측면에서도 균형을 이룹니다. 각 도메인에는 동일한 수의 노드 및 동일한 수의 복제본이 있습니다.

이제 N2 대신 N6를 사용하는 경우 발생하는 결과를 살펴보겠습니다. 복제본은 어떻게 배포되나요?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

이 레이아웃은 오류 도메인 제약 조건에 대한 "최대 차이" 보장에 대한 정의를 위반합니다. FD0에는 두 개의 복제본이 있지만 FD1에는 0이 있습니다. FD0과 FD1의 차이는 총 2이며, 이는 1의 최대 차이보다 큽니까. 제약 조건이 위반되므로 클러스터 리소스 관리자는 이 배열을 허용하지 않습니다. 마찬가지로 N2와 N6(N1 및 N2 대신)을 선택하면 다음과 같은 것을 얻을 수 있습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃은 오류 도메인측면에서 균형을 이룹니다. 그러나 UD0에는 복제본이 없고 UD1에는 두 개가 있기 때문에 업그레이드 도메인 제약 조건을 위반하고 있습니다. 이 레이아웃도 유효하지 않으며 클러스터 리소스 관리자에서 선택하지 않습니다.

이 상태 저장 복제본 또는 상태 비저장 인스턴스 배포 방식은 가능한 최고의 내결함성을 제공합니다. 하나의 도메인이 다운되면 최소한의 복제/인스턴스가 손실됩니다. 

반면 이 방법은 지나치게 엄격하여 클러스터가 모든 리소스를 활용하지 못할 수 있습니다. 특정 클러스터 구성에서는 특정 노드를 사용할 수 없습니다. 이로 인해 Service Fabric이 서비스를 배치하지 않아 경고 메시지가 발생할 수 있습니다. 이전 예제에서는 일부 클러스터 노드를 사용할 수 없습니다(예제의 N6). 해당 클러스터(N7-N10)에 노드를 추가한 경우에도 오류 및 업그레이드 도메인에 대한 제약 조건으로 인해 복제본/인스턴스가 N1-N5에만 배치됩니다. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>대안

클러스터 리소스 관리자는 오류 및 업그레이드 도메인에 대한 제약 조건의 다른 버전을 지원합니다. 최소한의 안전 수준을 보장하면서 배치할 수 있습니다. 대체 제약 조건은 다음과 같이 명시할 수 있습니다. 이 제약 조건이 "쿼럼 안전" 보장을 제공한다고 가정해 보겠습니다. 

> [!NOTE]
> 상태 저장 서비스의 경우 대부분의 파티션 복제본이 동시에 중지되는 상황에서 *쿼럼 손실*을 정의합니다. 예를 들어 **TargetReplicaSetSize가** 5인 경우 세 복제본 집합은 쿼럼을 나타냅니다. 마찬가지로 **TargetReplicaSetSize가** 6인 경우 쿼럼에 4개의 복제본이 필요합니다. 두 경우 모두 파티션이 정상적으로 작동하려면 두 개 이상의 복제본을 동시에 다운할 수 없습니다. 
>
> 상태 비수기 서비스의 경우 *쿼럼 손실과*같은 것은 없습니다. 상태 비수기 서비스는 대부분의 인스턴스가 동시에 중단되더라도 정상적으로 작동합니다. 따라서 이 문서의 나머지 부분에서 상태 관리 서비스에 중점을 둡니다.
>

이전 예제로 돌아갑시다. 제약 조건의 "쿼럼 안전" 버전을 사용하면 세 레이아웃모두 유효합니다. 두 번째 레이아웃에서 FD0이 실패하거나 UD1이 세 번째 레이아웃에서 실패한 경우에도 파티션에는 쿼럼이 계속 있습니다. (복제본의 대부분은 여전히 최대입니다.) 이 버전의 제약 조건으로 N6을 거의 항상 활용할 수 있습니다.

"쿼럼 안전" 접근 방식은 "최대 차이" 접근 방식보다 더 많은 유연성을 제공합니다. 그 이유는 거의 모든 클러스터 토폴로지에서 유효한 복제본 배포를 쉽게 찾을 수 있기 때문입니다. 그러나 이 접근 방식은 일부 오류가 다른 오류보다 심각하기 때문에 최고의 내결함성을 보장할 수 없습니다. 

최악의 경우 하나의 도메인과 하나의 추가 복제본이 실패하면 대부분의 복제본이 손실될 수 있습니다. 예를 들어 5개의 복제본 또는 인스턴스로 쿼럼을 잃는 데 세 번의 오류가 필요한 대신 두 번의 실패로 과반수를 잃을 수 있습니다. 

### <a name="adaptive-approach"></a>적응형 접근 방식
두 방법 모두 강점과 약점을 가지고 있기 때문에 이 두 가지 전략을 결합한 적응형 접근 방식을 도입했습니다.

> [!NOTE]
> 서비스 패브릭 버전 6.2부터 시작되는 기본 동작입니다. 
> 
> 적응형 접근 방식은 기본적으로 "최대 차이" 논리를 사용하다가 필요할 때만 "쿼럼 안전" 논리로 전환합니다. 클러스터 리소스 관리자는 클러스터 및 서비스가 구성되는 방식을 확인하여 필요한 전략을 자동으로 파악합니다.
> 
> 클러스터 리소스 관리자는 서비스에 대해 "쿼럼 기반" 논리를 사용하여 다음 두 가지 조건이 모두 true여야 합니다.
>
> * 서비스에 대한 **TargetReplicaSetSize는** 장애 도메인 수와 업그레이드 도메인 수에 따라 균등하게 나눌 수 있습니다.
> * 노드 수는 오류 도메인 수에 업그레이드 도메인 수를 곱한 값보다 적거나 같습니다.
>
> 쿼럼 손실이 상태 비수기 서비스와 관련이 없더라도 클러스터 리소스 관리자는 상태 비수기 및 상태 비관리 서비스 모두에 이 방법을 사용합니다.

이전 예제로 돌아가 이제 클러스터에 8개의 노드가 있다고 가정해 보겠습니다. 클러스터는 여전히 5개의 장애 도메인과 5개의 업그레이드 도메인으로 구성되며 해당 클러스터에서 호스팅되는 서비스의 **TargetReplicaSetSize** 값은 5개로 유지됩니다. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

필요한 모든 조건이 충족되므로 클러스터 리소스 관리자는 "쿼럼 기반" 논리를 사용하여 서비스를 배포합니다. 이를 통해 N6-N8을 사용할 수 있습니다. 이 경우 가능한 서비스 배포 중 하나는 다음과 같을 수 있습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

서비스의 **TargetReplicaSetSize** 값이 4개로 줄어든 경우 클러스터 리소스 관리자는 변경 사항을 알 수 있습니다. **TargetReplicaSetSize오류** 도메인 수와 업그레이드 도메인 수를 더 이상 구분할 수 없기 때문에 "최대 차이" 논리를 사용하여 다시 시작됩니다. 결과적으로 N1-N5 노드에 나머지 네 개의 복제본을 배포하기 위해 특정 복제본 이동이 발생합니다. 이렇게 하면 오류 도메인 및 업그레이드 도메인 논리의 "최대 차이" 버전이 위반되지 않습니다. 

이전 레이아웃에서 **TargetReplicaSetSize** 값이 5이고 N1이 클러스터에서 제거된 경우 업그레이드 도메인 수는 4개가 됩니다. 다시 말하지만, 클러스터 리소스 관리자는 업그레이드 도메인 수가 서비스의 **TargetReplicaSetSize** 값을 더 이상 균등하게 분할하지 않기 때문에 "최대 차이" 논리를 사용하기 시작합니다. 따라서 복제 R1을 다시 빌드할 때 오류 및 업그레이드 도메인에 대한 제약 조건이 위반되지 않도록 N4에 착륙해야 합니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |해당 없음 |해당 없음 |해당 없음 |해당 없음 |해당 없음 |해당 없음 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>장애 및 업그레이드 도메인 구성
Azure 에서 호스팅되는 서비스 패브릭 배포에서 장애 도메인 및 업그레이드 도메인이 자동으로 정의됩니다. Service Fabric은 Azure의 환경 정보를 선택하고 사용합니다.

자체 클러스터를 만들거나 개발 중 특정 토폴로지(를 실행하려는 경우)는 장애 도메인을 제공하고 도메인 정보를 직접 업그레이드할 수 있습니다. 이 예제에서는 3개의 데이터 센터(각각 3개의 랙)에 걸쳐 있는 9노드 로컬 개발 클러스터를 정의합니다. 또한 이 클러스터에는 세 개의 데이터 센터에 스트라이프된 세 개의 업그레이드 도메인이 있습니다. ClusterManifest.xml의 구성예는 다음과 같습니다.

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
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

이 예제에서는 독립 실행형 배포에 ClusterConfig.json을 사용합니다.

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
> Azure 리소스 관리자를 통해 클러스터를 정의하는 경우 Azure는 장애 도메인및 업그레이드 도메인을 할당합니다. 따라서 Azure Resource Manager 템플릿의 노드 유형 및 가상 시스템 크기 집합정의 정의에는 장애 도메인 또는 업그레이드 도메인에 대한 정보가 포함되지 않습니다.
>

## <a name="node-properties-and-placement-constraints"></a>노드 속성 및 배치 제약 조건
때로는 대부분의 경우 특정 워크로드가 클러스터의 특정 유형의 노드에서만 실행되도록 해야 합니다. 예를 들어 일부 워크로드에는 GPU 또는 SSD가 필요할 수 있으며 다른 워크로드에는 GPU가 필요할 수 있으며 그렇지 않은 워크로드도 있습니다. 

하드웨어를 특정 워크로드로 타겟팅하는 좋은 예는 거의 모든 n 계층 아키텍처입니다. 특정 컴퓨터는 응용 프로그램의 프런트 엔드 또는 API 제공 측으로 사용되며 클라이언트 또는 인터넷에 노출됩니다. 종종 서로 다른 하드웨어 리소스를 사용하는 다양한 머신에서 컴퓨팅 또는 스토리지 계층의 작업을 처리합니다. 이러한 컴퓨터는 대개 클라이언트 또는 인터넷에 직접 노출되지 _않습니다_. 

Service Fabric은 경우에 따라 특정 하드웨어 구성에서 특정 워크로드를 실행해야 할 수 있습니다. 예를 들어:

* 기존 n 계층 응용 프로그램이 서비스 패브릭 환경으로 "해제 및 이동"되었습니다.
* 성능, 규모 또는 보안 격리를 위해 특정 하드웨어에서 워크로드를 실행해야 합니다.
* 정책 또는 리소스 소비를 위해 워크로드를 다른 워크로드에서 격리해야 합니다.

이러한 종류의 구성을 지원하기 위해 Service Fabric에는 노드에 적용할 수 있는 태그가 포함되어 있습니다. 이러한 태그를 *노드 속성*이라고 합니다. *배치 제약 조건은* 하나 이상의 노드 속성에 대해 선택한 개별 서비스에 연결된 명령문입니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다. 제약 조건 집합은 확장할 수 있습니다. 모든 키/값 쌍이 작동할 수 있습니다. 

<center>

![클러스터 레이아웃에 대한 다양한 워크로드][Image5]
</center>

### <a name="built-in-node-properties"></a>기본 제공 노드 속성
Service Fabric은 자동으로 사용할 수 있는 일부 기본 노드 속성을 정의하므로 정의할 필요가 없습니다. 각 노드에서 정의된 기본 속성은 **NodeType** 및 **NodeName**입니다. 

예를 들어 배치 제약 조건을 `"(NodeType == NodeType03)"`로 작성할 수 있습니다. **NodeType은** 일반적으로 사용되는 속성입니다. 컴퓨터 유형과 1:1에 해당하므로 유용합니다. 각 머신의 종류는 기존의 n 계층 애플리케이션에서 워크로드 유형에 해당합니다.

<center>

![배치 제약 조건 및 노드 속성][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>배치 제약 조건 및 노드 속성 구문 
노드 속성에 지정된 값은 문자열, 부울 또는 서명된 긴 값일 수 있습니다. 서비스의 문을 배치 *제약 조건이라고* 하는데, 이는 클러스터에서 서비스를 실행할 수 있는 위치를 제한하기 때문입니다. 제약 조건은 클러스터의 노드 속성에서 작동하는 모든 부울 문일 수 있습니다. 이러한 부울 문의 유효한 선택기는 다음과 같습니다.

* 특정 문을 만드는 조건부 검사:

  | 인수를 제거합니다. | 구문 |
  | --- |:---:|
  | "같음" | "==" |
  | "다음과 같지 않음" | "!=" |
  | "다음보다 큼" | ">" |
  | "다음보다 크거나 같음" | ">=" |
  | "다음보다 작음" | "<" |
  | "다음보다 작거나 같음" | "<=" |

* 그룹화 및 논리 작업에 대한 부울 문:

  | 인수를 제거합니다. | 구문 |
  | --- |:---:|
  | "및" | "&&" |
  | "또는" | "&#124;&#124;" |
  | "아님" | "!" |
  | "단일 문인 그룹" | "()" |

다음은 기본 제약 조건 문의 몇 가지 예입니다.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

전체 배치 제약 조건 문이 "True"로 평가되는 노드에만 서비스가 배치될 수 있습니다. 정의된 속성이 없는 노드는 속성을 포함하는 배치 제약 조건과 일치하지 않습니다.

ClusterManifest.xml의 노드 유형에 대해 다음 노드 속성이 정의되었다고 가정해 보겠습니다.

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

다음 예제에서는 독립 실행형 배포에 대해 ClusterConfig.json을 통해 정의된 노드 속성 또는 Azure 호스팅 클러스터에 대한 Template.json을 보여 주습니다. 

> [!NOTE]
> Azure 리소스 관리자 템플릿에서 노드 유형은 일반적으로 매개 변수화됩니다. NodeType01이 아닌 것처럼 `"[parameters('vmNodeType1Name')]"` 보입니다.
>

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

다음과 같이 서비스에 대한 서비스 배치 *제약 조건을* 만들 수 있습니다.

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

NodeType01의 모든 노드가 유효한 경우 제약 조건이 `"(NodeType == NodeType01)"`있는 해당 노드 유형을 선택할 수도 있습니다.

서비스의 배치 제약 조건은 런타임 중에 동적으로 업데이트할 수 있습니다. 필요한 경우 클러스터에서 서비스를 이동하고 요구 사항을 추가 및 제거할 수 있습니다. 서비스 패브릭은 이러한 유형의 변경이 이루어지는 경우에도 서비스를 유지하고 사용할 수 있도록 합니다.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

배치 제약 조건은 명명된 모든 서비스 인스턴스에 대해 지정됩니다. 업데이트는 이전에 지정된 항목(덮어쓰기)에서 발생합니다.

클러스터 정의는 노드의 속성을 정의합니다. 노드의 속성을 변경하려면 클러스터 구성을 업그레이드해야 합니다. 노드 속성을 업그레이드하려면 영향을 받는 각 노드를 다시 시작하여 새 속성을 보고해야 합니다. 서비스 패브릭은 이러한 롤링 업그레이드를 관리합니다.

## <a name="describing-and-managing-cluster-resources"></a>클러스터 리소스 설명 및 관리
모든 조정자의 가장 중요한 작업 중 하나는 클러스터에서 리소스 소비를 관리할 수 있도록 돕는 일입니다. 클러스터 리소스 관리는 여러 가지 다른 작업을 의미할 수 있습니다. 

첫째, 컴퓨터에 과부하가 걸리지 않도록 해야 합니다. 즉 컴퓨터에서 처리할 수 있는 것보다 더 많은 서비스를 실행하지 않도록 하는 것입니다. 

둘째, 서비스를 효율적으로 실행하는 데 중요한 균형 조정과 최적화가 있습니다. 비용 효율적이거나 성능에 민감한 서비스 오퍼링은 일부 노드가 뜨거워지도록 허용하지 않지만 다른 노드는 콜드합니다. 핫 노드는 리소스 경합과 성능 저하로 이어집니다. 콜드 노드는 낭비되는 리소스와 비용 증가를 나타냅니다. 

서비스 패브릭은 *리소스를 메트릭으로 나타냅니다.* 메트릭은 서비스 패브릭에 대해 설명하려는 논리적 또는 물리적 리소스입니다. 메트릭의 예로는 "작업 대기열 깊이" 또는 "메모리InMb"가 있습니다. Service Fabric이 노드에서 제어할 수 있는 물리적 리소스에 대한 자세한 내용은 [리소스 거버넌스](service-fabric-resource-governance.md)를 참조하십시오. 클러스터 리소스 관리자에서 사용하는 기본 메트릭 및 사용자 지정 메트릭을 구성하는 방법에 대한 자세한 내용은 [이 문서를](service-fabric-cluster-resource-manager-metrics.md)참조하십시오.

메트릭은 배치 제약 조건 및 노드 속성과 다릅니다. 노드 속성은 노드 자체의 정적 설명자입니다. 메트릭은 노드가 가지고 있는 리소스와 해당 서비스가 노드에서 실행할 때 사용하는 리소스를 설명합니다. 노드 속성은 **HasSSD일** 수 있으며 true 또는 false로 설정될 수 있습니다. 해당 SSD에서 사용 가능한 공간의 양과 서비스에서 소비되는 양은 "DriveSpaceInMb"와 같은 메트릭입니다. 

배치 제약 조건 및 노드 속성과 마찬가지로 Service Fabric 클러스터 리소스 관리자는 메트릭 이름의 의미를 이해하지 못합니다. 메트릭 이름은 단순한 문자열입니다. 단위가 모호할 수 있는 경우 만드는 메트릭 이름의 일부로 단위를 선언하는 것이 좋습니다.

## <a name="capacity"></a>용량
모든 리소스 균형 *조정을*해제한 경우 서비스 패브릭 클러스터 리소스 관리자는 여전히 노드가 용량을 초과하지 않도록 합니다. 클러스터가 가득 차지 않았거나 워크로드가 어떤 노드보다 크지 않으면 용량 초과를 관리할 수 있습니다. 용량은 클러스터 리소스 관리자가 노드에 있는 리소스의 양을 이해하는 데 사용하는 또 다른 *제약 조건입니다.* 전체 클러스터에 대해 남은 용량을 추적합니다. 

서비스 수준에서 용량과 소비량은 메트릭을 기준으로 표현됩니다. 예를 들어 메트릭은 "ClientConnections"일 수 있으며 노드의 "ClientConnections"의 용량은 32,768일 수 있습니다. 다른 노드에는 다른 제한이 있을 수 있습니다. 해당 노드에서 실행되는 서비스는 현재 "ClientConnections" 메트릭 의 32,256을 소비하고 있다고 말할 수 있습니다.

런타임 동안 클러스터 리소스 관리자는 클러스터 및 노드의 남은 용량을 추적합니다. 용량을 추적하기 위해 클러스터 리소스 관리자는 서비스가 실행되는 노드의 용량에서 각 서비스의 사용량을 뺍니다. 이 정보를 통해 클러스터 리소스 관리자는 노드가 용량을 초과하지 않도록 복제본을 배치하거나 이동할 위치를 파악할 수 있습니다.

<center>

![클러스터 노드 및 용량][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

클러스터 매니페스트에 정의된 용량을 볼 수 있습니다. 클러스터매니페스트.xml의 예는 다음과 같습니다.

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

다음은 독립 실행형 배포에 대해 ClusterConfig.json또는 Azure 호스팅 클러스터에 대한 Template.json을 통해 정의된 용량의 예입니다. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

서비스의 로드는 종종 동적으로 변경됩니다. 복제본의 "ClientConnections" 로드가 1,024에서 2,048로 변경되었습니다. 당시 실행 중이던 노드의 용량은 해당 메트릭에 대해 512개만 남아 있었습니다. 이제 해당 노드에 공간이 충분하지 않기 때문에 복제본 또는 인스턴스의 배치가 잘못되었습니다. 클러스터 리소스 관리자는 노드를 용량 아래로 되돌려 가져와야 합니다. 하나 이상의 복제본 또는 인스턴스를 해당 노드에서 다른 노드로 이동하여 용량을 초과하는 노드의 부하를 줄입니다. 

클러스터 리소스 관리자는 복제본을 이동하는 비용을 최소화하려고 합니다. [이동 비용과](service-fabric-cluster-resource-manager-movement-cost.md) 전략 및 규칙 재조정에 대해 자세히 알아볼 수 [있습니다.](service-fabric-cluster-resource-manager-metrics.md)

## <a name="cluster-capacity"></a>클러스터 용량
서비스 패브릭 클러스터 리소스 관리자는 전체 클러스터가 너무 가득 차지 않도록 하는 방법은 무엇입니까? 동적 부하를 사용하면 할 수 있는 일이 많지 않습니다. 서비스는 클러스터 리소스 관리자가 취하는 작업과 독립적으로 부하가 급증할 수 있습니다. 따라서 내일 급증할 경우 현재 충분한 헤드룸을 갖춘 클러스터의 성능이 저하될 수 있습니다. 

클러스터 리소스 관리자의 컨트롤은 문제를 방지하는 데 도움이 됩니다. 가장 먼저 할 수 있는 일은 클러스터가 가득 차게 하는 새 워크로드를 만드는 것을 방지하는 것입니다.

상태 비수기 서비스를 만들고 이 서비스와 연결된 로드가 있다고 가정해 보겠습니다. 서비스는 "DiskSpaceInMb" 메트릭을 중요시합니다. 서비스는 서비스의 모든 인스턴스에 대해 "DiskSpaceInMb"의 다섯 단위를 사용합니다. 3개의 서비스 인스턴스를 만들려고 합니다. 즉, 이러한 서비스 인스턴스를 만들려면 클러스터에 15개의 "DiskSpaceInMb" 단위가 있어야 합니다.

클러스터 리소스 관리자는 클러스터의 나머지 용량을 결정할 수 있도록 각 메트릭의 용량과 소비를 지속적으로 계산합니다. 공간이 부족하면 클러스터 리소스 관리자가 서비스 만들기 호출을 거부합니다.

요구 사항은 15개 단위만 사용할 수 있으므로 여러 가지 방법으로 이 공간을 할당할 수 있습니다. 예를 들어 15개의 다른 노드에 하나의 남은 용량 단위또는 5개의 다른 노드에 3개의 나머지 용량 단위가 있을 수 있습니다. 클러스터 리소스 관리자가 세 노드에서 사용할 수 있는 5개의 단위가 있도록 항목을 다시 정렬할 수 있는 경우 서비스를 배치합니다. 클러스터가 거의 가득차거나 어떤 이유로 기존 서비스를 통합할 수 없는 경우가 아니면 일반적으로 클러스터를 다시 정렬할 수 있습니다.

## <a name="buffered-capacity"></a>버퍼링된 용량
버퍼링된 용량은 클러스터 리소스 관리자의 또 다른 기능입니다. 전체 노드 용량의 일부를 예약할 수 있습니다. 이 용량 버퍼는 업그레이드 및 노드 실패 중에 서비스를 배치하는 데만 사용됩니다. 

버퍼링된 용량은 모든 노드에 대한 메트릭별로 전역으로 지정됩니다. 예약된 용량에 대해 선택하는 값은 클러스터에 있는 오류 및 업그레이드 도메인 수의 함수입니다. 오류 및 업그레이드 도메인이 많을수록 버퍼링된 용량에 대해 더 낮은 숫자를 선택할 수 있습니다. 더 많은 도메인이 있는 경우 업그레이드 및 오류 중 사용할 수 없는 클러스터의 수가 감소할 수 있습니다. 버퍼링된 용량을 지정하는 것은 메트릭에 대한 노드 용량을 지정한 경우에만 의미가 있습니다.

ClusterManifest.xml에서 버퍼링된 용량을 지정하는 방법의 예는 다음과 같습니다.

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

다음은 독립 실행형 배포에 대해 ClusterConfig.json을 통해 버퍼링된 용량을 지정하는 방법 또는 Azure 호스팅 클러스터에 대한 Template.json을 지정하는 방법의 예입니다.

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
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

클러스터가 메트릭에 대해 버퍼링된 용량을 소모한 경우 새 서비스를 만드는 데 실패합니다. 버퍼를 유지하기 위해 새 서비스를 만들지 않도록 방지하면 업그레이드 및 실패로 인해 노드의 용량이 초과되지 않습니다. 버퍼링된 용량은 선택 사항이지만 메트릭에 대한 용량을 정의하는 모든 클러스터에서 이 용량을 사용하는 것이 좋습니다.

클러스터 리소스 관리자는 이 로드 정보를 노출합니다. 각 메트릭에 대해 다음 정보가 포함됩니다. 
- 버퍼링된 용량 설정입니다.
- 총 용량입니다.
- 현재 소비량입니다.
- 각 메트릭이 균형 조정으로 간주되는지 여부입니다.
- 표준 편차에 대한 통계입니다.
- 로드가 가장 적고 적은 노드입니다.  
  
다음 코드는 해당 출력의 예를 보여 주며,

```PowerShell
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
* 클러스터 리소스 관리자 내의 아키텍처 및 정보 흐름에 대한 자세한 내용은 [클러스터 리소스 관리자 아키텍처 개요를](service-fabric-cluster-resource-manager-architecture.md)참조하십시오.
* 조각 모음 메트릭을 정의하는 것은 노드를 분산시키는 대신 노드의 부하를 통합하는 한 가지 방법입니다. 조각 모음을 구성하는 방법에 대해 알아보려면 [서비스 패브릭의 메트릭 및 로드 조각 모음을](service-fabric-cluster-resource-manager-defragmentation-metrics.md)참조하십시오.
* 처음부터 시작하여 [서비스 패브릭 클러스터 리소스 관리자에 대한 소개를 받습니다.](service-fabric-cluster-resource-manager-introduction.md)
* 클러스터 리소스 관리자가 클러스터에서 로드를 관리하고 균형을 조정하는 방법을 알아보려면 [서비스 패브릭 클러스터 의 균형을 참조하세요.](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
