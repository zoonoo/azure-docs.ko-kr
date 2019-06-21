---
title: Cluster Resource Manager를 사용 하 여 클러스터 설명 | Microsoft Docs
description: 장애 도메인과 업그레이드 도메인, 노드 속성, 클러스터 Resource Manager에 대 한 노드 용량을 지정 하 여 Service Fabric 클러스터를 설명 합니다.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271651"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Cluster Resource Manager를 사용 하 여 Service Fabric 클러스터 설명
클러스터를 설명 하기 위한 여러 메커니즘을 제공 하는 Azure Service Fabric의 Cluster Resource Manager 기능:

* 장애 도메인
* 업그레이드 도메인
* 노드 속성
* 노드 용량

런타임 중 클러스터 Resource Manager는 클러스터에서 실행 되는 서비스의 고가용성 보장 하기 위해이 정보를 사용 합니다. 이러한 주요 규칙을 적용 하는 동안 클러스터 내에서 리소스 소비를 최적화 하려고 합니다.

## <a name="fault-domains"></a>장애 도메인
장애 도메인은 통합된 오류는 영역입니다. 단일 컴퓨터는 장애 도메인입니다. 잘못 된 NIC 펌웨어 드라이브 오류 공급 장치 오류 전원에서 다양 한 이유로 자체 실패할 수 있기 합니다. 

동일한 이더넷 스위치에 연결 된 컴퓨터는 동일한 장애 도메인에. 따라서 전원 또는 단일 위치에서 단일 소스를 공유 하는 컴퓨터 이며 

하드웨어 오류가 중복에 대 한 자연 스러운 이기 때문에 장애 도메인은 기본적으로 계층적입니다. Service Fabric에서 Uri로 표시 합니다.

장애 도메인은 올바르게 설정 되었는지 Service Fabric이이 정보를 사용 하 여 안전 하 게 서비스를 배치 하기 때문에 중요 한 것입니다. Service Fabric (일부 구성 요소의 오류로 인해 발생) 장애 도메인이 손실 때문에 서비스가 중단 될 수 있도록 서비스를 배치 하려고 하지 않습니다. 

Azure 환경에서 Service Fabric 환경에서 제공 하는 장애 도메인 정보를 사용 하 여 사용자 대신 클러스터의 노드를 올바르게 구성 합니다. 서비스 패브릭의 독립 실행형 인스턴스의 경우 장애 도메인은 클러스터 설정 된 시간에 정의 됩니다. 

> [!WARNING]
> Service Fabric에 제공 되는 장애 도메인 정보는 정확 하 게 중요 한 것입니다. 예를 들어, Service Fabric 클러스터의 노드 5 물리적 호스트에서 실행 중인 10 개의 가상 머신에서 실행 되 고 있는지 가정해 보겠습니다. 이 경우 10개의 가상 머신이 있지만 별도의 5개(최상위 수준) 장애 도메인만 있습니다. 실제 호스트가 실패할 경우 Vm 환경을 통합 된 오류 때문에 Vm을 동일한 루트 장애 도메인을 공유 하면 동일한 실제 호스트를 공유 합니다.  
>
> Service Fabric은 변경할 필요가 노드의 장애 도메인이 필요 합니다. 같은 Vm의 고가용성을 보장 하는 다른 메커니즘 [Ha-vm](https://technet.microsoft.com/library/cc967323.aspx), Service Fabric을 사용 하 여 충돌을 일으킬 수 있습니다. 이러한 메커니즘에는 하나의 호스트에서 다른 Vm의 투명 한 마이그레이션을 사용합니다. 다시 구성 하거나 VM 내에서 실행 중인 코드를 알리는 하지 있습니다. 따라서 이기 *지원 되지 않습니다* Service Fabric을 실행 하기 위한 환경으로 클러스터 합니다. 
>
> Service Fabric은 사용되는 유일한 고가용성 기술이어야 합니다. 라이브 VM 마이그레이션 등 San 메커니즘이 필요 하지 않습니다. 이러한 메커니즘은 Service Fabric과 함께 사용 하는 경우 이러한 _줄일_ 응용 프로그램 가용성 및 안정성. 이유는 이러한 복잡성, 실패의 중앙 집중식된 원본을 추가 및 Service Fabric과 충돌 하는 안정성 및 가용성 전략을 사용 하 여입니다. 
>
>

다음 그래픽에서 색을 지정 했습니다 장애 도메인에 적용 하 고 모든 다른 장애 도메인을 나열 하는 모든 엔터티. 이 예제에는 데이터센터("DC"), 랙("R") 및 블레이드("B")가 있습니다. 각 블레이드에 하나 이상의 가상 컴퓨터를 장애 도메인 계층 구조에서 다른 계층 수 있습니다.

<center>

![장애 도메인을 통해 구성 된 노드][Image1]
</center>

런타임에 Service Fabric 클러스터 Resource Manager 클러스터의 장애 도메인을 고려 하 고 레이아웃을 계획 합니다. 상태 저장 복제본 또는 상태 비저장 인스턴스를 서비스에 대 한 별도 장애 도메인에 있도록 배포 됩니다. 장애 도메인을 사용 하 여 서비스를 분산 하면 장애 도메인 계층 구조의 모든 수준에서 실패 한 경우 서비스의 가용성 손상 되지 않습니다.

Cluster Resource Manager는 장애 도메인 계층 구조에 얼마나 많은 계층이 고려 하지 않습니다. 계층 구조의 일부가 손실에 실행 되는 서비스에 영향을 주지 않도록 하려고 합니다. 

장애 도메인 계층 구조의 깊이의 각 수준에서 노드 수가 같은 경우에 적합 합니다. 클러스터의 장애 도메인의 "트리"가 불안정 하 게 하는 경우 Resource Manager에 대 한 클러스터 최상의 서비스 재할당을 파악 하기 어렵습니다. 불균형 장애 도메인 레이아웃 일부 도메인의 손실이 다른 도메인 보다 서비스 가용성에 영향을 준다는 것을 의미 합니다. 결과적으로, Cluster Resource Manager는 두 가지 목표 사이 고민 합니다. 

* "Heavy" 도메인에 있는 컴퓨터를 사용 하 여 서비스를 배치 하 여 하려고 합니다. 
* 도메인의 손실로 인해 문제가 발생 하지 않도록 다른 도메인의 서비스를 배치 하려고 합니다. 

분산되지 않은 도메인은 어떤 모양일까요? 다음 다이어그램에서는 두 개의 다른 클러스터 레이아웃을 보여 줍니다. 첫 번째 예에서는 노드는 장애 도메인 간에 균등 하 게 분산 됩니다. 두 번째 예제에서는 하나의 장애 도메인에는 다른 장애 도메인 보다 더 많은 노드가 있습니다. 

<center>

![두 개의 다른 클러스터 레이아웃][Image2]
</center>

Azure는 오류 도메인에 노드를 선택 하기 관리 됩니다. 하지만 프로 비전 하는 노드의 수에 따라 있습니다 수 여전히 보다에 다른 사용자에 더 많은 노드가 있는 장애 도메인을 사용 하 여 됩니다. 

예를 들어 5 개의 장애 도메인이 클러스터에 있지만 노드 형식에 대 한 7 개의 노드를 프로 비전 (**NodeType**). 이 경우 첫 번째 두 장애 도메인 결국 많은 노드가입니다. 더 많이 배포 하려면 계속 **NodeType** 문제가 악화 약간의 인스턴스를 사용 하 여 인스턴스. 이러한 이유로 각 노드 형식의 노드 수는 여러 장애 도메인 수는 것이 좋습니다.

## <a name="upgrade-domains"></a>업그레이드 도메인
업그레이드 도메인은 Service Fabric 클러스터 Resource Manager가 클러스터의 레이아웃을 이해 하는 다른 기능입니다. 업그레이드 도메인에는 동시에 업그레이드 된 노드 집합을 정의 합니다. 업그레이드 도메인을 사용 하면 Cluster Resource Manager를 이해 하 고 업그레이드와 같은 관리 작업을 조정 합니다.

업그레이드 도메인은 장애 도메인과 같이 있지만 몇 가지 주요 차이점이 많이 합니다. 먼저, 조정 된 하드웨어 오류의 영역 장애 도메인을 정의합니다. 반면, 업그레이드 도메인 정책에 의해 정의 됩니다. 결정할 수 있습니다, 수를 지정 하는 환경 대신 얻게 됩니다. 노드를 갖추는 만큼 많은 업그레이드 도메인을 사용할 수 있습니다. 장애 도메인 및 업그레이드 도메인의 또 다른 차이점은 업그레이드 도메인 아니라는 계층적입니다. 대신, 간단한 태그와 같은 자세한 됩니다. 

다음 다이어그램에서는 세 개의 장애 도메인에 스트라이프된 3 개의 업그레이드 도메인을 보여 줍니다. 또한 하나의 가능한 배치 3 개의 복제본에 대 한 상태 저장 서비스의 서로 다른 장애 및 업그레이드 도메인에서 끝난 각 위치를 보여 줍니다. 이 배치 서비스 업그레이드 중 장애 도메인이 손실 있으며 코드 및 데이터의 복사본이 하나씩 남아 있을 합니다.  

<center>

![배치 된 장애 도메인 및 업그레이드 도메인][Image3]
</center>

많은 업그레이드 도메인에 장단점이 있습니다. 업그레이드 도메인이 많으면 업그레이드의 각 단계가 더 세분화 되어 더 작은 수의 노드 또는 서비스에 영향을 의미 합니다. 서비스가 줄어들어 시스템 변동이 줄어듭니다 한 번에 이동 해야 할. 업그레이드 중 도입 된 모든 문제로 인해 영향을 받는 서비스가 감소 하기 때문에이이 안정성을 향상 시키는 경향이 있습니다. 업그레이드 도메인이 많으면 업그레이드의 영향을 처리 하는 다른 노드에서 사용 가능한 크기 이하의 버퍼 해야 한다는 의미 하기도 합니다. 

예를 들어 5 개의 업그레이드 도메인에 있는 경우 각각의 노드 처리 약 20%의 트래픽이 있습니다. 업그레이드에 대 한 해당 업그레이드 도메인이 중단 해야 하는 경우 해당 부하는 일반적으로 다른 곳으로 이동 해야 합니다. 남은 업그레이드 도메인이 4 했으므로 각 총 트래픽의 약 5%에 대 한 공간이 있어야 합니다. 업그레이드 도메인이 많으면 클러스터의 노드에서 작은 버퍼 해야 한다는 것을 의미 합니다. 

10 개의 업그레이드 도메인을 대신 갖추는 경우를 고려 합니다. 이 경우 총 트래픽의 약 10% 각 업그레이드 도메인 처리 됩니다. 때 클러스터를 통해 업그레이드 단계는 총 트래픽의 약 1.1% 공간만 있으면 각 도메인 해야 합니다. 업그레이드 도메인이 많으면 일반적으로 사용 하면 더 높은 사용률로 노드를 실행 하려면 작은 예약 된 용량을 필요 하기 때문에 있습니다. 장애 도메인에도 마찬가지입니다.  

여러 업그레이드 도메인의 단점은 업그레이드 시간이 오래 걸린다는 것입니다. Service Fabric 업그레이드 도메인 완료 되 고 다음 업그레이드를 시작 하기 전에 검사 짧은 기간 동안을 대기 합니다. 이러한 지연은 업그레이드가 진행되기 전에 업그레이드로 인한 문제를 감지할 수 있게 합니다. 이러한 단점은 한 번에 너무 많은 서비스에 영향을 주는 잘못된 변경 사항을 방지하기 때문에 받아들일 만합니다.

너무 적은 업그레이드 도메인의 현재 상태에는 많은 부작용에 있습니다. 각 업그레이드 도메인은 아래쪽 이며 업그레이드 중인 전체 용량의 많은 부분을 사용할 수 없습니다. 예를 들어, 세 개의 업그레이드 도메인에 있는 경우을 수행 하면 아래쪽의 전체 서비스 또는 클러스터 용량은 약 1 / 3 번입니다. 한 번에 가동 서비스의 상당 부분이 적절 하지 않은 워크 로드를 처리 하려면 클러스터의 나머지 부분에 용량이 충분 해야 하기 때문에 있습니다. 그렇지 않으면 것 보다 적게 로드 해당 노드는 해당 버퍼 정상적인 작업 중 즉 유지 관리 합니다. 이로 인해 서비스 실행 비용이 증가합니다.

환경에서 장애 또는 업그레이드 도메인의 총 수는 실제 제한이나 중첩 방법에 대한 제약이 없습니다. 이 밖에도 일반적인 패턴:

- 장애 도메인 및 업그레이드 도메인 1:1 매핑된
- 노드 (물리적 또는 가상 OS 인스턴스) 당 하나의 업그레이드 도메인
- 여기서 장애 도메인 및 업그레이드 도메인 행렬을 형성 대각선 일반적으로 실행 되는 컴퓨터를 사용 하 여 "스트라이프" 또는 "매트릭스" 모델

<center>

![장애 도메인 및 업그레이드 도메인 레이아웃][Image4]
</center>

선택 하는 레이아웃에 대 한 최상의 대답이 없습니다. 각각에 장점 및 단점이 있습니다. 예를 들어 1FD:1UD 모델의 설치는 간단합니다. 노드 모델당 하나의 업그레이드 도메인의 모델은 대부분의 사람들이 같은 하는 데는입니다. 업그레이드 하는 동안 각 노드는 독립적으로 업데이트 합니다. 이는 이전에 수동으로 작은 컴퓨터 집합을 업그레이드한 방법과 비슷합니다.

가장 일반적인 모델은 FD/UD 행렬, 여기서 장애 도메인 및 업그레이드 도메인에 테이블을 형성 하 고 노드는 대각선을 따라 배치 됩니다. 이는 Azure의 Service Fabric 클러스터에서 기본적으로 사용되는 모델입니다. 많은 노드를 사용 하 여 클러스터에 대 한 모든 항목이 조밀한 행렬 패턴 처럼 종료 합니다.

> [!NOTE]
> Azure에서 호스팅되는 Service Fabric 클러스터는 기본 전략을 변경 지원 하지 않습니다. 독립 실행형 클러스터에만 해당 사용자 지정을 제공합니다.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>장애 및 업그레이드 도메인 제약 조건 및 결과 동작
### <a name="default-approach"></a>기본 접근 방식
기본적으로 Cluster Resource Manager는 서비스 장애 및 업그레이드 도메인 간의 균형을 유지 합니다. 이것이 [제약 조건](service-fabric-cluster-resource-manager-management-integration.md)으로 모델링됩니다. 장애 도메인 및 업그레이드 도메인 상태에 대 한 제약 조건: "특정된 서비스 파티션에서, 있습니다 않아야 차이 계층 구조 수준이 동일한 두 도메인 간의 서비스 개체 (상태 비저장 서비스 인스턴스 또는 상태 저장 서비스 복제본) 수는 1 보다 큽니다."

이 제약 조건이 "최대 차이" 보증을 제공 하는 경우를 가정해 봅니다. 장애 도메인 및 업그레이드 도메인에 대 한 제약 조건에는 특정 이동 또는 규칙을 위반 하는 배열을 방지 합니다.

예를 들어 5 개의 장애 도메인과 5 개의 업그레이드 도메인을 사용 하 여 구성 하는 6 개의 노드를 사용 하 여 클러스터 있다는 것을 가정해 보겠습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

지금 사용 하 여 서비스를 만드는 것입니다를 가정해 보겠습니다를 **TargetReplicaSetSize** (또는 상태 비저장 서비스의 경우 **InstanceCount**) 5의 값입니다. 복제본은 N1-N5에 생성됩니다. 실제로 많은 서비스를 만들더라도 N6은 절대 사용되지 않습니다. 그렇지만 그 이유는 무엇일까요? N6을 선택하는 경우 현재 레이아웃 및 발생하는 상황 간의 차이를 살펴보겠습니다.

다음은 레이아웃 및 총 장애 및 업그레이드 도메인당 복제본 수입니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃이 장애 도메인 및 업그레이드 도메인당 노드의 측면에서 균형을 이룹니다. 에서도 균형을 이룹니다 장애 및 업그레이드 도메인당 복제본 수를 기준으로 합니다. 각 도메인에는 동일한 수의 노드 및 동일한 수의 복제본이 있습니다.

이제 N2 대신 N6를 사용하는 경우 발생하는 결과를 살펴보겠습니다. 복제본은 어떻게 배포되나요?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 제약 조건에 대 한 "최대 차이" 보증의 정의 위반 합니다. FD0 반면 FD1에는 0에는 두 개의 복제본에 있습니다. FD0 및 fd1 이라는 간의 차이 최대 차이인 1 보다 큰 2 총입니다. 제약 조건을 위반 하기 때문에 Cluster Resource Manager에서는이 정렬을 수 없습니다. 마찬가지로 N2 및 N6 (N1 N2 대신)를 선택한 경우 다음에서 얻을 수

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃이 장애 도메인 측면에서 균형을 이룹니다. 하지만 이제는 위반 업그레이드 도메인 제약 조건의 UD0에는 복제본이 없고 UD1에는 두 개의 때문입니다. 이 레이아웃도 잘못 되었으며 클러스터 리소스 관리자에서 선택할 수 없습니다.

이 상태 저장 복제본 또는 상태 비저장 인스턴스 배포 방식은 가능한 최고의 내결함성을 제공합니다. 하나의 도메인 다운 되 면 복제본/인스턴스의 최소 수는 손실 됩니다. 

반면 이 방법은 지나치게 엄격하여 클러스터가 모든 리소스를 활용하지 못할 수 있습니다. 특정 클러스터 구성에서는 특정 노드를 사용할 수 없습니다. 이 경고 메시지에서 발생 하 여 서비스를 배치 하지 서비스 패브릭이 발생할 수 있습니다. 이전 예제에서는 일부 클러스터 노드 수 없습니다 (예에서 N6)를 사용 합니다. (N7-N10) 클러스터에 노드를 추가 하는 경우에 장애 및 업그레이드 도메인 제약 조건으로 인해 복제본/인스턴스가 N1 – N5에만 배치는. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>또 다른 방법

클러스터 Resource Manager는 장애 및 업그레이드 도메인 제약 조건의 다른 버전을 지원합니다. 최소 수준의 안전 보장 하면서도 배치 수 있습니다. 다른 제약 조건은 다음과 같이 정의할 수 있습니다. "지정된 서비스 파티션의 경우 도메인 간 복제본 배포 확인 해야 파티션을 쿼럼 손실 문제가 발생 하지 않습니다." 이 제약 조건이 "쿼럼 안전" 보증을 제공 하는 경우를 가정해 봅니다. 

> [!NOTE]
> 상태 저장 서비스의 경우 대부분의 파티션 복제본이 동시에 중지되는 상황에서 *쿼럼 손실*을 정의합니다. 예를 들어 있으면 **TargetReplicaSetSize** 5 이면 모든 3 개 복제본 집합은 쿼럼을 나타냅니다. 마찬가지로, 하는 경우 **TargetReplicaSetSize** 은 6 4 개의 복제본 쿼럼에 필요 합니다. 두 경우 모두 두 개의 복제본 파티션을 계속 정상적으로 작동 하려는 경우 동시에 다운 수 있습니다. 
>
> 상태 비저장 서비스의 경우는 없습니다 *쿼럼 손실*합니다. 상태 비저장 서비스는 대부분의 인스턴스가 동시에 중단 될 경우에 정상적으로 작동을 계속 합니다. 따라서이 문서의 나머지 부분에서 상태 저장 서비스 집중적으로 다루겠습니다.
>

이전 예제로 돌아갑시다. 제약 조건의 "쿼럼 안전" 버전에서는 모든 세 가지 레이아웃 유효한 것입니다. 두 번째 레이아웃의 FD0 실패 또는 세 번째 레이아웃의 UD1 실패 한 경우에 파티션이 여전히 쿼럼을 해야 합니다. (복제본의 대부분은 계속 합니다.) 제약 조건의이 버전에서는 N6 거의 항상 활용할 수 있습니다.

"쿼럼 안전" 접근 방식 "최대 차이" 접근 방식 보다 더 많은 유연성을 제공합니다. 원인은 쉽게는 거의 모든 클러스터 토폴로지에서 유효한 복제본 배포를 찾을 수 있을 것입니다. 그러나 이 접근 방식은 일부 오류가 다른 오류보다 심각하기 때문에 최고의 내결함성을 보장할 수 없습니다. 

최악의 경우에서 복제본의 대부분이 하나의 도메인과 하나의 추가 복제본 오류가 발생 하 여 손실 될 수 있습니다. 예를 들어 5 개의 복제본 또는 인스턴스를 사용 하 여 쿼럼이 손실 하는 3 개의 오류, 대신 이제 손실 될 수 있는 2 건 만으로도 대부분이 합니다. 

### <a name="adaptive-approach"></a>적응형 접근 방식
두 방법 모두 장점과 단점이 있으므로 이러한 두 가지 전략이 결합 하는 적응형 접근 방식을 도입 했습니다.

> [!NOTE]
> 이 Service Fabric 버전 6.2부터 기본 동작입니다. 
> 
> 적응형 접근 방식은 기본적으로 "최대 차이" 논리를 사용하다가 필요할 때만 "쿼럼 안전" 논리로 전환합니다. 클러스터 Resource Manager는 자동으로 클러스터 및 서비스를 구성 하는 방법을 확인 하 여 어떤 전략은 필요한 파악 합니다.
> 
> 클러스터 Resource Manager의 서비스를 모두 이러한 조건에 해당할에 대 한 "쿼럼 기반" 논리를 사용 해야 합니다.
>
> * **TargetReplicaSetSize** 서비스는 장애 도메인 수 및 업그레이드 도메인 수로 균등 하 게 나눌 수 있습니다.
> * 노드 수가 업그레이드 도메인 수를 곱한 장애 도메인 보다 작거나 수와 같습니다.
>
> 쿼럼 손실 상태 비저장 서비스에 적합 하지 않지만 클러스터 Resource Manager를 사용 하 여이 방법은 상태 비저장 및 상태 저장 서비스에 대 한 주의 해야 합니다.

앞의 예제를 다시 살펴보겠습니다 및 클러스터에서 이제 8 개의 노드를에 있다고 가정 합니다. 클러스터는 여전히 5 개의 장애 도메인과 5 개의 업그레이드 도메인을 사용 하 여 구성 하며 **TargetReplicaSetSize** 해당 클러스터에서 호스팅되는 서비스 값은 5입니다. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

모든 필요한 조건을 충족 하므로 Cluster Resource Manager 서비스 배포에 "쿼럼 기반" 논리를 사용 합니다. 따라서 N6 N8 사용. 이 경우 하나의 가능한 서비스 배포가와 같습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

하는 경우 서비스의 **TargetReplicaSetSize** 값은 예를 들어 줄어듭니다 4, 클러스터 Resource Manager 해당 변경 내용을 확인할 수 있습니다. 때문에 "최대 차이" 논리를 사용 하 여 다시 시작 됩니다 **TargetReplicaSetSize** 장애 도메인과 업그레이드 도메인 수 면 더 이상 없습니다. 결과적으로, 특정 복제본 이동이 발생 하는 데 N1-N5 노드에 나머지 4 개의 복제본을 배포 합니다. 이런 방식으로 오류 도메인 및 업그레이드 도메인 논리 "최대 차이" 버전 위반 하지 않도록 합니다. 

이전 레이아웃으로 하는 경우는 **TargetReplicaSetSize** 값이 5 및 클러스터에서 N1이 제거, 업그레이드 도메인 수가 4 동일 합니다. 업그레이드 도메인 수에는 서비스의 균등 하 게 나눌 하지 때문에 "최대 차이" 논리를 사용 하 여 클러스터 Resource Manager을 시작 하는 다시 **TargetReplicaSetSize** 더 이상 값입니다. 결과적으로 복제본 R1은 다시 빌드되면 장애 도메인 및 업그레이드 도메인에 대 한 제약 조건을 위반 하지 않도록 있도록 N4에 도착 해야 합니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |해당 사항 없음 |해당 사항 없음 |해당 사항 없음 |해당 사항 없음 |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>장애 및 업그레이드 도메인 구성
Azure 호스팅 Service Fabric 배포 장애 도메인과 업그레이드 도메인이 자동으로 정의 됩니다. Service Fabric은 Azure의 환경 정보를 선택하고 사용합니다.

사용자 고유의 클러스터를 만들려는 (않거나 개발의 특정 토폴로지를 실행 하려면), 장애 도메인을 제공할 수 있으며 업그레이드 도메인 정보를 직접 수 있습니다. 이 예제에서는 각 세 개의 랙을 가진 세 개의 데이터 센터에 걸쳐 있는 9 노드 로컬 개발 클러스터를 정의 합니다. 이 클러스터에는 해당 데이터 센터에 스트라이프된 3 개의 업그레이드 도메인에 있습니다. ClusterManifest.xml 구성의 예는 다음과 같습니다.

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

이 예제에서는 독립 실행형 배포에 대 한 ClusterConfig.json을 사용합니다.

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
> Azure Resource Manager를 통해 클러스터를 정의할 때 Azure에서는 장애 도메인 및 업그레이드 도메인. 노드 형식 및 가상 머신 확장의 정의에서 설정 되므로 Azure Resource Manager 템플릿에 장애 도메인 또는 업그레이드 도메인에 대 한 정보를 포함 하지 않습니다.
>

## <a name="node-properties-and-placement-constraints"></a>노드 속성 및 배치 제약 조건
경우에 따라 (사실 대부분의 경우) 특정 워크 로드의 특정 클러스터의 노드 형식 에서만 실행 되는지 확인 해야 합니다. 예를 들어, 일부 워크 로드는 Gpu 또는 Ssd가 필요할 수 있습니다 및 다른 되지 않을 수 있습니다. 

특정 워크 로드에 대 한 하드웨어를 대상으로의 좋은 예는 거의 모든 n 계층 아키텍처입니다. 특정 컴퓨터 응용 프로그램의 프런트 엔드 또는 API-서비스 역할도 하며 클라이언트 또는 인터넷에 노출 됩니다. 종종 서로 다른 하드웨어 리소스를 사용하는 다양한 머신에서 계산 또는 스토리지 계층의 작업을 처리합니다. 이러한 컴퓨터는 대개 클라이언트 또는 인터넷에 직접 노출되지 _않습니다_. 

Service Fabric에서는 경우에 따라 특정 워크 로드 해야 할 수 있는 특정 하드웨어 구성에서 실행 해야 합니다. 예를 들면 다음과 같습니다.

* 기존 n 계층 응용 프로그램을 Service Fabric 환경으로 "리프트 및 이동" 되었습니다.
* 워크 로드 성능, 확장성 또는 보안 보안상의 이유 등 특정 하드웨어에서 실행 되어야 합니다.
* 워크 로드는 정책 또는 리소스 소비 이유로 다른 워크 로드 로부터 격리 되어야 합니다.

이러한 유형의 구성을 지원 하기 위해 Service Fabric 노드에 적용할 수 있는 태그를 포함 합니다. 이러한 태그를 *노드 속성*이라고 합니다. *배치 제약 조건* 문은 하나 이상의 노드 속성에 대해 선택한 개별 서비스에 연결 됩니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다. 제약 조건 집합은 확장 가능 합니다. 모든 키/값 쌍을 사용할 수 있습니다. 

<center>

![클러스터 레이아웃의 다양 한 워크 로드][Image5]
</center>

### <a name="built-in-node-properties"></a>기본 제공 노드 속성
Service Fabric 정의할 필요가 자동으로 사용할 수 있는 몇 가지 기본 노드 속성을 정의 합니다. 각 노드에서 정의 된 기본 속성은 **NodeType** 하 고 **NodeName**합니다. 

예를 들어 배치 제약 조건으로 작성할 수 있습니다 `"(NodeType == NodeType03)"`합니다. **NodeType** 자주 사용 되는 속성입니다. 컴퓨터의 종류와 1:1 해당 하므로 유용 합니다. 각 머신의 종류는 기존의 n 계층 애플리케이션에서 워크로드 유형에 해당합니다.

<center>

![배치 제약 조건 및 노드 속성][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>배치 제약 조건 및 노드 속성 구문 
노드 속성에 지정 된 값에는 문자열일 수 있습니다, 부울, 또는 기호가 있는 long입니다. 서비스에서 문을 배치 라고 *제약 조건* 클러스터의 서비스를 실행할 수 있는 제한 하기 때문에 합니다. 제약 조건에는 클러스터의 노드 속성에 대해 작동 하는 부울 문일 수 있습니다. 이러한 부울 문의 유효한 선택기는:

* 특정 문을 만들기 위한 조건부 검사 합니다.

  | 문 | 구문 |
  | --- |:---:|
  | "같음" | "==" |
  | "다음과 같지 않음" | "!=" |
  | "다음보다 큼" | ">" |
  | "다음보다 크거나 같음" | ">=" |
  | "다음보다 작음" | "<" |
  | "다음보다 작거나 같음" | "<=" |

* 그룹화 및 논리 연산에 대 한 부울 문:

  | 문 | 구문 |
  | --- |:---:|
  | "및" | "&&" |
  | "또는" | "&#124;&#124;" |
  | "아님" | "!" |
  | "단일 문인 그룹" | "()" |

기본 제약 조건문의 몇 가지 예는 다음과 같습니다.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

전체 배치 제약 조건 문이 "True"로 평가되는 노드에만 서비스가 배치될 수 있습니다. 정의 된 속성이 없는 노드 속성을 포함 하는 배치 제약 조건과 일치 하지 않습니다.

다음과 같은 노드 속성이 ClusterManifest.xml에서 노드 형식에 대 한 정의 가정해 보겠습니다.

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

다음 예제에서는 Azure에서 호스트 되는 클러스터에 대 한 독립 실행형 배포 또는 Template.json 경우 ClusterConfig.json을 통해 정의 된 노드 속성을 보여 줍니다. 

> [!NOTE]
> Azure Resource Manager 템플릿에서 노드 형식 대개 매개 변수화 됩니다. 다음과 같이 `"[parameters('vmNodeType1Name')]"` NodeType01 대신 합니다.
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

서비스 배치를 만들 수 있습니다 *제약 조건* 다음과 같은 서비스에 대 한 합니다.

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

NodeType01의 모든 노드가 유효 제약 조건 사용 하 여 해당 노드 유형을 선택할 수도 있습니다 `"(NodeType == NodeType01)"`합니다.

서비스의 배치 제약 조건은 런타임에 동적으로 업데이트할 수 있습니다. 해야 할 경우 클러스터에서 서비스를 이동, 추가 및 제거 요구 사항 하 고 수 등. Service Fabric이는 서비스를 유지 하 고 사용할 수 있는 이러한 종류의 변경 내용이 있는 경우에 확인 합니다.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

모든 명명 된 서비스 인스턴스에 대 한 배치 제약 조건이 지정 됩니다. 업데이트는 이전에 지정된 항목(덮어쓰기)에서 발생합니다.

클러스터 정의는 노드의 속성을 정의합니다. 노드의 속성을 변경 하려면 클러스터 구성을 업그레이드 합니다. 노드 속성을 업그레이드하려면 영향을 받는 각 노드를 다시 시작하여 새 속성을 보고해야 합니다. 서비스 패브릭은 이러한 롤링 업그레이드를 관리 합니다.

## <a name="describing-and-managing-cluster-resources"></a>설명 하 고 클러스터 리소스 관리
모든 조정자의 가장 중요한 작업 중 하나는 클러스터에서 리소스 소비를 관리할 수 있도록 돕는 일입니다. 클러스터 리소스 관리는 여러 가지 다른 작업을 의미할 수 있습니다. 

첫째, 컴퓨터에 과부하가 걸리지 않도록 해야 합니다. 즉 컴퓨터에서 처리할 수 있는 것보다 더 많은 서비스를 실행하지 않도록 하는 것입니다. 

둘째, 경우 균형 조정 및 최적화를 효율적으로 실행 중인 서비스에 중요 한 비용 효율적인 또는 성능에 민감한 서비스 제품은 일부 노드가 핫 이지만 나머지는 콜드를 허용할 수 없습니다. 핫 노드는 리소스 경합 및 성능 저하 시킬을 둡니다. 콜드 노드는 리소스 낭비와 증가 한 비용을 나타냅니다. 

Service Fabric로 리소스를 나타냅니다 *메트릭을*합니다. 메트릭은 서비스 패브릭에 대해 설명하려는 논리적 또는 물리적 리소스입니다. 메트릭의 예로 "WorkQueueDepth" 또는 "MemoryInMb"로 지정 합니다. 노드에서 서비스 패브릭에서 관리할 수 있는 물리적 리소스에 대 한 정보를 참조 하세요 [리소스 거 버 넌 스](service-fabric-resource-governance.md)합니다. 사용자 지정 메트릭 및 해당 용도 구성에 대 한 정보를 참조 하세요 [이 문서에서는](service-fabric-cluster-resource-manager-metrics.md)합니다.

메트릭은 배치 제약 조건 및 노드 속성이 다릅니다. 노드 속성은 노드 자체의 정적 설명자입니다. 메트릭은은 노드에 있는 리소스 및 서비스 노드에서 실행할 때 사용 하는 설명 합니다. 노드 속성 수 있습니다 **HasSSD** true 또는 false로 설정할 수 있습니다. 어떻게 사용 되는 서비스에서 해당 SSD에서 사용할 수 있는 공간의 양을 "DriveSpaceInMb."와 같은 메트릭 수 있습니다. 

마찬가지로 배치 제약 조건 및 노드 속성에 대 한 Service Fabric 클러스터 Resource Manager 이해 하지 못하는 메트릭 평균의 이름입니다. 메트릭 이름은 단순한 문자열입니다. 모호한이 아닐 때 만든 메트릭 이름이의 일부로 단위를 선언 하는 것이 좋습니다.

## <a name="capacity"></a>용량
모든 리소스를 해제 하는 경우 *분산*, Service Fabric 클러스터 Resource Manager 노드가 해당 용량 초과 계속 확인 합니다. 클러스터가 가득 차지 않았거나 워크로드가 어떤 노드보다 크지 않으면 용량 초과를 관리할 수 있습니다. 다른 용량은 *제약 조건* Cluster Resource Manager가 노드에 있는 리소스의 양을 이해 하기는 합니다. 전체 클러스터에 대해 남은 용량을 추적합니다. 

서비스 수준에서 용량과 소비량은 메트릭을 기준으로 표현됩니다. 예를 들어 메트릭이 "ClientConnections" 수 및 노드 "ClientConnections" 용량이 32768에 대 한 용량을 가질 수 있습니다. 다른 노드는 다른 제한이 있을 수 있습니다. 해당 노드에서 실행 되는 서비스 32,256 메트릭이 "ClientConnections."를 사용 하는 현재 일까요?

런타임 중 클러스터 Resource Manager는 클러스터 및 노드에서 남아 있는 용량을 추적합니다. 용량을 추적 하기 위해 Cluster Resource Manager 서비스가 실행 되는 노드의 용량에서 각 서비스의 사용량을 뺍니다. 이 정보를 사용 하 여 클러스터 Resource Manager 배치 하거나 노드가 용량을 초과 하지 않도록 복제본을 이동할 위치를 알 수 있습니다.

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

클러스터 매니페스트에 정의 된 용량을 볼 수 있습니다. ClusterManifest.xml 예는 다음과 같습니다.

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Azure에서 호스트 되는 클러스터에 대 한 독립 실행형 배포 또는 Template.json 경우 ClusterConfig.json을 통해 정의 된 용량의 예는 다음과 같습니다. 

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

서비스의 변경 내용을 자주를 동적으로 로드 합니다. 복제본의 부하가 "clientconnections"에서 1024로 변경 2,048 가정해 보겠습니다. 다음에서 실행 되는 노드는 해당 메트릭에 대 한 남은 유일한 512의 용량을 했습니다. 이제 해당 복제본 또는 인스턴스의 배치가 올바르지 해당 노드에 충분 한 공간이 없기 때문입니다. 클러스터 Resource Manager는 용량 아래로 노드를 가져올 해야 합니다. 해당 노드에서 하나 이상의 복제본 또는 인스턴스를 다른 노드로 이동 하 여 용량을 초과 되는 노드에서 로드를 줄입니다. 

클러스터 리소스 관리자는 복제본 이동 비용을 최소화 하려고 합니다. 에 대 한 자세한 내용은 [이동 비용](service-fabric-cluster-resource-manager-movement-cost.md) 및 약 [전략 및 규칙을 리 밸 러 싱](service-fabric-cluster-resource-manager-metrics.md)합니다.

## <a name="cluster-capacity"></a>클러스터 용량
Service Fabric 클러스터 Resource Manager 가득 없도록 전체 클러스터를 유지 하는 방법 동적 부하를 사용 하 여 수행할 수 많은 하지 않습니다. 서비스는 클러스터 Resource Manager를 사용 하는 작업과 독립적으로 해당 부하가 급증을 가질 수 있습니다. 결과적으로, 오늘은 여유가 많은 클러스터가 내일은 있으면 급증 내일 수 있습니다. 

클러스터 Resource Manager에서 컨트롤 문제 방지할 수 있습니다. 가장 먼저 할 수 있는 전체 클러스터가 시키는 새 워크 로드의 생성을 방지 됩니다.

상태 비저장 서비스를 만들고 관련 된 일부 부하가 있습니다 가정해 보겠습니다. 서비스가 "DiskSpaceInMb" 메트릭을 대 한 내용입니다. 5 단위의 "DiskSpaceInMb" 서비스의 모든 인스턴스에 대해 서비스를 사용 합니다. 3개의 서비스 인스턴스를 만들려고 합니다. 즉, 15 단위의 "DiskSpaceInMb"도 이러한 서비스 인스턴스를 만들 수는 클러스터에 표시 되도록 해야 합니다.

클러스터 Resource Manager 지속적으로 계산 용량 및 사용량 메트릭의 각 클러스터에 남아 있는 용량을 결정할 수 있습니다. 충분 한 공간이 없으면 Cluster Resource Manager는 서비스 만들기에 대 한 호출을 거부 합니다.

이기 때문에 요구 사항을 15 단위를 사용할 수 있도록 다양 한 방법으로이 공간을 할당할 수 있습니다. 예를 들어 남은 1 단위의 15 개의 노드에 용량 또는 남은 3 단위의 다른 5 개의 노드에서 용량 있을 수 있습니다. 세 개의 노드에서 5 단위는 클러스터 Resource Manager에서 작업을 다시 정렬할 수, 하는 경우 서비스를 배치 합니다. 클러스터가 거의 가득차거나 어떤 이유로 기존 서비스를 통합할 수 없는 경우가 아니면 일반적으로 클러스터를 다시 정렬할 수 있습니다.

## <a name="buffered-capacity"></a>버퍼링 된 용량
버퍼링 된 용량은 클러스터 리소스 관리자의 또 다른 기능입니다. 전체 노드 용량의 일부를 예약할 수 있습니다. 이 용량 버퍼는 업그레이드 및 노드가 실패 하는 동안 서비스를 배치 하는 데에 사용 됩니다. 

버퍼링 된 용량은 모든 노드에 대해 메트릭을 기준 전역적으로 지정 됩니다. 예약 된 용량에 대해 선택 하는 값이 클러스터에 있는 장애 도메인 및 업그레이드 도메인 수의 함수입니다. 장애 도메인 및 업그레이드 도메인이 많으면 버퍼링 된 용량에 대 한 낮은 값을 선택할 수 있다는 것을 의미 합니다. 더 많은 도메인이 있는 경우 업그레이드 및 오류 중 사용할 수 없는 클러스터의 수가 감소할 수 있습니다. 메트릭에 대해 노드 용량도 지정한 경우에 버퍼링 된 용량을 지정 하는 것이 좋습니다.

ClusterManifest.xml에서 버퍼링 된 용량을 지정 하는 방법의 예는 다음과 같습니다.

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Azure 호스트 클러스터에 대 한 독립 실행형 배포 또는 Template.json의 경우 ClusterConfig.json 통해 버퍼링 된 용량을 지정 하는 방법의 예는 다음과 같습니다.

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

클러스터가 메트릭에 대해 버퍼링된 용량을 소모한 경우 새 서비스를 만드는 데 실패합니다. 버퍼를 유지하기 위해 새 서비스를 만들지 않도록 방지하면 업그레이드 및 실패로 인해 노드의 용량이 초과되지 않습니다. 버퍼링 된 용량 선택적 이지만 메트릭에 대 한 용량을 정의 하는 모든 클러스터의 것이 좋습니다.

클러스터 Resource Manager는이 로드 정보를 제공합니다. 각 메트릭에 대해 다음 정보가 포함됩니다. 
- 버퍼링 된 용량 설정입니다.
- 총 용량입니다.
- 현재 소비 합니다.
- 각 메트릭에 간주 되는지 여부 분산 합니다.
- 표준 편차에 대 한 통계입니다.
- 노드는 가장 및 최소 로드가 있습니다.  
  
다음 코드는 해당 출력의 예제를 보여줍니다.

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
* 클러스터 Resource Manager 내의 아키텍처 및 정보 흐름에 대 한 내용은 참조 하세요 [Cluster Resource Manager 아키텍처 개요](service-fabric-cluster-resource-manager-architecture.md)합니다.
* 조각 모음 메트릭 정의 분배 하는 대신 노드에서 부하를 통합 하는 하나의 방법입니다. 조각 모음을 구성 하는 방법에 알아보려면 참조 [Service Fabric에서 부하 및 메트릭의 조각 모음](service-fabric-cluster-resource-manager-defragmentation-metrics.md)합니다.
* 부터 시작 하 고 [Service Fabric 클러스터 Resource Manager를 소개](service-fabric-cluster-resource-manager-introduction.md)합니다.
* 클러스터 리소스 관리자 관리 하 고 클러스터의 부하를 분산 하는 방법에 대해 알아보려면 [Service Fabric 클러스터 분산](service-fabric-cluster-resource-manager-balancing.md)합니다.

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
