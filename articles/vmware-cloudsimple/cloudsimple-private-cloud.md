---
title: CloudSimple-Azure에서 VMware 솔루션에서 사설 클라우드
description: CloudSimple 사설 클라우드 및 개념에 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dc07b4eea553e6cb3d9b522826e860ddbfbc1513
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577047"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple 사설 클라우드 개요

CloudSimple 변환 하 고 몇 분 안에 공용 클라우드에 VMware 워크 로드를 확장 합니다. CloudSimple 서비스를 사용 하 여 배포할 수 있습니다 VMware 기본적으로 Azure 운영 체제 미 설치 인프라에서. 배포는 Azure 위치에 있으며 Azure 클라우드 rest와 완전히 통합 됩니다.

* CloudSimple 솔루션 전체 VMware 운영 연속성을 제공 합니다. 이 솔루션의 공용 클라우드 이점을 제공합니다.
  * 탄력성
  * 혁신
  * 효율성
* CloudSimple를 사용 하 여 총 소유 비용을 줄이고 클라우드 소비 모델에서 이점이 있습니다. 용량 최적화, 주문형 프로비저닝, 지불으로-성장에 제공합니다.
* CloudSimple 완벽 하 게 호환 됩니다.
  * 기존 도구
  * 기술
  * 프로세스
* 이 호환성 정책을 방해 하지 않고 Azure 클라우드에서 워크 로드를 관리 팀을 수 있습니다.
  * 네트워크
  * 보안  
  * 데이터 보호  
  * 감사
* CloudSimple 인프라는 필요한 모든 네트워킹 및 관리 서비스를 관리 합니다. CloudSimple 서비스 팀을에 집중할 수 있습니다.
  * 비즈니스 가치
  * 응용 프로그램 프로 비전
  * 비즈니스 연속성
  * 지원
  * 정책 적용

## <a name="private-cloud-environment-overview"></a>사설 클라우드 환경 개요

사설 클라우드는 이러한 환경과 같이 격리 하는 VMware 스택을:

* ESXi 호스트
* vCenter
* vSAN
* NSX

사설 클라우드는 자체 관리 도메인에서 vCenter 서버에서 관리 됩니다.

스택의에서 실행 됩니다.

* 전용 노드
* 격리 된 운영 체제 미 설치 하드웨어 노드

사용자를 비롯 한 네이티브 VMware 도구를 통해 스택을 사용 합니다.

* vCenter
* NSX 관리자

Azure 위치에서 전용된 노드를 배포할 수 있습니다. 그런 다음 Azure와 CloudSimple 관리할 수 있습니다. 사설 클라우드 하나 이상의 vSphere 클러스터 구성 되며 각 클러스터에 3 ~ 16 노드가 포함 되어 있습니다.

사설 클라우드를 사용 하 여 노드를 구입을 만들 수 있습니다.

* 종 량 제 노드
* 예약 된, 전용 노드

온-프레미스 환경과 같은 연결을 사용 하 여 Azure 네트워크를 사설 클라우드를 연결할 수 있습니다.

* 보안
* 개인 VPN
* Azure ExpressRoute

사설 클라우드 환경의 오류의 단일 지점에서 제거 하도록 설계 됩니다.

* ESXi 클러스터 vSphere 고가용성으로 구성 및 복원 력 위한 여분의 노드가 하나 이상에 맞게 조정 됩니다.
* vSAN 중복 기본 저장소를 제공합니다. vSan에는 단일 오류 로부터 보호 하기 위해 세 개 이상의 노드가 필요 합니다. 대규모 클러스터에 대 한 더 높은 복원 력을 제공 하는 vSAN을 구성할 수 있습니다.
* 저장소 오류 로부터 보호 하기 위해 RAID 10 저장소 정책을 사용 하 여 vCenter 고 PSC, NSX Manager Vm을 구성할 수 있습니다. 그런 다음 노드 및 네트워크 오류에 대 한 HA vSphere에서 보호 된 합니다.

## <a name="scenarios-for-deploying-a-private-cloud"></a>사설 클라우드에 배포 하기 위한 시나리오

* **데이터 센터 사용 중지 또는 마이그레이션**

  * 기존 데이터 센터의 제한에 도달 하거나 하드웨어를 새로 고칠 때 추가 용량을 가져옵니다.
  * 클라우드에서 필요한 용량을 추가 하 고 하드웨어 새로 고침 관리는 골치 아픈 문제를 제거 합니다.
  * 클라우드 마이그레이션에 시간이 오래 걸리는 변환 또는 아키텍처에 비해 비용과 위험을 줄입니다.
  * 익숙한 VMware 도구 및 기술을 사용 하 여 클라우드 마이그레이션을 가속화 하 합니다. 클라우드에서 Azure 서비스 속도로 응용 프로그램 현대화를 사용 합니다.

* **요청 시 확장**

  * 새 개발 환경을 계절 용량 급증 등의 예기치 않은 요구 사항에 맞게 클라우드로 확장 합니다.
  * 필요에 따라 새 용량을 만들고 동안만 필요한 유지 합니다.
  * 사전 투자를 줄이고, 프로 비전의 속도 가속화 온-프레미스 및 클라우드에서 모두 동일한 아키텍처 및 정책을 사용 하 여 복잡성을 줄입니다.

* **재해 복구 및 Azure 클라우드에서 가상 데스크톱**

  * 데이터, 앱 및 Azure cloud에서 데스크톱에 대 한 원격 액세스를 설정 합니다. 높은 대역폭 연결을 사용 하면 업로드 다운로드 / 데이터 보안 문제 로부터 복구 신속 하 게 합니다. 지연율이 낮은 네트워크 지정 응답을 빠른 데스크톱 앱에서 사용자가 필요로 하는 시간입니다.

  * 모든 정책 및 네트워킹 CloudSimple 포털 및 익숙한 VMware 도구를 사용 하 여 클라우드에 복제 합니다. 이 복제 노력 및 만들기 및 관리 DR 및 VDI 구현 될 위험이 줄어듭니다.

* **고성능 응용 프로그램 및 데이터베이스**

  * CloudSimple에서 제공 하는 하이퍼 수렴 형 아키텍처를 사용 하 여 가장 까다로운 워크 로드를 실행 합니다.
  * Oracle, Microsoft SQL server, 미들웨어 시스템 및 성능이 뛰어난 비 SQL 데이터베이스를 실행 합니다.

  * 고속 25-Gbps 네트워크 연결을 사용 하 여 사용자 고유의 데이터 센터와 클라우드를 경험 합니다. 고속 연결을 사용 하면 온-프레미스에서 Azure에 VMware에 걸쳐 있는 하이브리드 앱을 실행 하 고 성능 저하 없이 Azure 개인 작업을 합니다.

* **진정한 하이브리드**

  * VMware 및 Azure 서비스에서 DevOps를 통합 합니다.
  * Azure 서비스 및 모든 작업에 걸쳐 적용할 수 있는 솔루션에 대 한 VMware 관리를 최적화 합니다.
  * 데이터 센터를 확장 하 고 응용 프로그램 아키텍처를 변경 하지 않고도 공용 클라우드 서비스에 액세스 합니다.
  * 액세스 제어 정책, 로깅 및 모니터링 하 고 Azure에서 VMware 응용 프로그램 id를 중앙 집중화 합니다.

## <a name="limits"></a>제한

아래 표는 사설 클라우드의 리소스에 노드 한도 보여줍니다.

| Resource | 제한 |
|----------|-------|
| 사설 클라우드를 만드는 노드의 최소 수 | 3 |
| 사설 클라우드 클러스터에서 노드의 최대 수 | 16 |
| 사설 클라우드에서 노드의 최대 수 | 64 |
| 새 클러스터에서 노드의 최소 수 | 3 |

## <a name="next-steps"></a>다음 단계

* 자세한 방법 [사설 클라우드 만들기](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 자세한 방법 [사설 클라우드 환경 구성](quickstart-create-private-cloud.md)