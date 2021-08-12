---
title: 클러스터 리소스 관리자를 사용한 클러스터 설명
description: 클러스터 리소스 관리자에 대한 장애 도메인, 업그레이드 도메인, 노드 속성, 노드 용량을 지정하여 Service Fabric 클러스터를 설명합니다.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: bb012c1f0cb60e590cb38fc9e362e400439b24b3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479807"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>클러스터 리소스 관리자를 사용한 Service Fabric 클러스터 설명

Azure Service Fabric의 클러스터 리소스 관리자 기능은 클러스터를 설명하는 몇 가지 메커니즘을 제공합니다.

* 장애 도메인
* 업그레이드 도메인
* 노드 속성
* 노드 용량

런타임 중에 클러스터 리소스 관리자는 이 정보를 사용하여 클러스터에서 실행되는 서비스의 고가용성을 보장합니다. 이러한 주요 규칙을 적용하는 동시에 클러스터의 리소스 사용 최적화를 시도합니다.

## <a name="fault-domains"></a>장애 도메인

장애 도메인은 통합된 오류는 영역입니다. 단일 컴퓨터는 장애 도메인입니다. 전원 공급 장치 오류, 드라이브 오류, 잘못된 NIC 펌웨어 등 다양한 이유로 자체적으로 실패할 수 있습니다.

동일한 이더넷 스위치에 연결된 컴퓨터는 동일한 장애 도메인에 있습니다. 즉, 단일 전원 또는 단일 위치를 공유하는 컴퓨터입니다.

하드웨어 오류가 중복되기 때문에 장애 도메인은 기본적으로 계층적입니다. Service Fabric에서 URI로 표시됩니다.

Service Fabric이 이러한 정보를 사용하여 서비스를 안전하게 배치하기 때문에 장애 도메인을 올바르게 설정하는 것이 중요합니다. Service Fabric은 일부 구성 요소의 오류로 인해 발생하는 장애 도메인의 손실 때문에 서비스가 중단될 수 있는 서비스를 배치하지 않으려고 합니다.

Service Fabric은 Azure 환경에서 사용자 대신 클러스터의 노드를 올바르게 구성하기 위해 작업 환경에 의해 제공되는 장애 도메인 정보를 사용합니다. Service Fabric 독립 실행형 인스턴스의 경우, 클러스터를 설정할 때 장애 도메인이 정의됩니다.

> [!WARNING]
> Service Fabric에 제공되는 장애 도메인 정보는 정확해야 합니다. 예를 들어, Service Fabric 클러스터의 노드가 5개의 물리적 호스트에서 실행되는 10개의 가상 머신 내에서 실행되고 있다고 가정해 보겠습니다. 이 경우 10개의 가상 머신이 있지만 별도의 5개(최상위 수준) 장애 도메인만 있습니다. 동일한 물리적 호스트를 공유하면 물리적 호스트에 장애가 발생하는 경우 VM에서 조정된 오류가 발생하므로 동일한 루트 장애 도메인을 공유하게 됩니다.  
>
> Service Fabric은 노드의 장애 도메인이 변경되지 않을 것으로 예상합니다. [HA-VM](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10))과 같이 VM의 고가용성을 보장하는 다른 메커니즘은 Service Fabric과 충돌할 수 있습니다. 이러한 메커니즘은 한 호스트에서 다른 호스트로 VM의 투명한 마이그레이션을 사용합니다. VM 내에서 실행 중인 코드를 재구성하거나 알리지 않습니다. 따라서 Service Fabric 클러스터를 실행하는 환경으로 *지원되지 않습니다*. 
>
> Service Fabric은 사용되는 유일한 고가용성 기술이어야 합니다. 라이브 VM 마이그레이션, SAN 등의 메커니즘은 필요하지 않습니다. 이러한 메커니즘을 Service Fabric과 함께 사용하면 애플리케이션의 가용성과 안정성이 _줄어듭니다_. 추가 복잡성이 발생하고 , 집중된 오류의 원본을 추가하고, Service Fabric의 기능과 충돌하는 안정성 및 가용성 전략을 사용하기 때문입니다.
>
>

다음 그림에서는 장애 도메인에 영향을 주는 모든 엔터티를 색으로 표시하고 그 결과에 해당하는 여러 장애 도메인을 나열합니다. 이 예제에는 데이터센터("DC"), 랙("R") 및 블레이드("B")가 있습니다. 각 블레이드에 두 개 이상의 가상 머신이 있는 경우, 장애 도메인 계층 구조에 또 다른 계층이 있다고 볼 수 있습니다.

![장애 도메인을 통해 구성된 노드][Image1]

런타임 중에, Service Fabric 클러스터 리소스 관리자는 클러스터의 장애 도메인을 고려하여 레이아웃을 계획합니다. 서비스에 대한 상태 저장 복제본 또는 상태 비저장 인스턴스가 별도의 장애 도메인에 있도록 분산됩니다. 장애 도메인을 통해 서비스를 분산하면 장애 도메인이 모든 계층 수준에서 실패할 때 서비스의 가용성이 손상되지 않습니다.

클러스터 리소스 관리자는 장애 도메인 계층 구조에 있는 레이어 수를 고려하지 않습니다. 계층 구조의 일부가 손실되어도 실행되는 서비스에 영향을 주지 않도록 노력합니다.

장애 도메인 계층 구조의 각 수준에서 노드 수가 동일한 것이 가장 좋습니다. 장애 도메인의 “트리”가 클러스터에서 분산되지 않은 경우, 클러스터 리소스 관리자에서 최상의 서비스 재할당을 계산하기가 더 어려워집니다. 불균형 장애 도메인 레이아웃은 일부 도메인의 손실이 다른 도메인보다 서비스 가용성에 더 영향을 준다는 것을 의미합니다. 결과적으로 클러스터 리소스 관리자는 두 가지 목표 사이에서 분열할 수 있습니다.

* 이는 “사용량이 많은” 도메인에 서비스를 배치하여 해당 컴퓨터를 사용하려고 합니다. 
* 도메인 손실로 인해 문제가 발생하지 않도록 다른 도메인에 서비스를 추가하려고 합니다.

분산되지 않은 도메인은 어떤 모양일까요? 다음 다이어그램에서는 두 개의 서로 다른 클러스터 레이아웃을 보여 줍니다. 첫 번째 예시의 경우 노드는 장애 도메인 간에 균등하게 분산됩니다. 두 번째 예시의 경우 하나의 장애 도메인에는 다른 장애 도메인보다 더 많은 노드가 있습니다.

![두 개의 다른 클러스터 레이아웃][Image2]

Azure에서는 장애 도메인에 노드가 포함된 선택 항목이 사용자를 위해 관리됩니다. 그러나 프로비전하는 노드의 수에 따라 다른 도메인보다 더 많은 노드가 있는 장애 도메인이 발생할 수 있습니다.

예를 들어 클러스터에 5개의 장애 도메인이 있지만 노드 형식(**NodeType**)에 대해 7개의 노드를 프로비전한다고 가정합니다. 이 경우 처음 두 장애 도메인에 더 많은 노드가 배치됩니다. 몇 개의 인스턴스만 사용하여 더 많은 **NodeType** 을 계속 배포하는 경우 문제가 악화됩니다. 이러한 이유로 각 노드 형식의 노드 수는 장애 도메인 수의 배수인 것이 좋습니다.

## <a name="upgrade-domains"></a>업그레이드 도메인

업그레이드 도메인은 Service Fabric 클러스터 리소스 관리자에서 클러스터의 레이아웃을 이해하는 데 도움이 되는 또 다른 기능입니다. 업그레이드 도메인은 업그레이드된 노드 집합을 동시에 정의합니다. 업그레이드 도메인은 클러스터 리소스 관리자가 업그레이드와 같은 관리 작업을 이해하고 오케스트레이션하는 데 도움이 됩니다.

업그레이드 도메인은 장애 도메인과 매우 유사하지만 몇 가지 주요 차이점이 있습니다. 먼저 장애 도메인은 조정된 하드웨어 오류 영역으로 정의됩니다. 반면 업그레이드 도메인은 정책으로 정의됩니다. 환경에서 숫자를 결정하는 대신 원하는 개수를 결정할 수 있습니다. 노드 숫자만큼의 업그레이드 도메인을 가질 수 있습니다. 장애 도메인과 업그레이드 도메인의 또 다른 차이점은 업그레이드 도메인은 계층적 구조가 아니라는 것입니다. 대신 간단한 태그와 비슷합니다.

다음 다이어그램에서는 3개의 업그레이드 도메인이 세 개의 장애 도메인에 스트라이프되어 있음을 보여 줍니다. 또한 상태 저장 서비스의 세 개의 서로 다른 복제본을 배치하는 한 가지 방법을 보여 줍니다. 이들은 각각 다른 장애 도메인 및 업그레이드 도메인에 배치됩니다. 이 배치를 사용하면 서비스를 업그레이드하는 중에 장애 도메인이 손실되어도 코드와 데이터의 복사본이 하나씩 남아 있을 수 있습니다.  

![장애 도메인 및 업그레이드 도메인으로 배치][Image3]

많은 수의 업그레이드 도메인이 있는 경우 장단점이 있습니다. 업그레이드 도메인이 많으면 업그레이드의 각 단계가 더 세분화되어 더 적은 수의 노드 또는 서비스에 영향을 줍니다. 한 번에 이동해야 하는 서비스가 줄어들어 시스템 변동이 줄어듭니다. 그러면 서비스가 업그레이드 중에 발생된 모든 문제로 인해 영향을 받는 서비스가 줄기 때문에 안정성을 향상시키는 경향이 있습니다. 또한 업그레이드 도메인이 많아질수록 업그레이드의 영향을 처리하기 위해 다른 노드에서 사용할 수 있는 버퍼는 더 적게 필요합니다.

예를 들어 5개의 업그레이드 도메인이 있는 경우 각 도메인의 노드는 트래픽의 약 20%를 처리합니다. 업그레이드를 위해 업그레이드 도메인을 중단해야 하는 경우 일반적으로 해당 로드를 다른 곳으로 이동해야 합니다. 남은 업그레이드 도메인이 4개이므로 각각에는 총 트래픽의 약 25%를 차지할 수 있는 공간이 있어야 합니다. 업그레이드 도메인이 많아질수록 클러스터의 노드에 필요한 버퍼는 더 적어집니다.

10개의 업그레이드 도메인이 있는 경우를 가정합니다. 이 경우 각 업그레이드 도메인에서는 총 트래픽의 약10%만 처리합니다. 클러스터를 통해 업그레이드가 진행될 때 각 도메인에는 총 트래픽의 약 11% 공간만 있으면 됩니다. 일반적으로 업그레이드 도메인이 많을수록 예약된 용량이 덜 필요하므로 더 높은 사용률로 노드를 실행할 수 있습니다. 장애 도메인에서도 마찬가지입니다.  

많은 업그레이드 도메인을 가진 경우의 단점은 업그레이드 시간이 오래 걸린다는 점입니다. Service Fabric은 업그레이드 도메인이 완료된 후 잠시 기다렸다가 다음 도메인 업그레이드를 시작하기 전에 검사를 수행합니다. 이러한 지연은 업그레이드가 진행되기 전에 업그레이드로 인한 문제를 감지할 수 있게 합니다. 이러한 단점은 한 번에 너무 많은 서비스에 영향을 주는 잘못된 변경 사항을 방지하기 때문에 받아들일 만합니다.

업그레이드 도메인이 적은 경우 부작용이 많이 있습니다. 각 업그레이드 도메인이 다운되고 업그레이드되는 동안 전체 용량의 상당 부분을 사용할 수 없습니다. 예를 들어, 업그레이드 도메인이 3개뿐인 경우, 전체 서비스 또는 클러스터 용량의 약 1/3을 한 번에 중단합니다. 워크로드를 처리하는 클러스터의 나머지 부분에 용량이 충분해야 하기 때문에 한 번에 서비스의 상당 부분이 중단되는 것은 바람직하지 않습니다. 해당 버퍼를 유지 관리하면 정상 작업 중에 이러한 노드가 다른 경우에 로드하는 것보다 더 적게 로드됩니다. 이로 인해 서비스 실행 비용이 증가합니다.

환경에서 장애 또는 업그레이드 도메인의 총 수는 실제 제한이나 중첩 방법에 대한 제약이 없습니다. 그러나 다음과 같은 일반적인 패턴이 있습니다.

* 1:1 매핑된 장애 도메인 및 업그레이드 도메인
* 노드(물리적 또는 가상 OS 인스턴스) 당 하나의 업그레이드 도메인
* 장애 도메인 및 업그레이드 도메인이 대각선으로 실행되는 컴퓨터를 사용하여 행렬을 형성하는 “스트라이프” 또는 “매트릭스” 모델

![장애 도메인 및 업그레이드 도메인 레이아웃][Image4]

어떤 레이아웃을 선택하느냐에 대한 정답은 없습니다. 각각에 장점 및 단점이 있습니다. 예를 들어 1FD:1UD 모델의 설치는 간단합니다. 노드 모델 당 하나의 업그레이드 도메인 모델은 사람들이 익숙한 모델과 가장 비슷합니다. 업그레이드하는 동안 각 노드는 독립적으로 업데이트됩니다. 이는 이전에 수동으로 작은 컴퓨터 집합을 업그레이드한 방법과 비슷합니다.

가장 일반적인 모델은 FD/UD 행렬이며, 여기서 장애 도메인과 업그레이드 도메인은 테이블을 형성하고 노드는 대각선을 따라 배치됩니다. 이는 Azure의 Service Fabric 클러스터에서 기본적으로 사용되는 모델입니다. 노드가 많은 클러스터의 경우 모든 것이 조밀한 행렬 패턴처럼 보입니다.

> [!NOTE]
> Azure에서 호스트되는 Service Fabric 클러스터는 기본 전략의 변경을 지원하지 않습니다. 독립 실행형 클러스터만 해당 사용자 지정을 제공합니다.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>장애 및 업그레이드 도메인 제약 조건 및 결과 동작
### <a name="default-approach"></a>기본 접근 방식

기본값으로 클러스터 리소스 관리자는 장애 도메인 및 업그레이드 도메인에서 서비스 간의 균형을 유지합니다. 이것이 [제약 조건](service-fabric-cluster-resource-manager-management-integration.md)으로 모델링됩니다. 장애 도메인 및 업그레이드 도메인에 대한 제약 조건은 다음과 같습니다. “지정된 서비스 파티션에서, 계층 구조 수준이 동일한 두 도메인 간의 서비스 개체(상태 비저장 서비스 인스턴스 또는 상태 저장 서비스 복제본) 수는 1개 이상 차이가 나지 않아야 합니다.”

이 제약 조건이 “최대 차이”를 보장한다고 가정해 봅니다. 장애 도메인 및 업그레이드 도메인에 대한 제약 조건은 규칙을 위반하는 특정 이동 또는 정렬을 방지합니다.

예를 들어, 5개의 장애 도메인과 5개의 업그레이드 도메인으로 구성된 6개의 노드가 있는 클러스터가 있다고 가정해 봅니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

이제 **TargetReplicaSetSize** (또는 상태 비저장 서비스의 경우 **InstanceCount**) 값이 5인 서비스를 만든다고 가정해 봅니다. 복제본은 N1-N5에 생성됩니다. 실제로 많은 서비스를 만들더라도 N6은 절대 사용되지 않습니다. 그렇지만 그 이유는 무엇일까요? N6을 선택하는 경우 현재 레이아웃 및 발생하는 상황 간의 차이를 살펴보겠습니다.

다음은 장애 도메인 및 업그레이드 도메인별 구현되는 레이아웃 및 총 복제본 수입니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 및 업그레이드 도메인당 노드의 측면에서 균형을 이룹니다. 또한 장애 도메인 및 업그레이드 도메인당 복제본 수의 측면에서도 균형을 이룹니다. 각 도메인에는 동일한 수의 노드 및 동일한 수의 복제본이 있습니다.

이제 N2 대신 N6를 사용하는 경우 발생하는 결과를 살펴보겠습니다. 복제본은 어떻게 배포되나요?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 제약 조건에 대한 “최대 차이” 보증에 대한 정의를 위반합니다. FD0에는 두 개의 복제본이 있고, FD1에는 없습니다. FD0과 FD1 사이에 총 2의 차이가 있으며, 이는 최대 차이인 1보다 큽니다. 제약 조건을 위반하기 때문에 클러스터 리소스 관리자는 이 정렬을 허용하지 않습니다.

마찬가지로 N2 및 N6(N1 및 N2 대신)을 선택한 경우 다음 결과를 얻을 수 있습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 측면에서 분산됩니다. 그러나 UD0에는 복제본이 하나도 없고 UD1에는 두 개가 있으므로 업그레이드 도메인 제약 조건을 위반합니다. 이 레이아웃도 유효하지 않으며 클러스터 리소스 관리자가 선택하지 않습니다.

이 상태 저장 복제본 또는 상태 비저장 인스턴스 배포 방식은 가능한 최고의 내결함성을 제공합니다. 한 도메인이 중지되는 경우, 복제본/인스턴스 손실이 최소화됩니다.

반면 이 방법은 지나치게 엄격하여 클러스터가 모든 리소스를 활용하지 못할 수 있습니다. 특정 클러스터 구성에서는 특정 노드를 사용할 수 없습니다. 이로 인해 Service Fabric은 서비스를 사용할 수 없으므로 경고 메시지가 생성될 수 있습니다. 이전 예시에서는 일부 클러스터 노드(이 예시에서는 N6)를 사용할 수 없습니다. 클러스터에 노드를 추가한 경우에도(N7-N10) 장애 도메인 및 업그레이드 도메인에 대한 제약 조건 때문에 복제본/인스턴스는 N1 – N5에만 배치됩니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>대안

클러스터 리소스 관리자는 장애 도메인 및 업그레이드 도메인에 대한 다른 버전의 제약 조건을 지원합니다. 최소 수준의 안전을 보장하면서 배치를 허용합니다. 대체 제약 조건은 다음과 같이 설명할 수 있습니다. “지정된 서비스 파티션에서, 여러 도메인에 복제본을 배포할 때 파티션에서 쿼럼 손실 문제가 발생하지 않아야 합니다.” 이 제약 조건이 “쿼럼 안전” 보증을 제공한다고 가정해 봅니다.

> [!NOTE]
> 상태 저장 서비스의 경우 대부분의 파티션 복제본이 동시에 중지되는 상황에서 *쿼럼 손실* 을 정의합니다. 예를 들어 **TargetReplicaSetSize** 가 5이면 모든 3개의 복제본 집합은 쿼럼을 나타냅니다. 마찬가지로, **TargetReplicaSetSize** 가 6인 경우에는 쿼럼에 4개의 복제본이 필요합니다. 두 경우 모두 파티션이 계속해서 정상적으로 작동하기를 원한다면 동시에 중지 가능한 복제본 수는 2개 이하입니다.
>
> 상태 비저장 서비스의 경우 *쿼럼 손실* 은 없습니다. 상태 비저장 서비스는 보통 대부분의 인스턴스가 동시에 다운되는 경우에도 계속 정상적으로 작동합니다. 이 문서의 나머지 부분에서는 상태 저장 서비스에 대해 집중적으로 설명합니다.
>

이전 예제로 돌아갑시다. 제약 조건의 “쿼럼 안전” 버전에서는 지정된 레이아웃 세 가지가 모두 유효합니다. 두 번째 레이아웃에서 FD0이 실패했거나 세 번째 레이아웃에서 UD1이 실패한 경우에도 파티션은 쿼럼을 유지합니다. 대부분의 복제본은 계속 작동합니다. 이 버전의 제약 조건을 사용하는 경우 N6는 거의 항상 활용될 수 있습니다.

“쿼럼 안전” 접근 방식은 “최대 차이” 접근 방식보다 유연합니다. 거의 모든 클러스터 토폴로지에서 유효한 복제본 배포를 찾기가 더 쉽기 때문입니다. 그러나 이 접근 방식은 일부 오류가 다른 오류보다 심각하기 때문에 최고의 내결함성을 보장할 수 없습니다.

최악의 경우 하나의 도메인과 하나의 추가 복제본에서 오류가 발생할 때 복제본의 대부분이 손실될 수 있습니다. 예를 들어 복제본 또는 인스턴스 5개가 있는 쿼럼이 손실되려면 오류 3건이 필요했지만, 이제는 2건만으로도 대부분이 손실될 수 있습니다.

### <a name="adaptive-approach"></a>적응형 접근 방식

두 접근 방식 모두 장점과 단점이 있기 때문에 두 가지 전략을 결합한 적응형 접근 방식을 도입했습니다.

> [!NOTE]
> Service Fabric 버전 6.2부터는 이것이 기본 동작입니다.
>
> 적응형 접근 방식은 기본적으로 "최대 차이" 논리를 사용하다가 필요할 때만 "쿼럼 안전" 논리로 전환합니다. 클러스터 리소스 관리자는 클러스터 및 서비스가 구성된 방식을 확인하여 어떤 전략이 필요한지 자동으로 계산합니다.
>
> 클러스터 리소스 관리자는 다음 두 조건이 모두 맞는 서비스에 대해 “쿼럼 기반” 논리를 사용해야 합니다.
>
> * 서비스에 대한 **TargetReplicaSetSize** 를 장애 도메인 수와 업그레이드 도메인 수로 균등하게 나눌 수 있습니다.
> * 노드 수가 업그레이드 도메인 수에 장애 도메인 수를 곱한 수보다 적거나 같습니다.
>
> 쿼럼 손실이 상태 비저장 서비스와는 관련이 없지만, 클러스터 리소스 관리자는 상태 비저장 서비스와 상태 저장 서비스 모두에 이 접근 방식을 사용한다는 것을 기억해야 합니다.

이전 예시로 돌아가서 클러스터에 8개의 노드가 있다고 가정해 봅니다. 클러스터는 여전히 5개의 장애 도메인과 5개의 업그레이드 도메인으로 구성되며 해당 클러스터에서 호스트되는 서비스의 **TargetReplicaSetSize** 값은 여전히 5입니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

필요한 모든 조건이 충족되므로 클러스터 리소스 관리자는 서비스 배포에 “쿼럼 기반” 논리를 사용합니다. 따라서 N6–N8을 사용할 수 있습니다. 이 경우 가능한 서비스 배포 중 하나는 다음과 같습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

예를 들어, 서비스의 **TargetReplicaSetSize** 가 4로 축소되면 클러스터 리소스 관리자가 해당 변경 내용을 통지합니다. **TargetReplicaSetSize** 를 더 이상 장애 도메인 및 업그레이드 도메인 수로 나눌 수 없으므로 “최대 차이” 논리를 사용하여 다시 시작됩니다. 결과적으로, N1-N5 노드에 있는 나머지 복제본 4개를 배포하기 위해 특정 복제본 이동이 발생합니다. 이렇게 하면 장애 도메인 및 업그레이드 도메인 논리의 “최대 차이” 버전이 위반되지 않습니다.

이전 레이아웃에서 **TargetReplicaSetSize** 값이 5이고 N1이 클러스터에서 제거되면 업그레이드 도메인 수는 4와 같습니다. 마찬가지로 클러스터 리소스 관리자는 업그레이드 도메인 수가 서비스의 **TargetReplicaSetSize** 값을 더 이상 균등하게 나누지 않기 때문에 “최대 차이” 논리를 사용하기 시작합니다. 결과적으로 복제본 R1은 다시 빌드되면 장애 도메인 및 업그레이드 도메인에 대한 제약 조건을 위반하지 않기 위해 N4에 도착해야 합니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |해당 없음 |해당 없음 |해당 없음 |해당 없음 |해당 없음 |해당 없음 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>장애 및 업그레이드 도메인 구성

Azure 호스팅 Service Fabric 배포에서는 장애 도메인 및 업그레이드 도메인이 자동으로 정의됩니다. Service Fabric은 Azure의 환경 정보를 선택하고 사용합니다.

사용자 고유의 클러스터를 만들거나 개발 중인 특정 토폴로지를 실행하려는 경우 장애 도메인 및 업그레이드 도메인 정보를 직접 제공할 수 있습니다. 이 예시에서는 각각 세 개의 랙을 가진 세 가지 데이터 센터에 걸쳐 있는 9개의 노드 로컬 개발 클러스터를 정의합니다. 이 클러스터에는 세 개의 해당 데이터 센터에 스트라이프된 3개의 업그레이드 도메인도 있습니다. ClusterManifest.xml의 구성 예는 다음과 같습니다.

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

이 예에서는 독립 실행형 배포에 대한 ClusterConfig.json을 사용합니다.

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
> Azure Resource Manager를 통해 클러스터를 정의하는 경우, Azure는 장애 도메인 및 업그레이드 도메인을 할당합니다. 따라서 Azure Resource Manager 템플릿에 있는 노드 형식 및 가상 머신 확장 집합의 정의에 장애 도메인 또는 업그레이드 도메인에 대한 정보가 포함되지 않습니다.
>

## <a name="node-properties-and-placement-constraints"></a>노드 속성 및 배치 제약 조건

때로는(실제로 대부분의 경우) 특정 워크로드가 클러스터의 특정 노드 형식에서만 실행되도록 하려고 할 수 있습니다. 예를 들어 일부 워크로드에는 GPU 또는 SSD가 필요할 수 있으며, 일부 작업에는 그렇지 않을 수 있습니다.

특정 워크로드에 대한 하드웨어를 대상으로 하는 예시는 거의 모든 n 계층 아키텍처입니다. 특정 머신은 프런트 엔드 또는 애플리케이션에서 제공하는 API 쪽의 역할을 하며 클라이언트나 인터넷에 노출됩니다. 종종 서로 다른 하드웨어 리소스를 사용하는 다양한 머신에서 컴퓨팅 또는 스토리지 계층의 작업을 처리합니다. 이러한 컴퓨터는 대개 클라이언트 또는 인터넷에 직접 노출되지 _않습니다_.

Service Fabric은 특정 하드웨어 구성에서 특정 워크로드를 실행해야 하는 경우가 있다고 예상합니다. 예를 들면 다음과 같습니다.

* 기존 n 계층 애플리케이션은 Service Fabric 환경으로 “리프트 및 이동”되었습니다.
* 성능, 규모 또는 보안상의 이유 등으로 특정 하드웨어에서 워크로드를 실행해야 합니다.
* 워크로드는 정책 또는 리소스 소비를 이유로 다른 워크로드로부터 격리되어야 합니다.

이러한 유형의 구성을 지원하기 위해, Service Fabric에는 노드에 적용될 수 있는 태그가 포함됩니다. 이러한 태그를 *노드 속성* 이라고 합니다. *배치 제약 조건* 은 하나 이상의 노드 속성에 대해 선택하는 개별 서비스에 연결되는 명령문입니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다. 제약 조건 집합은 확장 가능합니다. 모든 키/값 쌍이 작동할 수 있습니다. Service Fabric 8.1부터 노드 속성은 실행 중인 작업을 중단하지 않고 동적으로 업데이트할 수 있습니다.

![클러스터 레이아웃에 대한 다양한 워크로드][Image5]

### <a name="built-in-node-properties"></a>기본 제공 노드 속성

Service Fabric은 자동으로 사용할 수 있는 몇 가지 기본 노드 속성을 정의하므로 사용자가 정의하지 않아도 됩니다. 각 노드에 정의되는 기본 속성은 **NodeType** 및 **NodeName** 입니다.

예를 들어 배치 제약 조건을 `"(NodeType == NodeType03)"`으로 작성할 수 있습니다. **NodeType** 은 일반적으로 사용되는 속성입니다. 이는 컴퓨터 종류와 1:1로 대응하므로 유용합니다. 각 머신의 종류는 기존의 n 계층 애플리케이션에서 워크로드 유형에 해당합니다.

![배치 제약 조건 및 노드 속성][Image6]

## <a name="placement-constraints-and-node-property-syntax"></a>배치 제약 조건 및 노드 속성 구문

노드 속성에서 지정된 값은 문자열, 부울 또는 기호가 있는 long이 될 수 있습니다. 서비스가 클러스터에서 실행할 수 있는 위치를 제한하기 때문에 서비스의 문은 배치 *제약 조건* 이라고 합니다. 이 제약 조건은 클러스터의 다른 노드 속성에 작동하는 모든 부울 문일 수 있습니다. 이러한 부울 문의 유효한 선택기는 다음과 같습니다.

* 특정 문을 만들기 위한 조건부 검사

  | 문 | 구문 |
  | --- |:---:|
  | "같음" | "==" |
  | "다음과 같지 않음" | "!=" |
  | "다음보다 큼" | ">" |
  | "다음보다 크거나 같음" | ">=" |
  | "다음보다 작음" | "<" |
  | "다음보다 작거나 같음" | "<=" |

* 그룹화 및 논리 작업에 대한 부울 문

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

전체 배치 제약 조건 문이 "True"로 평가되는 노드에만 서비스가 배치될 수 있습니다. 정의된 속성이 없는 노드는 속성을 포함하는 배치 제약 조건과 일치하지 않습니다.

ClusterManifest.xml에서 노드 형식에 대해 다음과 같은 노드 속성이 정의되어 있다고 가정해 보겠습니다.

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

다음 예에서는 독립 실행형 배포에 대한 ClusterConfig.json 또는 Azure에서 호스트되는 클러스터에 대한 Template.json을 통해 정의된 노드 속성을 보여 줍니다.

> [!NOTE]
> Azure Resource Manager 템플릿에서 노드 형식은 대개 매개 변수화됩니다. NodeType01보다는 `"[parameters('vmNodeType1Name')]"`와 비슷합니다.
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

다음과 같이 서비스에 대한 서비스 배치 *제약 조건* 을 만들 수 있습니다.

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

NodeType01의 모든 노드가 유효하면 `"(NodeType == NodeType01)"` 제약 조건을 사용하여 해당 노드 형식을 선택할 수도 있습니다.

서비스의 배치 제약 조건은 런타임 중에 동적으로 업데이트할 수 있습니다. 따라서 필요한 경우 클러스터에서 서비스를 이동할 수 있으며, 요구 사항 등을 추가 및 제거할 수 있습니다. Service Fabric은 이러한 유형의 변경이 수행된 경우에도 서비스를 유지하고 사용할 수 있도록 보장합니다.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

배치 제약 조건은 명명된 모든 서비스 인스턴스에 대해 지정됩니다. 업데이트는 이전에 지정된 항목(덮어쓰기)에서 발생합니다.

클러스터 정의는 노드의 속성을 정의합니다. 노드의 속성을 변경하려면 클러스터 구성을 업그레이드해야 합니다. 노드 속성을 업그레이드하려면 영향을 받는 각 노드를 다시 시작하여 새 속성을 보고해야 합니다. Service Fabric은 이러한 롤링 업그레이드를 관리합니다.

## <a name="describing-and-managing-cluster-resources"></a>클러스터 리소스 설명 및 관리

모든 조정자의 가장 중요한 작업 중 하나는 클러스터에서 리소스 소비를 관리할 수 있도록 돕는 일입니다. 클러스터 리소스 관리는 여러 가지 다른 작업을 의미할 수 있습니다.

첫째, 컴퓨터에 과부하가 걸리지 않도록 해야 합니다. 즉 컴퓨터에서 처리할 수 있는 것보다 더 많은 서비스를 실행하지 않도록 하는 것입니다.

둘째, 서비스를 효율적으로 실행하는 데 중요한 분산 및 최적화가 있습니다. 비용 효율적이거나 성능에 민감한 서비스 제품은 일부 노드가 핫 노드이면서 다른 노드가 콜드 노드이도록 허용하지 않습니다. 핫 노드는 리소스 경합 및 성능 저하를 일으킵니다. 콜드 노드는 낭비된 리소스와 증가된 비용을 나타냅니다.

Service Fabric은 리소스를 *메트릭* 으로 나타냅니다. 메트릭은 서비스 패브릭에 대해 설명하려는 논리적 또는 물리적 리소스입니다. 메트릭의 예로는 “WorkQueueDepth” 또는 “MemoryInMb”가 있습니다. Service Fabric에서 노드에 대해 관리할 수 있는 물리적 리소스에 대한 자세한 내용은 [리소스 거버넌스](service-fabric-resource-governance.md)를 참조하세요. 클러스터 리소스 관리자가 사용하는 기본 메트릭 및 사용자 지정 메트릭을 구성하는 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

메트릭은 배치 제약 조건 및 노드 속성과 다릅니다. 노드 속성은 노드 자체의 정적 설명자입니다. 메트릭은 노드에 존재하고 서비스가 노드에서 실행할 때 사용하는 리소스를 설명합니다. 노드 속성은 **HasSSD** 일 수 있고 true 또는 false로 설정될 수 있습니다. 해당 SSD에서 사용할 수 있는 공간의 양과 서비스에서 사용되는 양은 “DriveSpaceInMb”와 같은 메트릭일 수 있습니다.

배치 제약 조건 및 노드 속성의 경우와 같이 Service Fabric 클러스터 리소스 관리자가 메트릭 이름의 의미를 이해하지 못합니다. 메트릭 이름은 단순한 문자열입니다. 만든 메트릭 이름이 모호할 경우 단위를 그 일부로 선언하는 것이 좋습니다.

## <a name="capacity"></a>용량

모든 리소스 *분산* 을 해제한 경우에도 Service Fabric의 클러스터 리소스 관리자는 여전히 노드가 해당 용량을 초과하지 않도록 합니다. 클러스터가 가득 차지 않았거나 워크로드가 어떤 노드보다 크지 않으면 용량 초과를 관리할 수 있습니다. 용량은 클러스터 리소스 관리자가 노드에 있는 리소스의 양을 이해하기 위해 사용하는 또 다른 *제약 조건* 입니다. 전체 클러스터에 대해 남은 용량을 추적합니다. Service Fabric 8.1부터 노드 용량은 실행 중인 워크로드의 중단 없이 동적으로 업데이트할 수 있습니다.

서비스 수준에서 용량과 소비량은 메트릭을 기준으로 표현됩니다. 예를 들어 메트릭은 “ClientConnections”이고 노드의 용량은 32,768인 “ClientConnections”의 용량일 수 있습니다. 다른 노드에는 다른 제한이 있을 수 있습니다. 해당 노드에서 실행되는 서비스는 현재 32,256인 메트릭 “ClientConnections”를 사용 중이라고 말할 수 있습니다.

런타임 중에 클러스터 리소스 관리자는 클러스터 및 노드의 나머지 용량을 추적합니다. 용량을 추적하기 위해 클러스터 리소스 관리자는 서비스에서 실행되는 노드의 용량에서 각 서비스의 사용량을 뺍니다. 이 정보를 통해 클러스터 리소스 관리자는 노드가 용량을 초과하지 않도록 복제본을 배치하거나 이동할 위치를 파악할 수 있습니다.

![클러스터 노드 및 용량][Image7]

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

클러스터 매니페스트에 정의된 용량을 확인할 수 있습니다. ClusterManifest.xml의 예시는 다음과 같습니다.

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

다음은 독립 실행형 배포를 위한 ClusterConfig.json 또는 Azure 호스팅 클러스터에 대한 Template.json을 통해 정의된 용량의 예입니다.

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

서비스의 부하는 종종 동적으로 변경됩니다. 복제본의 “ClientConnections” 로드가 1,024에서 2,048로 변경된 경우를 가정합니다. 해당 메트릭에 대해 실행 중인 노드의 용량은 512뿐이었습니다. 이제 해당 노드에 충분한 공간이 없으므로 복제본 또는 인스턴스의 배치가 잘못되었습니다. 클러스터 리소스 관리자는 노드를 용량 이하로 되돌려야 합니다. 하나 이상의 복제본 또는 인스턴스를 해당 노드에서 다른 노드로 이동하여 용량을 초과하는 노드의 부하를 줄입니다.

클러스터 리소스 관리자에서는 이러한 복제본 이동 비용을 최소화하려고 합니다. [이동 비용](service-fabric-cluster-resource-manager-movement-cost.md) 및 [균형 재조정 전략 및 규칙](service-fabric-cluster-resource-manager-metrics.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="cluster-capacity"></a>클러스터 용량

그렇다면 Service Fabric 클러스터 리소스 관리자에서 전체 클러스터가 가득 차지 않도록 유지하는 방법은 무엇일까요? 동적 로드로 수행할 수 있는 작업은 많지 않습니다. 클러스터 리소스 관리자에서 수행하는 작업과 별개로 서비스의 부하가 급증할 수 있습니다. 즉, 오늘은 여유가 많은 클러스터가 내일은 수요가 많아져 전력이 부족해질 수 있습니다.

클러스터 리소스 관리자 제어는 문제를 방지하는 데 도움이 됩니다. 가장 먼저 수행할 수 있는 작업은 클러스터가 가득 찰 수 있는 새 작업을 만드는 것을 방지하는 것입니다.

상태 비저장 서비스를 만들고 일부 부하가 연결되어 있다고 가정해 봅니다. 서비스는 “DiskSpaceInMb” 메트릭을 고려합니다. 서비스는 서비스의 모든 인스턴스에 대해 5개의 “DiskSpaceInMb” 단위를 사용합니다. 3개의 서비스 인스턴스를 만들려고 합니다. 즉, 이러한 서비스 인스턴스를 만들기 위해 클러스터에 15단위의 “DiskSpaceInMb”가 있어야 합니다.

클러스터 리소스 관리자에서 각 메트릭의 용량 및 사용량을 지속적으로 계산하여 클러스터에 남아 있는 용량을 결정할 수 있습니다. 공간이 충분하지 않으면 클러스터 리소스 관리자에서 서비스 만들기 호출을 거부합니다.

요청은 15단위를 사용할 수 있기 때문에 다양한 방법으로 이 공간을 할당할 수 있습니다. 예를 들어, 다른 15개의 노드에서 남은 1단위의 용량이나 다른 5개의 노드에서 남은 3단위의 용량일 수 있습니다. 클러스터 리소스 관리자에서 작업을 다시 정렬하여 3개 노드에서 5단위를 사용할 수 있으면 서비스를 배치합니다. 클러스터가 거의 가득차거나 어떤 이유로 기존 서비스를 통합할 수 없는 경우가 아니면 일반적으로 클러스터를 다시 정렬할 수 있습니다.

## <a name="node-buffer-and-overbooking-capacity"></a>노드 버퍼 및 초과 예약 용량

메트릭에 대한 노드 용량이 지정된 경우에는 클러스터 리소스 관리자에서 전체 부하가 지정된 노드 용량을 초과하는 경우 복제본을 배치하거나 이동하지 않습니다. 이로 인해 클러스터의 용량이 거의 충분하고 부하가 많은 복제본을 배치, 교체 또는 이동해야 하는 경우에는 때때로 새 복제본을 배치하거나 실패한 복제본을 대체할 수 있습니다.

더 많은 유연성을 제공하기 위해 노드 버퍼 또는 초과 예약 용량을 지정할 수 있습니다. 메트릭에 대해 노드 버퍼 또는 초과 예약 용량이 지정된 경우, 클러스터 리소스 관리자는 버퍼 또는 초과 예약 용량이 사용되지 않는 상태로 유지되는 방식으로 복제본을 배치하거나 이동하려고 시도하지만, 다음과 같은 서비스 가용성을 향상시키는 작업에 필요한 경우 버퍼 또는 초과 예약 용량을 사용할 수 있습니다.

* 새 복제본 배치 또는 실패한 복제본 바꾸기
* 업그레이드 중 배치
* 소프트 및 하드 제약 조건 위반 수정
* 조각 모음

노드 버퍼 용량은 지정된 노드 용량 이하인 예약된 용량 부분을 나타내며, 초과 예약 용량은 지정된 노드 용량보다 높은 추가 용량을 나타냅니다. 두 경우 모두 클러스터 리소스 관리자는 이 용량을 사용 가능한 상태로 유지하려고 시도합니다.

예를 들어, 노드에 메트릭 *CpuUtilization* 에 대해 지정된 용량이 100이고 해당 메트릭에 대한 노드 버퍼 비율이 20%로 설정된 경우, 총 용량 및 버퍼링되지 않은 용량은 각각 100 및 80이 되며, 클러스터 리소스 관리자는 정상적인 상황에서 노드에 80개 이상의 부하를 배치하지 않습니다.

![총 용량은 노드 용량과 같습니다(노드 버퍼 용량+ 버퍼링되지 않은 용량)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

노드 버퍼는 위에서 언급한 서비스 가용성을 높이는 작업에만 사용되는 노드 용량의 일부를 예약하려는 경우에 사용해야 합니다.

반면 노드 초과 예약 비율을 20%로 사용 및 설정하면 총 용량 및 버퍼링되지 않은 용량은 각각 120 및 100이 됩니다.

![총 용량은 초과 예약 용량과 노드 용량(초과 예약 용량+ 버퍼링되지 않은 용량)과 같습니다](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

총 리소스 사용량이 용량을 초과하는 경우에도 클러스터 리소스 관리자가 노드에 복제본을 배치할 수 있도록 하려면 초과 예약 용량을 사용해야 합니다. 성능이 저하되는 경우 서비스에 대한 추가 가용성을 제공하는 데 사용할 수 있습니다. 초과 예약 용량을 사용하는 경우 사용자 애플리케이션 로직은 필요한 것보다 적은 수의 물리적 리소스로 작동할 수 있어야 합니다.

노드 버퍼 또는 초과 예약 용량이 지정된 경우 대상 노드의 총 부하가 총 용량(노드 버퍼의 경우 노드 용량, 초과 예약의 경우 노드 용량 + 초과 예약 용량)을 초과하면 클러스터 리소스 관리자는 복제본을 이동하거나 배치하지 않습니다.

초과 예약 용량을 무한으로 지정할 수도 있습니다. 이 경우 클러스터 리소스 관리자는 노드의 총 부하를 지정된 노드 용량 이하로 유지하려고 하지만, 잠재적으로 노드에 훨씬 더 많은 부하가 발생하여 심각한 성능 저하가 발생할 수 있습니다.

메트릭은 노드 버퍼와 초과 예약 용량을 동시에 보유할 수 없습니다.

*ClusterManifest.xml* 에서 노드 버퍼 또는 초과 예약 용량을 지정하는 방법의 예는 다음과 같습니다.

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

다음은 독립 실행형 배포에 대한 *ClusterConfig.json* 또는 Azure 호스팅 클러스터에 대한 *Template.json* 을 통해 노드 버퍼를 지정하거나 용량을 초과 예약하는 방법의 예입니다.

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>다음 단계

* 클러스터 리소스 관리자 내의 아키텍처 및 정보 흐름에 대한 자세한 내용은 [클러스터 리소스 관리자 아키텍처 개요](service-fabric-cluster-resource-manager-architecture.md)를 참조하세요.
* 조각 모음 메트릭을 정의하는 것은 노드를 분산하는 대신 노드에 로드를 통합하는 한 가지 방법입니다. 조각 모음을 구성하는 방법에 대한 자세한 내용은 [Service Fabric에서 메트릭 및 로드 조각 모음](service-fabric-cluster-resource-manager-defragmentation-metrics.md)을 참조하세요.
* 처음부터 시작하여 [Service Fabric 클러스터 리소스 관리자 소개를 확인합니다](service-fabric-cluster-resource-manager-introduction.md).
* 클러스터 리소스 관리자가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [Service Fabric 클러스터 부하 분산](service-fabric-cluster-resource-manager-balancing.md)을 참조하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
