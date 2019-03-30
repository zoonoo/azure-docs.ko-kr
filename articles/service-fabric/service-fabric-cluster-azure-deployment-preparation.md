---
title: Azure Service Fabric 클러스터 배포 계획 | Microsoft Docs
description: 계획 및 azure Service Fabric 클러스터 배포는 프로덕션 준비에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663241"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>클러스터 배포를 위한 계획 및 준비

계획 및 프로덕션 클러스터 배포에 대 한 준비이 매우 중요 합니다.  여러 가지 요인을 고려해 야 합니다.  이 문서에서는 클러스터 배포를 준비 하는 단계를 안내 합니다.

## <a name="read-the-best-practices-information"></a>모범 사례 정보
Azure Service Fabric 응용 프로그램 및 클러스터를 성공적으로 관리 하는 좋습니다 프로덕션 환경의 안정성을 최적화 하기 위해 수행한 작업 있습니다.  자세한 내용은 [Service Fabric 응용 프로그램 및 클러스터에 대 한 모범 사례](service-fabric-best-practices-overview.md)합니다.

## <a name="select-the-os-for-the-cluster"></a>클러스터에 대 한 OS를 선택 합니다.
Azure Service Fabric을 사용하면 Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다.  클러스터를 배포 하기 전에 OS를 선택 해야 합니다.  Windows 또는 Linux입니다.  동일한 OS를 실행 하는 클러스터의 모든 노드 (가상 머신), Windows 및 Linux Vm을 동일한 클러스터에 혼합할 수 없습니다.

## <a name="capacity-planning"></a>용량 계획
프로덕션 배포의 경우 용량 계획은 중요한 단계입니다. 다음은 해당 프로세스의 일부로 고려해야 할 몇 가지 사항입니다.

* 초기 클러스터에 대 한 노드 형식 수 
* 각 노드 유형의 (크기, 인스턴스 수, 기본, 인터넷 연결, Vm 등의 수)의 속성
* 클러스터의 안정성 및 지속성 특성

### <a name="select-the-initial-number-of-node-types"></a>초기 노드 형식 수를 선택 합니다.
먼저, 만드는 클러스터를 어디에 사용할지 결정해야 합니다. 이 클러스터에 어떤 종류의 애플리케이션을 배포할 계획인가요? 애플리케이션이 여러 서비스를 보유하고 해당 서비스 중 일부를 공용 또는 인터넷에 연결해야 하나요? 서비스(애플리케이션을 구성함)에는 더 유용한 RAM 또는 더 높은 CPU 사용률과 같은 서로 다른 인프라가 필요한가요? Service Fabric 클러스터는 둘 이상의 노드 유형의 구성 될 수 있습니다: 주 노드 유형 및 하나 이상의 주가 아닌 노드 형식입니다. 각 노드 유형은 가상 머신 확장 집합에 매핑됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. [노드 속성 및 배치 제약 조건] [ placementconstraints] 특정 노드 형식에 특정 서비스를 제한 하도록 설정할 수 있습니다.  자세한 내용은 [으로 시작 해야 하는 클러스터 노드 형식의 수](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)입니다.

### <a name="select-node-properties-for-each-node-type"></a>각 노드 유형에 대 한 노드 속성을 선택 합니다.
노드 유형에 연결 된 확장 집합의 VM SKU, 번호 및 Vm의 속성을 정의합니다.

각 노드 유형에 대 한 최소 Vm 크기에서 결정 됩니다 합니다 [내구성 계층] [ durability] 노드 형식을 선택 합니다.

주 노드 형식에 대 한 최소 Vm 수에서 결정 됩니다 합니다 [안정성 계층] [ reliability] 선택할 수 있습니다.

에 대 한 최소 요구 사항을 참조 하세요 [주 노드 형식](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)를 [주가 아닌 노드 형식에 상태 저장 워크 로드](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), 및 [주가 아닌 노드 형식에 상태 비저장 워크 로드](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)합니다. 

최소 노드 수보다 더 이상이 노드 형식에서 실행 하려는 응용 프로그램/서비스의 복제본 수를 기반으로 해야 합니다.  [Service Fabric 응용 프로그램의 용량 계획](service-fabric-capacity-planning.md) 응용 프로그램을 실행 하는 데 필요한 리소스를 예측 하는 데 도움이 됩니다. 항상 클러스터를 강화 하거나 나중에 응용 프로그램 워크 로드 변경에 맞게 축소 수 있습니다. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>클러스터의 내구성 및 안정성 수준을 선택 합니다.
지속성 계층은 기본 Azure 인프라와 함께 VM에 있는 권한을 이 시스템에 표시하는 데 사용됩니다. 주 노드 유형에서 이 권한을 사용하면 Service Fabric이 시스템 서비스 및 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(VM 다시 부팅, VM 이미지로 다시 설치, 또는 VM 마이그레이션 등)을 일시 중지할 수 있습니다. 주가 아닌 노드 형식에서 이 권한을 사용하면 Service Fabric이 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(예: VM 다시 부팅, VM 이미지로 다시 설치, VM 마이그레이션)을 일시 중지할 수 있습니다.  서로 다른 수준과 권장 사항을 사용 하 고 볼 때, 어떤 수준에서의 이점에 대 한 [클러스터의 내구성 특성][durability]합니다.

안정성 계층은 주 노드 유형에서 이 클러스터에서 실행하려는 시스템 서비스의 복제본 수를 설정하는 데 사용됩니다. 복제본 수가 많을수록 클러스터에서 시스템 서비스를 신뢰할 수 있습니다.  서로 다른 수준과 권장 사항을 사용 하 고 볼 때, 어떤 수준에서의 이점에 대 한 [클러스터의 안정성 특성][reliability]합니다. 

## <a name="enable-reverse-proxy-andor-dns"></a>역방향 프록시 및/또는 DNS를 사용 하도록 설정
클러스터의 노드는 동일한 로컬 네트워크에 있으므로 클러스터 내에서 서로 연결하고 있는 서비스는 다른 서비스의 엔드포인트에 직접 액세스할 수 있습니다. Service Fabric 서비스 간의 연결을 쉽게 추가 서비스를 제공 합니다. A [DNS 서비스](service-fabric-dnsservice.md) 와 [역방향 프록시 서비스](service-fabric-reverseproxy.md)합니다.  클러스터를 배포할 때 두 서비스를 사용할 수 있습니다.

서비스가 많기 때문에 특히 컨테이너 화 된 서비스는 기존 URL 이름을 가질 수 있습니다는 표준 DNS를 사용 하 여이 해결 하려면 수 있는 프로토콜 (명명 서비스 프로토콜 대신) 응용 프로그램 "리프트 앤 시프트" 시나리오에서 특히 편리 합니다. 바로 이것이 DNS 서비스의 용도입니다. DNS 서비스를 통해 DNS 이름을 서비스 이름에 매핑할 수 있으므로 엔드포인트 IP 주소를 확인할 수 있습니다.

역방향 프록시는 HTTP 끝점 (HTTPS)을 노출 하는 클러스터의 서비스를 해결 합니다. 역방향 프록시는 특정 URI 형식을 제공 하 여 다른 서비스 호출을 크게 간소화 합니다.  역방향 프록시는 또한 해결을 처리 하 고, 연결 및 통신할 다른 하나의 서비스에 필요한 단계를 다시 시도 하세요.

## <a name="prepare-for-disaster-recovery"></a>재해 복구 준비
고가용성 전달의 중요한 부분은 서비스가 다른 모든 유형의 오류를 감당할 수 있는지 확인하는 것입니다. 계획되지 않으며 사용자 통제 하에 있지 않은 오류의 경우 특히 이러한 과정이 중요합니다. [재해 복구 준비](service-fabric-disaster-recovery.md) 없습니다 올바르게 모델링 및 관리 하는 경우 재해가 될 수 있는 몇 가지 일반적인 오류 모드에 설명 합니다. 완화 방법 및 재해가 발생할 경우 수행할 작업을 설명 합니다.

## <a name="production-readiness-checklist"></a>프로덕션 준비 검사 목록
애플리케이션 및 클러스터가 프로덕션 트래픽을 허용할 준비가 되었나요? 통해 클러스터를 프로덕션에 배포 하기 전에 실행 합니다 [프로덕션 준비 검사 목록](service-fabric-production-readiness-checklist.md)합니다. 응용 프로그램 및이 검사 목록의 항목을 진행 하 여 원활 하 게 실행 하는 클러스터를 유지 합니다. 프로덕션으로 전환 하기 전에 검사할 이러한 모든 항목을 권장 합니다.

## <a name="next-steps"></a>다음 단계
* [Windows를 실행 하는 Service Fabric 클러스터 만들기](service-fabric-best-practices-overview.md)
* [Linux를 실행 하는 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster