---
title: CloudSimple-CloudSimple 유지 관리 및 업데이트 하 여 azure의 VMware 솔루션
description: 예약 된 유지 관리 및 업데이트에 대 한 CloudSimple 서비스 프로세스를 설명합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160247"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple 유지 관리 및 업데이트

사설 클라우드 환경의 단일 실패 지점이 없게 하도록 설계 되었습니다.

* ESXi 클러스터 vSphere 고가용성으로 구성 됩니다. 클러스터 복원 력 위한 여분의 노드가 하나 이상 있어야 크기가 조정 됩니다.
* 중복 기본 저장소는 단일 오류에 대 한 보호를 제공 하는 노드가 3 개 이상 필요한 vSAN에서 제공 됩니다. 대규모 클러스터에 대 한 더 높은 복원 력을 제공 하 vSAN은 구성할 수 있습니다.
* vCenter 고 PSC, NSX Manager Vm 저장소 오류 로부터 보호 하기 위해 RAID 10 저장소 정책을 사용 하 여 구성 됩니다. Vm은 vSphere HA로 노드/네트워크 오류 로부터 보호 됩니다.
* ESXi 호스트에 중복 팬 및 nic가 있습니다.
* TOR 및 편 스위치 HA 복원 력을 제공 하는 쌍으로 구성 됩니다.

CloudSimple는 지속적으로 가동 시간 및 가용성에 대 한 다음 Vm을 모니터링 하 고 가용성 Sla를 제공 합니다.

* ESXi 호스트
* vCenter
* PSC
* NSX 관리자

또한 CloudSimple 오류에 대 한 지속적으로 다음을 모니터링합니다.

* 하드 디스크
* 물리적 NIC 포트
* 서버
* 팬
* Power
* 스위치
* 스위치 포트

디스크 또는 노드 실패 하면 새 노드를 즉시 포커스 상태를 다시 영향을 받는 VMware 클러스터에 자동으로 추가 됩니다.

CloudSimple 백업 하 고 유지 관리 및 사설 클라우드에 이러한 VMware 요소를 업데이트 합니다.

* ESXi
* vCenter 플랫폼 서비스
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>백업 및 복원

CloudSimple 백업에 포함 됩니다.

* 야간 증분 백업 vCenter, PSC 및 DVS 규칙입니다.
* VCenter의 사용 하 여 네이티브 Api를 응용 프로그램 계층 구성 요소를 백업 합니다.
* 모든 업데이트 하거나 VMware 관리 소프트웨어를 업그레이드 하기 전에 자동 백업 합니다.
* Azure에 TLS1.2 암호화 된 채널을 통해 데이터를 전송 하기 전에 vCenter에서 소스에서 데이터 암호화. 데이터는 지역 간에 복제 되는 위치를 Azure blob에 저장 됩니다.

열어 복원을 요청 수를 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)합니다.

## <a name="maintenance"></a>유지 관리

CloudSimple 여러 종류의 계획 된 유지 관리를 수행합니다.

### <a name="backendinternal-maintenance"></a>백 엔드/내부 유지 관리

이 유지 관리에는 일반적 물리적 자산을 다시 구성 하거나 소프트웨어 패치를 설치 합니다. 서비스 되는 데 사용 되는 자산의 일반 소비를 반영 되지 않습니다. 각 물리적 랙에 하려는 중복 Nic를 사용 하 여 일반적인 네트워크 트래픽 및 사설 클라우드 작업 영향을 받지 않습니다. 조직에서 유지 관리 간격의 전체 중복 대역폭을 사용 하는 경우에 성능에 영향을 확인할 수 있습니다.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple 포털 유지 관리

CloudSimple 제어 평면 또는 인프라 업데이트 될 때 제한 된 서비스 가동 중지 시간이 필요 합니다. 현재 유지 관리 간격으로 매월 한 번으로 수 있습니다. 빈도 시간이 지남에 따라 거부 해야 합니다. CloudSimple 포털 유지 관리에 대 한 알림을 제공 하 고 가능한 한 짧게 간격을 유지 합니다. 포털 유지 관리 동안 다음 서비스에는 영향을 주지 않고 함수 계속 합니다.

* VMware 관리 평면 및 응용 프로그램
* vCenter 액세스
* 모든 네트워킹 및 저장소
* 모든 Azure 트래픽

### <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라의 구성을 변경 해야 하는 경우도 있습니다.  현재 이러한 간격에는 1 ~ 2 개월 마다 발생할 수 있지만 빈도 시간이 지남에 따라 거부 해야 합니다. 이 유형의 유지 관리는 일반적으로 일반 CloudSimple 서비스 사용을 중단 하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 중 다음 서비스에는 영향을 주지 않고 함수 계속 합니다.

* VMware 관리 평면 및 응용 프로그램
* vCenter 액세스
* 모든 네트워킹 및 저장소
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple는 VMware 소프트웨어 (ESXi, vCenter, PSC 및 NSX) 사설 클라우드에서 수명 주기 관리 담당합니다.

소프트웨어 업데이트는 다음과 같습니다.

* **패치**합니다. 보안 패치 또는 VMware에서 릴리스 버그 수정입니다.
* **업데이트**. VMware 스택 구성 요소의 변경 부 버전입니다.
* **업그레이드**합니다. VMware 스택 구성 요소의 주요 버전 변경입니다.

VMware에서 사용할 수는 즉시 CloudSimple 중요 보안 패치를 테스트 합니다. SLA를 당 CloudSimple 켜져 보안 패치를 사설 클라우드 환경에 1 주일 이내입니다.

CloudSimple는 VMware 소프트웨어 구성 요소에 대 한 분기별 유지 관리 업데이트를 제공합니다. VMware 소프트웨어의 새 주요 버전을 사용할 수 있는 경우 업그레이드에 대 한 적절 한 유지 관리 기간을 조정 하기 위해 고객과 CloudSimple 작동 합니다.

## <a name="next-steps"></a>다음 단계

[Veeam을 사용 하 여 워크 로드 Vm 백업](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)합니다.