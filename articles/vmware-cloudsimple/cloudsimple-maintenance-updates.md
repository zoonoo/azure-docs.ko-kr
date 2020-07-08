---
title: CloudSimple 유지 관리 및 업데이트
titleSuffix: Azure VMware Solution by CloudSimple
description: 예약 된 유지 관리 및 업데이트에 대 한 CloudSimple 서비스 프로세스를 설명 합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77025030"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple 유지 관리 및 업데이트

사설 클라우드 환경은 단일 실패 지점이 없도록 설계 되었습니다.

* ESXi 클러스터는 vSphere HA (고가용성)를 사용 하 여 구성 됩니다. 클러스터는 복원 력을 위해 하나 이상의 예비 노드를 갖도록 크기가 지정 됩니다.
* 중복 된 기본 저장소는 단일 오류에 대 한 보호를 제공 하기 위해 3 개 이상의 노드가 필요한 vSAN에 의해 제공 됩니다. 대량 클러스터에 대해 더 높은 복원 력을 제공 하도록 vSAN을 구성할 수 있습니다.
* vCenter, PSC 및 NSX Manager Vm은 저장소 오류 로부터 보호 하기 위해 RAID 10 저장소로 구성 됩니다. Vm은 vSphere HA에의 한 노드/네트워크 오류 로부터 보호 됩니다.
* ESXi 호스트에는 중복 팬 및 Nic가 있습니다.
* 및 스파인 스위치는 HA 쌍으로 구성 되어 복원 력을 제공 합니다.

CloudSimple은 작동 시간 및 가용성을 위해 다음 Vm을 지속적으로 모니터링 하 고 가용성 Sla를 제공 합니다.

* ESXi 호스트
* vCenter
* PSC
* NSX 관리자

CloudSimple은 다음 오류를 지속적으로 모니터링 합니다.

* 하드 디스크
* 실제 NIC 포트
* 서버
* 팬
* 고급
* 스위치
* 스위치 포트

디스크 또는 노드에 오류가 발생 하면 영향을 받는 VMware 클러스터에 새 노드가 자동으로 추가 되어 상태를 즉시 다시 가져옵니다.

CloudSimple은 사설 클라우드에서 이러한 VMware 요소를 백업, 유지 관리 및 업데이트 합니다.

* ESXi
* vCenter 플랫폼 서비스
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>백업 및 복원

CloudSimple 백업에는 다음이 포함 됩니다.

* VCenter, PSC 및 DVS 규칙의 야간 증분 백업
* 응용 프로그램 계층의 구성 요소를 백업 하기 위한 vCenter 네이티브 Api
* VMware 관리 소프트웨어를 업데이트 하거나 업그레이드 하기 전의 자동 백업
* TLS 1.2 암호화 된 채널을 통해 Azure로 데이터를 전송 하기 전에 원본에서 vCenter 데이터 암호화 데이터는 지역 간에 복제 되는 Azure blob에 저장 됩니다.

[지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)를 열어 복원을 요청할 수 있습니다.

## <a name="maintenance"></a>유지 관리

CloudSimple은 여러 유형의 계획 된 유지 관리를 수행 합니다.

### <a name="backendinternal-maintenance"></a>백 엔드/내부 유지 관리

이러한 유지 관리 작업에는 일반적으로 물리적 자산을 다시 구성 하거나 소프트웨어 패치를 설치 해야 합니다. 서비스 중인 자산의 일반적인 소비에는 영향을 주지 않습니다. 중복 Nic를 사용 하는 경우, 일반 네트워크 트래픽 및 사설 클라우드 작업이 영향을 받지 않습니다. 유지 관리 간격 동안 조직에서 전체 중복 대역폭을 사용 해야 하는 경우에만 성능에 영향을 줄 수 있습니다.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple 포털 유지 관리

CloudSimple 제어 평면이 나 인프라를 업데이트 하는 경우 몇 가지 제한 된 서비스 가동 중지 시간이 필요 합니다. 현재는 유지 관리 간격을 한 달에 한 번만 자주 사용할 수 있습니다. 빈도는 시간이 지남에 따라 거부 될 것으로 예상 됩니다. CloudSimple은 포털 유지 관리에 대 한 알림을 제공 하 고 가능한 한 짧게 유지 합니다. 포털 유지 관리 간격 중에 다음 서비스는 영향 없이 계속 작동 합니다.

* VMware management 평면 및 응용 프로그램
* vCenter 액세스
* 모든 네트워킹 및 저장소
* 모든 Azure 트래픽

### <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라의 구성을 변경 해야 하는 경우가 있습니다.  현재는 1-2 개월 마다 이러한 간격이 발생할 수 있지만 시간이 지남에 따라 빈도가 저하 될 것으로 예상 됩니다. 이러한 유형의 유지 관리는 일반적으로 CloudSimple 서비스의 정상적인 사용을 중단 하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 동안 다음 서비스는 영향 없이 계속 작동 합니다.

* VMware management 평면 및 응용 프로그램
* vCenter 액세스
* 모든 네트워킹 및 저장소
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple은 사설 클라우드에서 VMware 소프트웨어 (ESXi, vCenter, PSC 및 NSX)의 수명 주기 관리를 담당 합니다.

소프트웨어 업데이트는 다음과 같습니다.

* **패치**. VMware에서 릴리스된 보안 패치 또는 버그 수정
* **업데이트**. VMware stack 구성 요소의 부 버전 변경입니다.
* **업그레이드**. VMware stack 구성 요소의 주 버전 변경

CloudSimple은 VMware에서 사용할 수 있게 되는 즉시 중요 한 보안 패치를 테스트 합니다. SLA 당 CloudSimple은 1 주일 내 사설 클라우드 환경에 대 한 보안 패치를 롤업 합니다.

CloudSimple은 VMware 소프트웨어 구성 요소에 분기별 유지 관리 업데이트를 제공 합니다. 새 주 버전의 VMware 소프트웨어를 사용할 수 있는 경우 CloudSimple은 고객과 연동 하 여 업그레이드를 위한 적절 한 유지 관리 기간을 조정 합니다.

## <a name="next-steps"></a>다음 단계

[Veeam을 사용 하 여 워크 로드 Vm 백업](backup-workloads-veeam.md)
