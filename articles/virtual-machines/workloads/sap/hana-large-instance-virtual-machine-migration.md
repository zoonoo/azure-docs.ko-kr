---
title: Azure에서 SAP HANA(대규모 인스턴스)를 Azure 가상 컴퓨터로 마이그레이션 | 마이크로 소프트 문서
description: Azure에서 SAP HANA를 Azure(대형 인스턴스)로 마이그레이션하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617034"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure 대형 인스턴스에서 Azure 가상 컴퓨터로의 SAP HANA 마이그레이션
이 문서에서는 가능한 Azure Large Instance 배포 시나리오를 설명하고 전환 가동 중지 시간을 최소화하여 계획 및 마이그레이션 접근 방식을 제공합니다.

## <a name="overview"></a>개요
2016년 9월 SAP HANA(HLI)에 대한 Azure 대형 인스턴스가 발표된 이후 많은 고객이 이 하드웨어를 메모리 내 컴퓨팅 플랫폼에 대한 서비스 제공으로 채택했습니다.  최근 몇 년 동안 HanA 확장 배포의 지원과 함께 Azure VM 크기 확장은 대부분의 엔터프라이즈 고객의 ERP 데이터베이스 용량 수요를 초과했습니다.  SAP HANA 워크로드를 실제 서버에서 Azure VM으로 마이그레이션하려는 고객이 표시됩니다.
이 가이드는 단계별 구성 문서가 아닙니다. 일반적인 배포 모델을 설명하고 계획 및 마이그레이션 조언을 제공합니다.  전환 가동 중지 시간을 최소화하기 위해 준비에 필요한 고려 사항을 고려한 사항을 호출하는 것이 목적입니다.

## <a name="assumptions"></a>가정
이 문서에서는 다음과 같이 가정합니다.
- 유일한 관심사는 중요한 소프트웨어 업그레이드 나 패치없이 하나 대인스턴스(HLI)에서 Azure VM으로의 균일한 HANA 데이터베이스 컴퓨팅 서비스 마이그레이션입니다. 이러한 사소한 업데이트에는 관련 SAP 노트에서 지원하는 것으로 명시적으로 명시된 최신 OS 버전 또는 HANA 버전의 사용이 포함됩니다. 
- 모든 업데이트/업그레이드 활동은 마이그레이션 전이나 후에 수행해야 합니다.  예를 들어 SAP HANA MCOS가 MDC 배포로 변환합니다. 
- 가동 중지 시간을 가장 적게 제공하는 마이그레이션 방법은 SAP HANA 시스템 복제입니다. 다른 마이그레이션 방법은 이 문서의 범위에 속하지 않습니다.
- 이 지침은 HLI의 Rev3 및 Rev4 SCO 모두에 적용됩니다.
- HANA 배포 아키텍처는 마이그레이션 하는 동안 주로 변경 되지 않습니다.  즉, 단일 인스턴스 DR이 있는 시스템은 대상에서 동일한 방식으로 유지됩니다.
- 고객은 대상(to-be) 아키텍처의 SLA(서비스 수준 계약)를 검토하고 이해했습니다. 
- HlI와 VM 간의 상용 조건은 다릅니다. 고객은 비용 관리를 위해 VM 사용을 모니터링해야 합니다.
- 고객은 HLI가 전용 컴퓨팅 플랫폼이며 VM은 공유되었지만 격리된 인프라에서 실행된다는 것을 알고 있습니다.
- 고객은 대상 VM이 의도한 아키텍처를 지원하는지 확인했습니다. SAP HANA 배포에 대해 인증된 지원되는 모든 VM SkUS를 보려면 [SAP HANA 하드웨어 디렉토리를](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)참조하십시오.
- 고객은 설계 및 마이그레이션 계획의 유효성을 검사했습니다.
- 기본 사이트와 함께 재해 복구 VM을 계획합니다.  고객은 마이그레이션 후 VM에서 실행되는 기본 사이트에 대해 HLI를 DR 노드로 사용할 수 없습니다.
- 고객은 비즈니스 복구 가능성 및 규정 준수 요구 사항에 따라 필요한 백업 파일을 VM을 대상으로 복사했습니다. VM 액세스 가능한 백업을 사용하면 전환 기간 동안 시간 별 복구가 가능합니다.
- HSR HA의 경우 고객은 [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 및 [RHEL에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)대한 SAP HANA HA 가이드당 STONITH 장치를 설정하고 구성해야 합니다.  HLI 케이스처럼 미리 구성되지 않았습니다.
- 이 마이그레이션 방법은 Optane 구성을 갖춘 HLI SCO를 다루지 않습니다.

## <a name="deployment-scenarios"></a>배포 시나리오
HLI 고객을 위한 일반적인 배포 모델은 다음 표에 요약되어 있습니다.  모든 HLI 시나리오에 대해 Azure VM으로 마이그레이션할 수 있습니다.  사용 가능한 보완 Azure 서비스의 이점을 누리려면 약간의 아키텍처 변경이 필요할 수 있습니다.

| 시나리오 ID | HLI 시나리오 | VM으로 마이그레이션하려면? | 설명 |
| --- | --- | --- | --- |
| 1 | [SID가 1개인 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | yes | - |
| 2 | [MCOS가 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | yes | - |
| 3 | [스토리지 복제를 사용하여 DR이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | 예 | Azure 가상 플랫폼에서 저장소 복제를 사용할 수 없으며 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경할 수 없습니다. |
| 4 | [스토리지 복제를 사용하는 DR(다목적)이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | 예 | Azure 가상 플랫폼에서 저장소 복제를 사용할 수 없으며 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경할 수 없습니다. |
| 5 | [고가용성을 위한 스토니트(STONITH)가 있는 HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | yes | 대상 VM에 대해 미리 구성된 SBD가 없습니다.  STONITH 솔루션을 선택하고 배포합니다.  가능한 옵션: Azure 펜싱 [에이전트(RHEL,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)모두에 지원됨), SBD |
| 6 | [HSR, 스토리지 복제를 갖춘 DR을 갖춘 HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | 예 | DR 요구 사항에 맞게 스토리지 복제를 HSR 또는 백업/복원으로 교체 |
| 7 | [호스트 자동 장애 조치(Failover)(1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | yes | Azure VM을 사용하여 공유 저장소에 ANF 사용 |
| 8 | [대기 상태로 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | yes | BW/4HANA M128s, M416s, M416ms VM은 ANF를 사용하여 스토리지전용 |
| 9 | [대기 없이 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | yes | M128s, M416s, M416ms VM을 갖춘 BW/4HANA(스토리지를 위해 ANF 사용 유무) |
| 10 | [스토리지 복제를 사용하여 DR을 사용하여 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | 예 | DR 요구 사항에 맞게 스토리지 복제를 HSR 또는 백업/복원으로 교체 |
| 11 | [HSR을 사용하는 DR이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | yes | - |
| 12 | [단일 노드 HSR에서 DR까지(비용 최적화)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | yes | - |
| 13 | [HSR을 가진 HA 와 닥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | yes | - |
| 14 | [HSR이 있는 HA 및 DR(비용 최적화)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | yes | - |
| 15 | [HSR을 사용하여 DR을 사용한 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | yes | M128s와 BW / 4HANA. M416s, M416ms VM(스토리지를 위해 ANF 사용 유무) |


## <a name="source-hli-planning"></a>소스(HLI) 계획
HLI 서버를 온보딩할 때 Microsoft 서비스 관리와 고객은 SAP HANA 데이터베이스를 실행하기 위한 컴퓨팅, 네트워크, 저장소 및 OS별 설정을 계획했습니다.  Azure VM으로 마이그레이션하려면 유사한 계획이 필요합니다.

### <a name="sap-hana-housekeeping"></a>SAP HANA 하우스키핑 
원치 않거나 오래된 데이터 또는 오래된 로그가 새 데이터베이스로 마이그레이션되지 않도록 데이터베이스 콘텐츠를 정리하는 것이 좋습니다.  하우스키핑에는 일반적으로 오래되었거나 만료되었거나 비활성 데이터를 삭제하거나 보관하는 작업이 포함됩니다.  이러한 '데이터 위생' 작업은 비프로덕션 시스템에서 테스트하여 프로덕션 사용 전에 데이터 트림 유효성을 검증해야 합니다.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>새 VM 및 가상 네트워크에 대한 네트워크 연결 허용 
고객의 HLI 배포에서 네트워크는 [SAP HANA(대형 인스턴스) 네트워크 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)문서에 설명된 정보를 기반으로 설정되었습니다. 또한 네트워크 트래픽 라우팅은 'Azure에서 라우팅' 섹션에 설명된 방식으로 수행됩니다.
- 새 VM을 마이그레이션 대상으로 설정할 때 HLI에 연결할 수 있는 IP 주소 범위가 이미 허용된 기존 가상 네트워크에 배치된 경우 추가 연결 업데이트가 필요하지 않습니다.
- 새 Azure VM이 새 Microsoft Azure 가상 네트워크에 배치되고 다른 지역에 있고 기존 가상 네트워크를 피어링하는 경우 원래 HLI 프로비저닝의 ExpressRoute 서비스 키 및 리소스 ID를 사용하여 이 새 가상 가상 네트워크에 액세스할 수 있습니다. 네트워크 IP 범위.  Microsoft 서비스 관리와 협력하여 가상 네트워크를 HLI 연결에 사용할 수 있습니다.  참고: 응용 프로그램과 데이터베이스 계층 간의 네트워크 대기 시간을 최소화하려면 응용 프로그램과 데이터베이스 계층이 모두 동일한 가상 네트워크에 있어야 합니다.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>기존 앱 계층 가용성 집합, 가용성 영역 및 근접 배치 그룹(PPG)
현재 배포 모델은 특정 서비스 수준 목표를 충족하기 위해 수행됩니다.  이 이동에서 대상 인프라가 설정된 목표를 충족하거나 초과할 지 확인합니다.  
고객 SAP 응용 프로그램 서버는 가용성 집합에 배치될 가능성이 높습니다.  현재 배포 서비스 수준이 만족스럽고 
- 대상 VM이 HLI 논리 이름의 호스트 이름을 가정하는 경우 SAP 프로필을 업데이트하지 않고 VM의 IP를 가리키는 도메인 이름 서비스(DNS) 주소 확인을 업데이트하면 작동합니다.
- PPG를 사용하지 않는 경우 네트워크 대기 시간을 최소화하기 위해 모든 응용 프로그램과 DB 서버를 동일한 영역에 배치해야 합니다.
- PPG를 사용하는 경우 이 문서의 '대상 계획, 가용성 설정, 가용성 영역 및 근접 배치 그룹(PPG)' 섹션을 참조하십시오.

### <a name="storage-replication-discontinuance-process-if-used"></a>저장소 복제 중단 프로세스(사용된 경우)
저장소 복제를 DR 솔루션으로 사용하는 경우 SAP 응용 프로그램이 종료된 후 종료(예약되지 않음)해야 합니다.  또한 마지막 SAP HANA 카탈로그, 로그 파일 및 데이터 백업이 원격 DR HLI 저장소 볼륨에 복제되었습니다.  물리적 서버에서 Azure VM으로 전환하는 동안 재해가 발생할 경우를 대비하여 예방 조치로 이 작업을 수행합니다.

### <a name="data-backups-preservation-consideration"></a>데이터 백업 보존 고려 사항
Azure VM에서 SAP HANA로 잘라낸 후 HLI의 모든 스냅숏 기반 데이터 또는 로그 백업에 쉽게 액세스할 수 없거나 필요한 경우 VM에 복원할 수 없습니다. 초기 전환 기간에 Azure 기반 백업이 시점 복구 요구 사항을 충족하기에 충분한 기록을 작성하기 전에 HLI의 스냅숏 외에 파일 수준 백업을 잘라내기 며칠 또는 몇 주 전에 하는 것이 좋습니다.  이러한 백업을 새 SAP HANA VM에서 액세스할 수 있는 Azure 저장소 계정으로 복사하도록 합니다.
HLI 콘텐츠를 백업하는 것 외에도 롤백이 필요한 경우 SAP 환경의 전체 백업에 쉽게 액세스할 수 있어야 합니다.

### <a name="adjusting-system-monitoring"></a>시스템 모니터링 조정 
고객은 다양한 도구를 사용하여 SAP 환경 내의 시스템에 대한 경고 알림을 모니터링하고 보냅니다.  이 항목은 모니터링에 대한 변경 사항을 통합하고 필요한 경우 경고 알림 수신자를 업데이트하기 위한 적절한 작업을 위한 호출에 불과합니다.

### <a name="microsoft-operations-team-involvement"></a>Microsoft 운영 팀 참여 
기존 HLI 인스턴스를 기반으로 Azure 포털에서 티켓을 엽니다.  지원 티켓을 만든 후 지원 엔지니어가 이메일을 통해 연락을 드립니다.  

### <a name="engage-microsoft-account-team"></a>Microsoft 계정 팀 참여
HLI 계약의 기념일 갱신 시간에 가까운 마이그레이션을 계획하여 계산 리소스에 대한 비용보다 불필요한 비용을 최소화합니다. HLI 블레이드를 해제하려면 계약 종료 및 장치의 실제 종료를 조정해야 합니다.

## <a name="destination-planning"></a>대상 계획
기존 인프라를 대신할 새로운 인프라를 구축하면 새로운 추가가 대규모 계획에 적합하도록 보장할 가치가 있습니다.  다음은 숙고를 위한 몇 가지 핵심 포인트입니다.

### <a name="resource-availability-in-the-target-region"></a>대상 지역의 리소스 가용성 
현재 SAP 응용 프로그램 서버의 배포 영역은 일반적으로 연결된 HlI와 근접합니다.  그러나 사용 가능한 Azure 리전보다 적은 수의 위치에서 HLI가 제공됩니다.  물리적 HLI를 Azure VM으로 마이그레이션할 때 성능 최적화를 위해 모든 관련 서비스의 근접 거리를 '미세 조정'하는 것도 좋은 시기입니다.  이렇게 하는 동안 한 가지 중요한 고려 사항은 선택한 지역에 필요한 모든 리소스가 있는지 확인하는 것입니다.  예를 들어 특정 VM 제품군의 가용성 또는 고가용성 설정을 위한 Azure Zones 제공입니다.

### <a name="virtual-network"></a>가상 네트워크 
고객은 기존 가상 네트워크에서 새 HANA 데이터베이스를 실행할지 아니면 새 데이터베이스를 만들지 선택해야 합니다.  주요 결정 요소는 SAP 환경에 대한 현재 네트워킹 레이아웃입니다.  또한 인프라가 1구역에서 2구역 으로 이동하고 PPG를 사용하면 아키텍처가 변경됩니다. 자세한 내용은 [SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간에 대 한 문서 Azure PPG를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)참조 하십시오.   

### <a name="security"></a>보안
새로운 SAP HANA VM이 새로운 vnet/서브넷에 착륙하든, 보호가 필요한 새로운 비즈니스 크리티컬 서비스를 나타냅니다.  회사 정보 보안 정책을 준수하는 액세스 제어를 이 새로운 서비스 클래스에 대해 평가하고 배포해야 합니다.

### <a name="vm-sizing-recommendation"></a>VM 크기 조정 권장 사항
이 마이그레이션은 HANA 컴퓨팅 엔진의 크기를 조정할 수 있는 기회이기도 합니다.  HANA Studio와 함께 HANA [시스템 뷰를](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) 사용하여 시스템 리소스 소비를 파악할 수 있으므로 적절한 크기 조정을 통해 지출 효율성을 높일 수 있습니다.

### <a name="storage"></a>스토리지 
저장소 성능은 SAP 응용 프로그램 사용자 환경에 영향을 주는 요소 중 하나입니다.  주어진 VM SKU를 기반으로 [SAP HANA Azure 가상 시스템 저장소 구성을 게시한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)최소 저장소 레이아웃이 있습니다. 이러한 최소 사양을 검토하고 기존 HLI 시스템 통계와 비교하여 새로운 HANA VM에 대한 적절한 IO 용량과 성능을 보장하는 것이 좋습니다.

새 HANA VM 및 관련 스럽에 대해 PPG를 구성하는 경우 지원 티켓을 제출하여 저장소와 VM의 공동 위치를 검사하고 확인합니다. 백업 솔루션을 변경해야 할 수 있으므로 운영 비용 놀라움을 피하기 위해 저장소 비용도 다시 검토해야 합니다.

### <a name="storage-replication-for-disaster-recovery"></a>재해 복구를 위한 저장소 복제
HLI를 사용하면 저장소 복제가 재해 복구의 기본 옵션으로 제공되었습니다. 이 기능은 Azure VM에서 SAP HANA의 기본 옵션이 아닙니다. HSR, 백업/복원 또는 비즈니스 요구 사항을 충족하는 기타 지원되는 솔루션을 고려하십시오.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>가용성 집합, 가용성 영역 및 근접 배치 그룹 
네트워크 대기 시간을 최소한으로 유지하기 위해 응용 프로그램 계층과 SAP HANA 간의 거리를 줄이려면 새 데이터베이스 VM과 현재 SAP 응용 프로그램 서버를 PPG에 배치해야 합니다. [SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 배포에 대한 Azure 가용성 집합 및 가용성 영역이 PPG와 어떻게 작동하는지 알아보려면 근접 배치 그룹을 참조하십시오.
대상 HANA 시스템의 구성원이 둘 이상의 Azure Zone에 배포된 경우 고객은 선택한 영역의 대기 시간 프로필을 명확하게 볼 수 있어야 합니다. SAP 시스템 구성 요소의 배치는 SAP 응용 프로그램과 데이터베이스 사이의 근접 거리에 대해 최적입니다.  퍼블릭 도메인 [가용성 영역 대기 시간 테스트 도구를](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) 사용하면 측정을 더 쉽게 할 수 있습니다.  


### <a name="backup-strategy"></a>Backup 전략
많은 고객이 이미 HLI에서 SAP HANA에 대한 타사 백업 솔루션을 사용하고 있습니다.  이 경우 보호된 VM 및 HANA 데이터베이스만 추가로 구성하면 됩니다.  마이그레이션 후 컴퓨터가 서비스 해제되는 경우 진행 중인 HLI 백업 작업을 예약하지 않을 수 있습니다.
이제 VM에서 SAP HANA에 대한 Azure 백업을 일반적으로 사용할 수 있습니다.  Azure VM에서 [백업,](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) [복원,](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)SAP HANA 백업 [관리에](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) 대한 자세한 내용은 다음 링크를 참조하십시오.

### <a name="dr-strategy"></a>DR 전략
서비스 수준 목표가 더 긴 복구 시간을 수용하는 경우 저장소를 blob하고 복원하거나 새 VM으로 복원하는 간단한 백업이 가장 간단하고 비용이 적게 드는 DR 전략입니다.  
HANA DR이 일반적으로 HSR로 수행되는 대형 인스턴스 플랫폼과 마찬가지로; Azure VM에서 HSR은 가장 자연스럽고 네이티브SAP HANA DR 솔루션이기도 합니다.  원본 배포가 단일 인스턴스인지 클러스터된 것인지에 관계없이 DR 리전에서 원본 인프라의 복제본이 필요합니다.
이 DR 복제본은 기본 HLI에서 VM 마이그레이션이 완료된 후 구성됩니다.  DR HANA DB는 VM 인스턴스의 기본 SAP HANA에 보조 복제 사이트로 등록합니다.  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 애플리케이션 서버 연결 대상 변경
HSR 마이그레이션은 새 HANA DB 호스트를 생성하므로 응용 프로그램 계층에 대한 새 DB 호스트 이름을 생성하므로 새 호스트 이름을 반영하도록 SAP 프로필을 수정해야 합니다.  호스트 이름을 보존하는 이름 확인으로 전환이 수행된 경우 프로필을 변경할 필요가 없습니다.

### <a name="operating-system"></a>운영 체제
HLI 및 VM에 대한 운영 체제 이미지는 동일한 릴리스 수준에 있음에도 불구하고 SLES 12 SP4는 동일하지 않습니다. 고객은 대상에 동일한 패키지를 설치하기 위해 HLI에 필요한 패키지, 핫픽스, 패치, 커널 및 보안 픽스의 유효성을 검사해야 합니다.  HSR을 사용하여 이전 OS에서 최신 OS 버전으로 VM으로 복제할 수 있습니다.  [SAP 노트 2763388을](https://launchpad.support.sap.com/#/notes/2763388)검토하여 지원되는 특정 버전을 확인합니다.

### <a name="new-sap-license-request"></a>새 SAP 라이센스 요청
이제 VM으로 마이그레이션되었으므로 새 HANA 시스템에 대한 새 SAP 라이선스를 요청하는 간단한 호출입니다.

### <a name="service-level-agreement-sla-differences"></a>서비스 수준 계약(SLA) 차이
저자는 HLI와 Azure VM 사이의 가용성 SLA의 차이를 호출하고 싶습니다.  예를 들어 클러스터된 HLI HA 쌍은 99.99%의 가용성을 제공합니다. 동일한 SLA를 달성하려면 VM을 가용성 영역에 배포해야 합니다. 이 [문서에서는](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) 고객이 그에 따라 대상 인프라를 계획할 수 있도록 연결된 배포 아키텍처의 가용성에 대해 설명합니다.


## <a name="migration-strategy"></a>마이그레이션 전략
이 문서에서는 HLI에서 Azure VM으로마이그레이션하기 위한 HANA 시스템 복제 방법만 다룹니다.  배포된 대상 저장소 솔루션에 따라 프로세스가 약간 다릅니다. 상위 단계는 아래에 설명되어 있습니다.

### <a name="vm-with-premiumultra-disks-for-data"></a>데이터를 위한 프리미엄/울트라 디스크가 있는 VM
프리미엄 또는 울트라 디스크와 함께 배포되는 VM의 경우 표준 SAP HANA 시스템 복제 구성이 HSR 설정에 적용됩니다.  [SAP 도움말 문서에서는](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) 시스템 복제 설정, 보조 시스템 인수, 기본 시스템 으로의 장애 및 시스템 복제 비활성화와 관련된 단계에 대한 개요를 제공합니다.  마이그레이션을 위해 복제 단계를 설정하고 인계하고 비활성화하기만 하면 됩니다.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>데이터 및 로그 볼륨을 위한 ANF가 있는 VM
높은 수준에서 전체 데이터 및 로그 볼륨의 최신 HLI 저장소 스냅숏을 Azure Storage에 복사하여 대상 HANA VM에서 액세스 하고 복구할 수 있습니다.  복사 프로세스는 모든 네이티브 Linux 복사 도구를 사용하여 수행할 수 있습니다.  

> [!IMPORTANT]
> 복사 및 데이터 전송은 HANA 데이터베이스 크기 및 네트워크 대역폭에 따라 몇 시간이 걸릴 수 있습니다.  복사 프로세스의 대부분은 기본 HANA DB 가동 중지 시간 전에 수행해야 합니다.

### <a name="mcos-to-mdc-conversion"></a>MCOS에서 MDC로 변환
MCOS(단일 시스템) 배포 모델의 여러 구성 요소는 일부 HLI 고객이 사용했습니다.  그 동기는 이전 SAP HANA 버전의 MDC(다중 데이터베이스 컨테이너) 저장소 스냅숏 제한을 우회하는 것이었습니다.  MCOS 모델에서 여러 독립적인 SAP HANA 인스턴스가 하나의 HLI 블레이드에 누적됩니다.  마이그레이션에 HSR을 사용하면 잘 작동하지만 각각 하나의 테넌트 DB가 있는 여러 HANA VM이 생성됩니다.  이 최종 상태는 고객이 익숙한 것보다 더 바쁜 풍경을 만듭니다.  SAP HANA 2.0 기본 배포가 MDC인 경우 HSR 마이그레이션 후 [HANA 테넌트 이동을](https://launchpad.support.sap.com/#/notes/2472478) 수행할 수 있는 실행 가능한 대안이 있습니다.  이 프로세스는 이러한 독립적인 HANA 데이터베이스를 하나의 HANA 컨테이너에 있는 공동 으로 '통합'합니다.  

### <a name="application-layer-consideration"></a>응용 프로그램 계층 고려 사항
DB 서버는 SAP 시스템의 중심으로 간주됩니다.  모든 응용 프로그램 서버는 SAP HANA DB 근처에 있어야 합니다.  경우에 따라 PPG를 새로 사용해야 하는 경우 기존 응용 프로그램 서버를 HANA VM이 필요한 PPG로 재배치해야 할 수 있습니다.  배포 템플릿이 이미 있는 경우 새 응용 프로그램 서버를 쉽게 빌드할 수 있습니다.  
기존 응용 프로그램 서버와 새 HANA VM이 최적으로 위치한 경우 추가 용량이 필요하지 않는 한 새 응용 프로그램 서버를 빌드할 필요가 없습니다.  
서비스 가용성을 향상시키기 위해 새 인프라가 구축되면 기존 응용 프로그램 서버가 필요하지 않을 수 있으며 종료하고 삭제해야 합니다.
대상 VM 호스트 이름이 변경되고 HLI 호스트 이름과 다른 경우 새 호스트를 가리키도록 SAP 응용 프로그램 서버 프로필을 조정해야 합니다.  HANA DB IP 주소만 변경된 경우 새 HANA VM에 들어오는 연결을 연결하려면 DNS 레코드 업데이트가 필요합니다.

### <a name="acceptance-test"></a>합격 시험
HLI에서 VM으로의 마이그레이션은 이기종 마이그레이션과 비교하여 데이터베이스 콘텐츠에 대한 중요한 변경을 하지 않지만 새 설정의 주요 기능 및 성능 측면의 유효성을 검사하는 것이 좋습니다.  

### <a name="cutover-plan"></a>컷오버 계획
그러나 이 마이그레이션은 간단하지만 기존 DB의 서비스 해제와 관련이 있습니다.  대체가 필요한 경우 관련 콘텐츠 및 백업 이미지로 소스 시스템을 보존하기 위한 신중한 계획이 중요합니다.  좋은 계획은 더 빠른 반전을 제공합니다.   


## <a name="post-migration"></a>마이그레이션 후
데이터 무결성이 유지되도록 HLI 종속 서비스 또는 연결을 안전하게 분리할 때까지 마이그레이션 작업이 수행되지 않습니다.  또한 불필요한 서비스를 종료합니다.  이 섹션에서는 몇 가지 최상위 항목을 호출합니다.

### <a name="decommissioning-the-hli"></a>HLI 해체
HANA DB를 Azure VM으로 성공적으로 마이그레이션한 후 HLI DB에서 생산적인 비즈니스 트랜잭션이 실행되지 않도록 합니다.  그러나 HLI를 로컬 백업 보존 기간과 동일하게 시간 동안 실행하는 것은 필요한 경우 더 빠른 복구를 보장하는 안전한 방법입니다.  그런 다음에야 HLI 블레이드를 폐기해야 합니다.  고객은 Microsoft 담당자에게 문의하여 Microsoft와의 HLI 계약을 계약상 체결해야 합니다.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>HLI용으로 구성된 프록시(예: IPtables, BIGIP) 제거 
IPTables와 같은 프록시 서비스가 HLI로 온-프레미스 트래픽을 라우팅하는 데 사용되는 경우 VM으로 성공적으로 마이그레이션한 후에는 더 이상 필요하지 않습니다.  그러나 이 연결 서비스는 HLI 블레이드가 여전히 대기 되어 있는 한 유지되어야 합니다.  HLI 블레이드가 완전히 폐기된 후에만 서비스를 종료합니다.

### <a name="remove-global-reach-for-hli"></a>HLI에 대한 글로벌 도달 범위 제거 
글로벌 리치는 고객의 ExpressRoute 게이트웨이를 HLI 익스프레스루트 게이트웨이와 연결하는 데 사용됩니다.  이를 통해 고객의 온-프레미스 트래픽이 프록시 서비스를 사용하지 않고 HLI 테넌트에 직접 도달할 수 있습니다.  마이그레이션 후 HLI 장치가 없는 경우 이 연결이 더 이상 필요하지 않습니다.  IPTables 프록시 서비스의 경우와 마찬가지로 글로벌리치도 HLI 블레이드가 완전히 해제될 때까지 보관해야 합니다.

### <a name="operating-system-subscription--movereuse"></a>운영 체제 구독 - 이동/재사용
VM 서버가 가동되고 HLI 블레이드가 해제되면 OS 라이선스를 교체하거나 재사용할 수 있습니다.



## <a name="next-steps"></a>다음 단계
 아래 문서를 참조하세요.
- [Azure에서 SAP HANA 인프라 구성 및 작업.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure의 SAP 워크로드: 계획 및 배포 검사 목록.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
