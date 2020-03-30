---
title: 클라우드 단순 유지 관리 및 업데이트
titleSuffix: Azure VMware Solution by CloudSimple
description: 예약된 유지 관리 및 업데이트에 대한 CloudSimple 서비스 프로세스에 대해 설명합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025030"
---
# <a name="cloudsimple-maintenance-and-updates"></a>클라우드 단순 유지 관리 및 업데이트

프라이빗 클라우드 환경은 단일 실패 지점이 없음을 위해 설계되었습니다.

* ESXi 클러스터는 vSphere 고가용성(HA)으로 구성됩니다. 클러스터의 크기는 복원력을 위해 하나 이상의 예비 노드를 갖도록 조정됩니다.
* 중복 기본 저장소는 vSAN에서 제공되며, 단일 오류에 대한 보호를 제공하기 위해 세 개 이상의 노드가 필요합니다. vSAN은 더 큰 클러스터에 대해 더 높은 복원력을 제공하도록 구성할 수 있습니다.
* vCenter, PSC 및 NSX 관리자 VM은 저장소 오류로부터 보호하기 위해 RAID-10 스토리지로 구성됩니다. VM은 vSphere HA에 의해 노드/네트워크 오류에 대해 보호됩니다.
* ESXi 호스트에는 중복 팬과 NIC가 있습니다.
* TOR 및 척추 스위치는 복원력을 제공하기 위해 HA 쌍으로 구성됩니다.

CloudSimple은 다음 VM을 지속적으로 모니터링하여 가동 시간 및 가용성을 모니터링하고 가용성 SLA를 제공합니다.

* ESXi 호스트
* vCenter
* Psc
* NSX 관리자

CloudSimple은 또한 다음과 같은 오류가 지속적으로 모니터링합니다.

* 하드 디스크
* 물리적 NIC 포트
* 서버
* 팬 들
* Power
* 스위치
* 스위치 포트

디스크 또는 노드가 실패하면 영향을 받는 VMware 클러스터에 새 노드가 자동으로 추가되어 즉시 상태를 다시 유지합니다.

CloudSimple은 프라이빗 클라우드에서 이러한 VMware 요소를 백업, 유지 관리 및 업데이트합니다.

* Esxi
* v센터 플랫폼 서비스
* 컨트롤러
* vSAN
* Nsx

## <a name="back-up-and-restore"></a>백업 및 복원

CloudSimple 백업에는 다음이 포함됩니다.

* vCenter, PSC 및 DVS 규칙의 야간 증분 백업.
* v응용 프로그램 계층에서 구성 요소를 백업할 수 있는 네이티브 API를 중심으로 합니다.
* VMware 관리 소프트웨어를 업데이트하거나 업그레이드하기 전에 자동으로 백업합니다.
* v데이터 데이터가 TLS1.2 암호화 된 채널을 통해 Azure로 전송되기 전에 원본의 데이터 암호화를 설정합니다. 데이터는 여러 리전간에 복제되는 Azure Blob에 저장됩니다.

[지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)열어 복원을 요청할 수 있습니다.

## <a name="maintenance"></a>유지 관리

CloudSimple은 여러 가지 유형의 계획된 유지 관리를 수행합니다.

### <a name="backendinternal-maintenance"></a>백엔드/내부 유지 보수

이 유지 관리에는 일반적으로 물리적 자산을 다시 구성하거나 소프트웨어 패치를 설치하는 작업이 포함됩니다. 서비스 중인 자산의 정상적인 소비에는 영향을 주지 않습니다. 중복 NIC가 각 물리적 랙으로 이동하면 일반 네트워크 트래픽 및 프라이빗 클라우드 운영에는 영향을 미치지 않습니다. 조직에서 유지 관리 간격 동안 전체 중복 대역폭을 사용할 것으로 예상되는 경우에만 성능에 영향을 미칠 수 있습니다.

### <a name="cloudsimple-portal-maintenance"></a>클라우드 단순 포털 유지 관리

CloudSimple 제어 평면 또는 인프라가 업데이트될 때 일부 제한된 서비스 가동 중지 시간이 필요합니다. 현재 유지 관리 간격은 한 달에 한 번만큼 빈번할 수 있습니다. 빈도는 시간이 지남에 따라 감소 할 것으로 예상된다. CloudSimple은 포털 유지 관리에 대한 알림을 제공하고 간격을 가능한 한 짧게 유지합니다. 포털 유지 관리 간격 동안 다음과 같은 서비스는 영향을 주지 않고 계속 작동합니다.

* VMware 관리 평면 및 응용 프로그램
* v센터 액세스
* 모든 네트워킹 및 스토리지
* 모든 Azure 트래픽

### <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라의 구성을 변경해야 하는 경우가 있습니다.  현재 이러한 간격은 1-2개월마다 발생할 수 있지만 시간이 지남에 따라 빈도가 감소할 것으로 예상됩니다. 이러한 유형의 유지 관리는 일반적으로 CloudSimple 서비스의 정상적인 소비를 중단하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 동안 다음과 같은 서비스는 아무런 영향 없이 계속 작동합니다.

* VMware 관리 평면 및 응용 프로그램
* v센터 액세스
* 모든 네트워킹 및 스토리지
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple은 프라이빗 클라우드에서 VMware 소프트웨어(ESXi, vCenter, PSC 및 NSX)의 수명 주기 관리를 담당합니다.

소프트웨어 업데이트에는 다음이 포함됩니다.

* **패치**. VMware에서 릴리스한 보안 패치 또는 버그 수정.
* **업데이트**. VMware 스택 구성 요소의 부 버전 변경.
* **업그레이드 .** VMware 스택 구성 요소의 주요 버전 변경.

CloudSimple은 VMware에서 사용할 수 있게 되는 즉시 중요한 보안 패치를 테스트합니다. SLA에 따라 CloudSimple은 1주일 이내에 사설 클라우드 환경에 보안 패치를 제공합니다.

CloudSimple은 VMware 소프트웨어 구성 요소에 분기별 유지 관리 업데이트를 제공합니다. 새로운 주요 버전의 VMware 소프트웨어를 사용할 수 있는 경우 CloudSimple은 고객과 협력하여 업그레이드에 적합한 유지 관리 기간을 조정합니다.

## <a name="next-steps"></a>다음 단계

[Veeam을 사용하여 워크로드 VM 백업](backup-workloads-veeam.md)
