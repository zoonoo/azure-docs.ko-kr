---
title: CloudSimple 유지 관리 및 업데이트
titleSuffix: Azure VMware Solution by CloudSimple
description: 예약된 유지 관리 및 업데이트에 대한 CloudSimple 서비스 프로세스를 설명합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 03/09/2021
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1cbd708f6c1f5159655099d5d72900ab0c608147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522311"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple 유지 관리 및 업데이트

프라이빗 클라우드 환경은 단일 장애점이 없도록 설계되었습니다.

* ESXi 클러스터는 vSphere HA(고가용성)를 사용하여 구성됩니다. 클러스터는 복원력을 위해 하나 이상의 예비 노드를 갖도록 크기가 지정됩니다.
* 중복된 기본 스토리지는 단일 오류로부터 보호하기 위해 3개 이상의 노드가 필요한 vSAN에 의해 제공됩니다. 대규모 클러스터에 더 높은 복원력을 제공하도록 vSAN을 구성할 수 있습니다.
* RAID-10 스토리지를 통해 vCenter, PSC 및 NSX Manager VM을 구성하여 스토리지 장애를 방지합니다. VM은 vSphere HA에 의한 노드/네트워크 장애로부터 보호됩니다.
* ESXi 호스트에는 중복 팬 및 NIC가 있습니다.
* TOR 및 스파인 스위치는 HA 쌍으로 구성되어 복원력을 제공합니다.

CloudSimple은 작동 시간 및 가용성을 위해 다음 VM을 지속적으로 모니터링하고 가용성 SLA를 제공합니다.

* ESXi 호스트
* vCenter
* PSC
* NSX Manager

CloudSimple은 다음 장애를 지속적으로 모니터링합니다.

* 하드 디스크
* 실제 NIC 포트
* 서버
* 팬
* 고급
* 스위치
* 스위치 포트

디스크 또는 노드에 장애가 발생하면 영향을 받는 VMware 클러스터에 새 노드가 자동으로 추가되어 즉시 상태로 돌아갑니다.

CloudSimple은 프라이빗 클라우드에서 이러한 VMware 요소를 백업, 유지 관리 및 업데이트합니다.

* ESXi
* vCenter 플랫폼 서비스
* 컨트롤러
* vSAN
* NSX

## <a name="back-up-and-restore"></a>백업 및 복원

CloudSimple 백업에는 다음이 포함됩니다.

* vCenter, PSC 및 DVS 규칙의 야간 증분 백업
* 애플리케이션 계층의 구성 요소를 백업하기 위한 vCenter 네이티브 API
* VMware 관리 소프트웨어를 업데이트하거나 업그레이드하기 전의 자동 백업
* TLS1.2 암호화된 채널을 통해 Azure로 데이터를 전송하기 전에 원본에서 vCenter 데이터 암호화. 데이터는 지역 간에 복제되는 Azure blob에 저장됩니다.

[지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)를 열어 복원을 요청할 수 있습니다.

## <a name="maintenance"></a>유지 관리

CloudSimple은 여러 유형의 계획된 유지 관리를 수행합니다.

### <a name="backendinternal-maintenance"></a>백엔드/내부 유지 관리

이러한 유지 관리 작업에는 일반적으로 물리적 자산을 다시 구성하거나 소프트웨어 패치를 설치해야 합니다. 이는 서비스 중인 자산의 일반적인 소비에는 영향을 주지 않습니다. 중복 NIC가 각 물리적 랙으로 이동하는 경우, 정상 네트워크 트래픽과 프라이빗 클라우드 작업에 영향을 주지 않습니다. 유지 관리 간격 동안 조직에서 전체 중복 대역폭을 사용해야 하는 경우에만 성능에 영향을 줄 수 있습니다.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple 포털 유지 관리

CloudSimple 제어 평면이나 인프라를 업데이트하는 경우 몇 가지 제한된 서비스 가동 중지 시간이 필요합니다. 현재 유지 관리 간격은 한 달에 한 번 발생할 수 있습니다. 빈도는 시간이 지남에 따라 감소할 것으로 예상됩니다. CloudSimple은 포털 유지 관리에 대한 알림을 제공하고 간격을 가능한 한 짧게 유지합니다. 포털 유지 관리 간격 중에 다음 서비스는 영향 없이 계속 작동합니다.

* VMware 관리 평면 및 애플리케이션
* vCenter 액세스
* 모든 네트워킹 및 스토리지
* 모든 Azure 트래픽

### <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라의 구성을 변경해야 하는 경우가 있습니다.  현재는 1-2개월마다 이러한 간격이 발생할 수 있지만 시간이 지남에 따라 빈도가 감소할 것으로 예상됩니다. 이러한 유형의 유지 관리는 일반적으로 CloudSimple 서비스의 정상적인 사용을 중단하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 중에 다음 서비스는 영향 없이 계속 작동합니다.

* VMware 관리 평면 및 애플리케이션
* vCenter 액세스
* 모든 네트워킹 및 스토리지
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple은 프라이빗 클라우드에서 VMware 소프트웨어(ESXi, vCenter, PSC 및 NSX)의 수명 주기 관리를 담당합니다.

소프트웨어 업데이트에는 다음이 포함됩니다.

* **패치**. VMware에서 출시한 보안 패치 또는 버그 수정 사항
* **업데이트**. VMware 스택 구성 요소의 부 버전 변경
* **업그레이드**. VMware 스택 구성 요소의 주 버전 변경

CloudSimple은 VMware로부터 제공되는 대로 중요 보안 패치를 테스트합니다. 

다음에 예약된 업데이트가 배포될 때까지 해당 패치를 설치하는 대신 문서화된 VMware 해결 방법이 구현됩니다. 

## <a name="next-steps"></a>다음 단계

[Veeam을 사용하여 워크로드 VM 백업](backup-workloads-veeam.md)
