---
title: Service Fabric 관리형 클러스터(미리 보기)
description: Service Fabric 관리형 클러스터는 배포 및 클러스터 관리 환경을 간소화하는 Azure Service Fabric 클러스터 리소스 모델의 진화입니다.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410400"
---
# <a name="service-fabric-managed-clusters-preview"></a>Service Fabric 관리형 클러스터(미리 보기)

Service Fabric 관리형 클러스터는 배포 및 클러스터 관리 환경을 간소화하는 Azure Service Fabric 클러스터 리소스 모델의 진화입니다.

기존 Service Fabric 클러스터에 대한 ARM(Azure Resource Model) 템플릿은 클러스터와 서비스가 제대로 작동하기 위해 여러 지원 리소스를 함께 사용하여 클러스터 리소스를 정의해야 합니다. 이 모든 구성 요소는 배포 시 클러스터 수명 주기 전체에 걸쳐 올바르게 “연결”되어야 합니다. 이와 대조적으로 Service Fabric 관리형 클러스터의 캡슐화 모델은 단일 *Service Fabric 관리형 클러스터* 리소스로 구성됩니다. 클러스터에 대한 모든 기본 리소스는 사용자를 대신하여 Azure에서 추상화되고 관리됩니다.

**Service Fabric 기존 클러스터 모델**
![Service Fabric 기존 클러스터 모델][sf-composition]

**Service Fabric 관리형 클러스터 모델**
![Service Fabric 캡슐화된 클러스터 모델][sf-encapsulation]

크기와 복잡성 측면에서 Service Fabric 관리형 클러스터에 대한 ARM 템플릿은 일반적인 Service Fabric 클러스터를 정의하는 데 필요한 1000줄 대비 약 100줄의 JSON입니다.

| Service Fabric 리소스 | Service Fabric 관리형 클러스터 리소스 |
|----------|-----------|
| Service Fabric 클러스터 | Service Fabric 관리형 클러스터 |
| 가상 머신 확장 집합 | |
| 부하 분산 장치 | |
| 공용 IP 주소 | |
| 스토리지 계정 | |
| 가상 네트워크 | |

Service Fabric 관리형 클러스터는 기존 클러스터에 비해 여러 가지 이점을 제공합니다.

**간소화된 클러스터 배포 및 관리**
- 단일 Azure 리소스 배포 및 관리
- 인증서 관리 및 자동 순환
- 간소화된 크기 조정 작업

**작업 오류 방지**
- 기본 리소스와의 구성 불일치 방지
- 안전하지 않은 작업의 차단(예: 시드 노드 삭제)

**기본 모범 사례**
- 단순화된 안정성 및 내구성 설정

클러스터에 필요한 기본 리소스의 비용 외에 Service Fabric 관리형 클러스터에 대한 추가 비용은 없습니다.

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric 관리형 클러스터 SKU

Service Fabric 관리형 클러스터는 기본 및 표준 SKU에서 모두 사용할 수 있습니다.

| 기능 | Basic | Standard |
| ------- | ----- | -------- |
| 네트워크 리소스([Load Balancer](../load-balancer/skus.md), [공용 IP](../virtual-network/public-ip-addresses.md)용 SKU) | Basic | Standard |
| 최소 노드(VM 인스턴스) 수 | 3 | 5 |
| 노드 유형별 최대 노드 수 | 100 | 100 |
| 최대 노드 유형 개수 | 1 | 20 |
| 노드 형식 추가/제거 | 예 | 예 |
| 영역 중복 | 예 | 예 |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Service Fabric 관리형 클러스터 기능 로드맵
이는 Service Fabric 관리형 클러스터의 초기 미리 보기 버전이므로 알아야 할 몇 가지 차이가 있습니다. 이러한 기능은 이후 릴리스에서 제공될 예정입니다. 

* Visual Studio에서 직접 클러스터에 애플리케이션 게시
* 관리 ID 
* ARM 애플리케이션 배포 
* 가용성 영역 
* 역방향 프록시 
* 자동 크기 조정 
* NSG 규칙 업데이트 
* 자동 OS 업그레이드

## <a name="next-steps"></a>다음 단계

Service Fabric 관리형 클러스터를 시작하려면 빠른 시작:

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 만들기(미리 보기)](quickstart-managed-cluster-template.md)를 사용해 보세요.


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png