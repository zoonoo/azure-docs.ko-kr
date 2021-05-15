---
title: CloudSimple의 Azure VMware 솔루션 - 프라이빗 클라우드
description: 완전한 VMware 작업 연속성, 기존 도구, 기술 및 프로세스와의 호환성을 비롯한 CloudSimple의 개념 및 장점에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140720"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple 프라이빗 클라우드 개요

CloudSimple은 VMware 워크로드를 몇 분 만에 퍼블릭 클라우드로 변환하고 확장합니다. CloudSimple 서비스를 사용하면 Azure 운영 체제 미설치 인프라에 VMware를 기본적으로 배포할 수 있습니다. 배포는 Azure 위치에 있으며 나머지 Azure 클라우드와 완전히 통합됩니다.

CloudSimple 솔루션은 완전한 VMware 작업 연속성을 제공합니다. 이 솔루션은 다음의 퍼블릭 클라우드 혜택을 제공합니다.

* 탄력성
* 혁신
* 효율성

CloudSimple을 사용하면 총 소유 비용을 낮추는 클라우드 소비 모델을 활용할 수 있습니다. 또한 주문형 프로비전, 사용량 기준 지불 방식 및 용량 최적화 기능을 제공합니다.

CloudSimple은 다음과 완전히 호환됩니다.

* 기존 도구
* 기술
* 프로세스

이러한 호환성을 통해 팀은 다음과 같은 유형의 정책을 중단하지 않고 Azure 클라우드에서 워크로드를 관리할 수 있습니다.

* 네트워크
* 보안  
* 데이터 보호  
* 감사

CloudSimple은 인프라와 모든 필요한 네트워킹 및 관리 서비스를 관리합니다. CloudSimple 서비스를 통해 팀은 다음 사항에 집중할 수 있습니다.

* 비즈니스 가치
* 애플리케이션 프로비저닝
* 비즈니스 연속성
* 지원
* 정책 적용

## <a name="private-cloud-environment-overview"></a>프라이빗 클라우드 환경 개요

프라이빗 클라우드는 다음을 지원하는 격리된 VMware 스택입니다.

* ESXi 호스트
* vCenter
* vSAN
* NSX

프라이빗 클라우드는 CloudSimple 포털을 통해 관리됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다.

스택은 다음에서 실행됩니다.

* 전용 노드
* 격리된 운영 체제 미설치 하드웨어 노드

사용자는 다음과 같은 기본 VMware 도구를 통해 스택을 사용합니다.

* vCenter
* NSX 관리자

Azure 위치에 전용 노드를 배포할 수 있습니다. 그 다음 Azure 및 CloudSimple을 통해 관리할 수 있습니다. 프라이빗 클라우드는 하나 이상의 vSphere 클러스터로 구성되며 각 클러스터에는 3~16 개의 노드가 포함됩니다.

구매한, 사용량 기준 지불 노드 또는 예약된 전용 노드를 통해 프라이빗 클라우드를 생성할 수 있습니다.

다음 연결을 통해 프라이빗 클라우드를 온-프레미스 환경 및 Azure 네트워크에 연결할 수 있습니다.

* 보안
* 프라이빗 VPN
* Azure ExpressRoute

프라이빗 클라우드 환경은 단일 장애점을 제거하도록 설계되었습니다.

* ESXi 클러스터는 vSphere 고가용성(HA)으로 구성되며 복원력을 위한 하나 이상의 예비 노드를 갖도록 크기가 지정됩니다.
* vSAN은 기본 스토리지를 중복으로 제공합니다. vSan는 단일 장애를 방지하기 위해 3개 이상의 노드가 필요합니다. 대규모 클러스터에 더 높은 복원력을 제공하도록 vSAN을 구성할 수 있습니다.
* RAID-10 스토리지 정책을 통해 vCenter, PSC 및 NSX Manager VM을 구성하여 스토리지 장애를 방지할 수 있습니다. vSphere HA는 노드 및 네트워크 장애를 방지합니다.

## <a name="scenarios-for-deploying-a-private-cloud"></a>프라이빗 클라우드 배포 시나리오

프라이빗 클라우드 배포에 대한 몇 가지 사용 사례는 다음과 같습니다.

### <a name="data-center-retirement-or-migration"></a>데이터 센터 사용 중지 또는 마이그레이션

* 기존 데이터 센터 제한에 도달하거나 하드웨어를 새로 고치는 경우 추가 용량을 확보합니다.
* 클라우드에 필요한 용량을 추가하고 하드웨어 새로 고침 관리 문제를 해결합니다.
* 시간이 많이 소요되는 변환 또는 리아키텍처에 비해 클라우드 마이그레이션의 위험 및 비용을 줄입니다.
* 익숙한 VMware 도구와 기술을 통해 클라우드 마이그레이션을 가속화합니다. 클라우드에서 Azure 서비스를 통해 원하는 속도로 애플리케이션을 현대화합니다.

### <a name="expand-on-demand"></a>주문형 확장

* 클라우드 확장을 통해 새로운 개발 환경 또는 계절별 용량 급증과 같은 예기치 않은 요구 사항을 충족합니다.
* 필요에 따라 새로운 용량을 생성하고 필요한 만큼 유지합니다.
* 온-프레미스와 클라우드에서 동일한 아키텍처 및 정책을 통해 초기 투자를 줄이고 프로비전 속도를 가속화하며 복잡성을 줄입니다.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 클라우드의 재해 복구 및 가상 데스크톱

* Azure 클라우드에서 데이터, 앱 및 데스크톱에 대한 원격 액세스를 설정합니다. 고대역폭 연결을 통해 데이터를 신속하게 업로드/다운로드하여 인시던트를 복구합니다. 저지연 네트워크는 사용자가 데스크톱 앱에서 요구하는 빠른 응답 시간을 제공합니다.

* CloudSimple 포털 및 친숙한 VMware 도구를 사용하여 클라우드의 모든 정책과 네트워킹을 복제합니다. 복제는 DR 및 VDI 구현 생성과 관리의 노력 및 위험을 줄여줍니다.

### <a name="high-performance-applications-and-databases"></a>고성능 애플리케이션 및 데이터베이스

* CloudSimple이 제공하는 하이퍼 컨버지드 아키텍처를 통해 가장 어려운 워크로드를 실행합니다.
* Oracle, Microsoft SQL server, 미들웨어 시스템 및 고성능의 SQL 데이터베이스를 실행합니다.
* 고속 25gbps 네트워크 연결을 통해 클라우드를 자신만의 데이터 센터로 경험합니다. 고속 연결을 통해 성능 저하 없이 온-프레미스, Azure의 VMware 및 Azure 프라이빗 워크로드 같은 하이브리드 앱을 실행할 수 있습니다.

### <a name="true-hybrid"></a>진정한 하이브리드

* VMware와 Azure 서비스의 DevOps를 통합합니다.
* 모든 워크로드에서 적용할 수 있는 Azure 서비스 및 솔루션에 대한 VMware 관리를 최적화합니다.
* 데이터 센터의 확장 또는 애플리케이션의 리아키텍처링 없이 퍼블릭 클라우드 서비스에 액세스할 수 있습니다.
* Azure의 VMware 애플리케이션에 대한 ID, 액세스 제어 정책, 로깅 및 모니터링을 중앙 집중화합니다.

## <a name="limits"></a>제한

다음 테이블에 프라이빗 클라우드의 리소스에 대한 노드 제한이 표시됩니다.

| 리소스 | 제한 |
|----------|-------|
| 프라이빗 클라우드를 생성을 위한 최소 노드 수 | 3 |
| 프라이빗 클라우드 클러스터의 최대 노드 수 | 16 |
| 프라이빗 클라우드의 최대 노드 수 | 64 |
| 새로운 클러스터의 최소 노드 수 | 3 |

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 생성](create-private-cloud.md) 방법 알아보기
* [프라이빗 클라우드 환경 구성](quickstart-create-private-cloud.md) 방법 알아보기
