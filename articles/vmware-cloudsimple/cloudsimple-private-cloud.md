---
title: 클라우드에 의한 Azure VMware 솔루션단순 - 프라이빗 클라우드
description: CloudSimple 프라이빗 클라우드 및 개념에 대해 알아보십시오.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4fb930603455ed1a5df5d357fcab669f41a0c28c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024954"
---
# <a name="cloudsimple-private-cloud-overview"></a>클라우드심플 프라이빗 클라우드 개요

CloudSimple은 몇 분 만에 VMware 워크로드를 공용 클라우드로 변환하고 확장합니다. CloudSimple 서비스를 사용하면 Azure 베어 메탈 인프라에 기본적으로 VMware를 배포할 수 있습니다. 배포는 Azure 위치에 있으며 나머지 Azure 클라우드와 완전히 통합됩니다.

CloudSimple 솔루션은 완벽한 VMware 운영 연속성을 제공합니다. 이 솔루션은 다음과 같은 퍼블릭 클라우드의 이점을 제공합니다.

* 탄력성
* 혁신
* 효율성

CloudSimple을 사용하면 총 소유 비용을 낮추는 클라우드 소비 모델의 이점을 누릴 수 있습니다. 또한 온디맨드 프로비저닝, 성장에 따른 종량제 및 용량 최적화를 제공합니다.

CloudSimple은 다음과 완벽하게 호환됩니다.

* 기존 도구
* 기술
* 프로세스

이러한 호환성을 통해 팀은 다음과 같은 유형의 정책을 중단하지 않고 Azure 클라우드에서 워크로드를 관리할 수 있습니다.

* 네트워크
* 보안  
* 데이터 보호  
* 감사

CloudSimple은 인프라와 필요한 모든 네트워킹 및 관리 서비스를 관리합니다. CloudSimple 서비스를 통해 팀은 다음에 집중할 수 있습니다.

* 비즈니스 가치
* 애플리케이션 프로비저닝
* 비즈니스 연속성
* 고객 지원팀
* 정책 적용

## <a name="private-cloud-environment-overview"></a>프라이빗 클라우드 환경 개요

프라이빗 클라우드는 다음을 지원하는 격리된 VMware 스택입니다.

* ESXi 호스트
* vCenter
* vSAN
* Nsx

프라이빗 클라우드는 CloudSimple 포털을 통해 관리됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다.

스택은 다음에서 실행됩니다.

* 전용 노드
* 절연 베어 메탈 하드웨어 노드

사용자는 네이티브 VMware 도구를 통해 스택을 사용 합니다., 포함 하 여:

* vCenter
* NSX 관리자

Azure 위치에 전용 노드를 배포할 수 있습니다. 그런 다음 Azure 및 CloudSimple을 통해 관리할 수 있습니다. 프라이빗 클라우드는 하나 이상의 vSphere 클러스터로 구성되며 각 클러스터에는 3~16개의 노드가 포함됩니다.

구매한 종량제 노드 또는 예약된 전용 노드를 사용하여 프라이빗 클라우드를 만들 수 있습니다.

다음 연결을 사용하여 사설 클라우드를 온-프레미스 환경 및 Azure 네트워크에 연결할 수 있습니다.

* 보안
* 프라이빗 VPN
* Azure ExpressRoute

프라이빗 클라우드 환경은 단일 장애 지점을 제거하도록 설계되었습니다.

* ESXi 클러스터는 vSphere 고가용성으로 구성되며 복원력을 위해 하나 이상의 예비 노드를 갖도록 크기가 조정됩니다.
* vSAN은 중복 기본 저장소를 제공합니다. vSan은 단일 오류에 대한 보호를 제공하기 위해 세 개 이상의 노드가 필요합니다. 더 큰 클러스터에 대해 더 높은 복원력을 제공하도록 vSAN을 구성할 수 있습니다.
* RAID-10 스토리지 정책으로 vCenter, PSC 및 NSX 관리자 VM을 구성하여 스토리지 오류로부터 보호할 수 있습니다. vSphere HA는 노드 및 네트워크 오류로부터 보호합니다.

## <a name="scenarios-for-deploying-a-private-cloud"></a>프라이빗 클라우드 배포 시나리오

다음은 프라이빗 클라우드 배포에 대한 몇 가지 예시입니다.

### <a name="data-center-retirement-or-migration"></a>데이터 센터 폐기 또는 마이그레이션

* 기존 데이터 센터 또는 하드웨어 새로 고침의 한계에 도달하면 추가 용량을 얻을 수 있습니다.
* 클라우드에 필요한 용량을 추가하고 하드웨어 새로 고침 관리의 번장들을 제거합니다.
* 시간이 많이 소요되는 변환 또는 아키텍처에 비해 클라우드 마이그레이션의 위험과 비용을 줄입니다.
* 익숙한 VMware 도구와 기술을 사용하여 클라우드 마이그레이션을 가속화합니다. 클라우드에서 Azure 서비스를 사용하여 사용자의 속도에 맞춰 응용 프로그램을 현대화합니다.

### <a name="expand-on-demand"></a>온디맨드 확장

* 새로운 개발 환경이나 계절적 용량 버스트와 같은 예기치 않은 요구 사항을 충족하도록 클라우드로 확장합니다.
* 필요에 따라 새 용량을 생성하고 필요한 기간동안만 유지합니다.
* 온-프레미스와 클라우드 모두에서 동일한 아키텍처 및 정책으로 선행 투자를 줄이고, 프로비저닝 속도를 가속화하며, 복잡성을 줄입니다.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 클라우드의 재해 복구 및 가상 데스크톱

* Azure 클라우드에서 데이터, 앱 및 데스크톱에 대한 원격 액세스를 설정합니다. 고대역폭 연결을 사용하면 데이터를 빠르게 업로드/ 다운로드하여 사고로부터 복구할 수 있습니다. 대기 시간이 짧은 네트워크는 사용자가 데스크톱 앱에서 기대하는 빠른 응답 시간을 제공합니다.

* CloudSimple 포털 및 친숙한 VMware 도구를 사용하여 클라우드에서 모든 정책과 네트워킹을 복제합니다. 복제는 DR 및 VDI 구현을 만들고 관리하는 노력과 위험을 줄입니다.

### <a name="high-performance-applications-and-databases"></a>고성능 애플리케이션 및 데이터베이스

* CloudSimple에서 제공하는 하이퍼컨버지드 아키텍처로 가장 까다로운 워크로드를 실행합니다.
* 오라클, Microsoft SQL 서버, 미들웨어 시스템 및 고성능 노SQL 데이터베이스를 실행합니다.
* 고속 25Gbps 네트워크 연결을 통해 클라우드를 자체 데이터 센터로 경험해 보십시오. 고속 연결을 사용하면 성능을 저하시키지 않으면서 온-프레미스, Azure의 VMware 및 Azure 개인 워크로드에 걸쳐 있는 하이브리드 앱을 실행할 수 있습니다.

### <a name="true-hybrid"></a>진정한 하이브리드

* VMware 및 Azure 서비스 간에 DevOps를 통합합니다.
* 모든 워크로드에 적용할 수 있는 Azure 서비스 및 솔루션에 대해 VMware 관리를 최적화합니다.
* 데이터 센터를 확장하거나 애플리케이션을 다시 설계할 필요 없이 퍼블릭 클라우드 서비스에 액세스할 수 있습니다.
* Azure에서 Id를 중앙 집중화하고, 제어 정책에 액세스하고, VMware 응용 프로그램에 대한 로깅 및 모니터링을 합니다.

## <a name="limits"></a>제한

다음 표에는 사설 클라우드의 리소스에 대한 노드 제한이 나와 있습니다.

| 리소스 | 제한 |
|----------|-------|
| 프라이빗 클라우드를 만들 수 있는 최소 노드 수 | 3 |
| 프라이빗 클라우드의 클러스터에 있는 최대 노드 수 | 16 |
| 프라이빗 클라우드의 최대 노드 수 | 64 |
| 새 클러스터의 최소 노드 수 | 3 |

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드를 만드는](create-private-cloud.md) 방법 알아보기
* [프라이빗 클라우드 환경을 구성하는](quickstart-create-private-cloud.md) 방법 알아보기
