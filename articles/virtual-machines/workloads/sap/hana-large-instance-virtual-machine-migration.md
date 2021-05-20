---
title: Azure의 SAP HANA(대규모 인스턴스)를 Azure 가상 머신으로 마이그레이션| Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)를 Azure 가상 머신으로 마이그레이션하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd1cfb0cc8e1868e78b4d284d1b1f4e7e85aa318
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677043"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure의 SAP HANA(대규모 인스턴스)를 Azure 가상 머신으로 마이그레이션
이 문서에서는 가능한 Azure 대규모 인스턴스 배포 시나리오를 설명하고 전환 가동 중지 시간을 최소화한 계획 및 마이그레이션 방식을 제공합니다.

## <a name="overview"></a>개요
2016년 9월 SAP HANA(HLI)용 Azure 대규모 인스턴스 발표 이후 많은 고객이 HaaS(Hardware-as-a-Service) 제품을 메모리 내 컴퓨팅 플랫폼에 채택하고 있습니다.  최근 몇 년 동안은 HANA 스케일 아웃 배포 지원과 결합된 Azure VM 크기 확장이 엔터프라이즈 고객 대부분의 ERP 데이터베이스 용량 수요를 초과했습니다.  SAP HANA 워크로드를 물리적 서버에서 Azure VM으로 마이그레이션하는 데 관심을 표현하는 고객이 보이기 시작했습니다.
이 문서는 단계별 구성 가이드가 아닙니다. 일반적인 배포 모델을 설명하고 계획 및 마이그레이션 조언을 제공합니다.  전환 가동 중지 시간을 최소화하기 위한 준비에 필요한 고려 사항을 설명하는 것이 목적입니다.

## <a name="assumptions"></a>가정
이 문서에서는 다음과 같이 가정합니다.
- 동종 HANA 데이터베이스 컴퓨팅 서비스를 HLI(Hana Large Instance)에서 Azure VM으로 마이그레이션하는 것만이 유일한 고려 대상입니다. 중요한 소프트웨어 업그레이드나 패치 적용은 포함되지 않습니다. 사소한 업데이트에는 관련 SAP Note에 지원하는 것으로 명시되어 있는 최신 OS 버전 또는 HANA 버전 사용이 포함됩니다. 
- 모든 업데이트/업그레이드 작업은 마이그레이션 전이나 후에 수행해야 합니다.  예: SAP HANA MCOS를 MDC 배포로 변환. 
- 가동 중지 시간을 최소화하는 마이그레이션 방식은 SAP HANA 시스템 복제입니다. 다른 마이그레이션 방법은 이 문서의 범위에 포함되지 않습니다.
- 이 지침은 HLI의 Rev3 및 Rev4 SKU 모두에 적용됩니다.
- 마이그레이션 중에 HANA 배포 아키텍처는 주로 변경되지 않은 상태로 유지됩니다.  즉, 단일 인스턴스 DR을 사용하는 시스템은 대상에서 동일하게 유지됩니다.
- 대상(예정) 아키텍처의 Service Level Agreement(서비스 수준 약정)를 고객이 검토하고 이해했습니다. 
- HLI와 VM 간의 상업적 용어는 다릅니다. 고객은 비용 관리를 위해 자체 VM 사용량을 모니터링해야 합니다.
- HLI는 전용 컴퓨팅 플랫폼이고 VM은 격리된 공유 인프라에서 실행된다는 점을 고객이 이해했습니다.
- 대상 VM이 원하는 아키텍처를 지원하는지 고객이 확인했습니다. SAP HANA 배포용으로 인증된 지원되는 모든 VM SKU를 보려면 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)를 참조하세요.
- 디자인 및 마이그레이션 계획의 유효성을 고객이 검사했습니다.
- 재해 복구 VM을 주 사이트와 함께 계획합니다.  고객은 마이그레이션 후 HLI를 VM에서 실행되는 주 사이트의 DR 노드로 사용할 수 없습니다.
- 고객이 비즈니스 복구 가능성 및 규정 준수 요구 사항에 따라 필요한 백업 파일을 대상 VM에 복사했습니다. VM 액세스 가능 백업을 통해, 전환 기간 동안 특정 시점으로 복구를 수행할 수 있습니다.
- HSR HA의 경우 고객이 [SLES](./high-availability-guide-suse-pacemaker.md) 및 [RHEL](./high-availability-guide-rhel-pacemaker.md)에 대한 SAP HANA HA 가이드에 따라 STONITH 디바이스를 설정하고 구성해야 합니다.  HLI 사례와 같이 미리 구성되어 있지 않습니다.
- 이 마이그레이션 방식에 Optane 구성을 통한 HLI SKU는 포함되지 않습니다.

## <a name="deployment-scenarios"></a>배포 시나리오
HLI 고객의 일반적인 배포 모델은 다음 표에 요약되어 있습니다.  모든 HLI 시나리오에서 Azure VM으로 마이그레이션이 가능합니다.  사용 가능한 보조 Azure 서비스의 이점을 활용하려면 약간의 아키텍처 변경이 필요할 수 있습니다.

| 시나리오 ID | HLI 시나리오 | VM으로 마이그레이션 여부 | 설명 |
| --- | --- | --- | --- |
| 1 | [SID가 1개인 단일 노드](./hana-supported-scenario.md#single-node-with-one-sid) | 예 | - |
| 2 | [MCOS 포함 단일 노드](./hana-supported-scenario.md#single-node-mcos) | 예 | - |
| 3 | [스토리지 복제를 사용하는 DR이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | 아니요 | Azure 가상 플랫폼에서는 스토리지 복제를 사용할 수 없습니다. 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경하세요. |
| 4 | [스토리지 복제를 사용하는 DR(다목적)이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | 아니요 | Azure 가상 플랫폼에서는 스토리지 복제를 사용할 수 없습니다. 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경하세요. |
| 5 | [고가용성을 위한 STONITH 포함 HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | 예 | 대상 VM에 대해 미리 구성된 SBD가 없습니다.  STONITH 솔루션을 선택하고 배포합니다.  가능한 옵션: Azure 펜싱 에이전트([RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md) 모두 지원), SBD |
| 6 | [HSR을 통한 HA, 스토리지 복제를 통한 DR](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | 아니요 | DR에 필요한 스토리지 복제를 HSR 또는 백업/복원으로 교체 |
| 7 | [호스트 자동 장애 조치(Failover)(1+1)](./hana-supported-scenario.md#host-auto-failover-11) | 예 | Azure VM에서 공유스토리지에 ANF 사용 |
| 8 | [대기 상태로 스케일 아웃](./hana-supported-scenario.md#scale-out-with-standby) | 예 | 스토리지 전용 ANF를 사용하는 M128s, M416s, M416ms VM이 있는 BW/4HANA |
| 9 | [대기 없이 스케일 아웃](./hana-supported-scenario.md#scale-out-without-standby) | 예 | M128s, M416s, M416ms VM이 있는 BW/4HANA(스토리지에 ANF를 사용하거나 사용하지 않음) |
| 10 | [스토리지 복제를 사용하는 DR이 있는 스케일 아웃](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | 아니요 | DR에 필요한 스토리지 복제를 HSR 또는 백업/복원으로 교체 |
| 11 | [HSR을 사용하는 DR이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | 예 | - |
| 12 | [단일 노드 HSR에서 DR로(비용 최적화)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | 예 | - |
| 13 | [HSR을 통한 HA 및 DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | 예 | - |
| 14 | [HSR을 통한 HA 및 DR(비용 최적화)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 예 | - |
| 15 | [HSR을 사용하는 DR이 있는 스케일 아웃](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | 예 | M128s, M416s, M416ms VM이 있는 BW/4HANA(스토리지에 ANF를 사용하거나 사용하지 않음) |


## <a name="source-hli-planning"></a>원본(HLI) 계획
HLI 서버를 온보딩할 때 Microsoft Service Management와 고객은 모두 SAP HANA 데이터베이스 실행을 위한 컴퓨팅, 네트워크, 스토리지 및 OS별 설정에 대한 계획을 진행합니다.  Azure VM으로 마이그레이션에도 유사한 계획을 진행해야 합니다.

### <a name="sap-hana-housekeeping"></a>SAP HANA 하우스키핑 
원치 않는 오래된 데이터 또는 부실한 로그가 새 데이터베이스로 마이그레이션되지 않도록 데이터베이스 콘텐츠를 정리하는 것이 운영상 좋은 방법입니다.  하우스키핑에는 일반적으로 오래되거나 만료되었거나 비활성 상태인 데이터 삭제 또는 보관이 포함됩니다.  이러한 '데이터 예방 조치' 작업은 프로덕션 사용 전에 데이터 트리밍 유효성을 검사하기 위해 비프로덕션 시스템에서 테스트해야 합니다.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>새 VM 및/또는 가상 네트워크에 대한 네트워크 연결 허용 
고객의 HLI 배포에서 네트워크는 [SAP HANA(대규모 인스턴스) 네트워크 아키텍처](./hana-network-architecture.md) 문서에 설명된 정보를 기반으로 설정되었습니다. 또한 네트워크 트래픽 라우팅은 'Azure의 라우팅' 섹션에 설명된 방식으로 수행됩니다.
- 새 VM을 마이그레이션 대상으로 설정할 때 HLI에 연결하도록 이미 허용된 IP 주소 범위가 있는 기존 가상 네트워크에 배치된 경우에는 연결 업데이트가 추가로 필요하지 않습니다.
- 새 Azure VM이 새 Microsoft Azure Virtual Network(다른 지역에 있을 수 있음)에 배치되고 기존 가상 네트워크와 피어링된 경우, 원래 HLI 프로비저닝의 ExpressRoute 서비스 키 및 리소스 ID를 사용하여 새 가상 네트워크 IP 범위에 대한 액세스를 허용할 수 있습니다.  Microsoft Service Management와 협력하여 가상 네트워크와 HLI 연결이 가능하도록 설정합니다.  참고: 애플리케이션과 데이터베이스 계층 간의 네트워크 대기 시간을 최소화하려면 애플리케이션과 데이터베이스 계층이 모두 동일한 가상 네트워크에 있어야 합니다.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>기존 앱 계층 가용성 집합, 가용성 영역 및 PPG(근접 배치 그룹)
현재 배포 모델은 특정 서비스 수준 목표를 충족하기 위해 수행됩니다.  이 경우, 설정된 목표를 대상 인프라가 충족하거나 초과해야 합니다.  
대부분, 고객 SAP 애플리케이션 서버는 가용성 집합에 배치됩니다.  현재 배포 서비스 수준이 만족스러운 경우 그리고: 
- 대상 VM이 HLI 논리적 이름의 호스트 이름을 가정하는 경우 VM의 IP를 가리키는 DNS(도메인 이름 서비스) 주소 확인을 업데이트하면 SAP 프로필을 업데이트하지 않고도 작동합니다.
- PPG를 사용하지 않는 경우에는 네트워크 지연 시간을 최소화하기 위해 모든 애플리케이션과 DB 서버를 동일한 영역에 배치해야 합니다.
- PPG를 사용하는 경우에는 이 문서의 '대상 계획, 가용성 집합, 가용성 영역 및 PPG(근접 배치 그룹)' 섹션을 참조하세요.

### <a name="storage-replication-discontinuance-process-if-used"></a>스토리지 복제 중단 프로세스(사용되는 경우)
스토리지 복제가 DR 솔루션으로 사용되는 경우에는 SAP 애플리케이션이 종료된 후 종료(예약되지 않음)되어야 합니다.  또한 마지막 SAP HANA 카탈로그, 로그 파일 및 데이터 백업이 원격 DR HLI 스토리지 볼륨에 복제되어야 합니다.  이런 작업은 물리적 서버에서 Azure VM으로 전환하는 동안 재해가 발생하는 경우를 대비하여 예방 조치로 수행합니다.

### <a name="data-backups-preservation-consideration"></a>데이터 백업 보존 고려 사항
Azure VM의 SAP HANA로 전환된 후에는 필요한 경우 HLI의 모든 스냅샷 기반 데이터 또는 로그 백업에 쉽게 액세스하거나 VM으로 복원할 수 없습니다. 초기 전환 기간에는 Azure 기반 백업을 통해 특정 시점 복구 요구 사항을 충족하기에 충분한 기록이 작성되기 전에, 전환되기 며칠 또는 몇 주 전에 HLI에서 스냅샷 외에도 파일 수준 백업을 수행하는 것이 좋습니다.  이러한 백업은 새 SAP HANA VM에서 액세스할 수 있는 Azure Storage 계정에 복사해야 합니다.
HLI 콘텐츠를 백업하는 것 외에도 롤백이 필요한 경우 SAP 환경의 전체 백업에 쉽게 액세스할 수 있도록 하는 것이 좋습니다.

### <a name="adjusting-system-monitoring"></a>시스템 모니터링 조정 
고객은 다양한 도구를 사용하여 SAP 환경 내에서 모니터링하고 시스템에 대한 경고 알림을 보냅니다.  이 항목은 모니터링에 대한 변경 사항을 통합하고 필요한 경우 경고 알림 수신자를 업데이트하는 데 적절한 작업에 대한 설명입니다.

### <a name="microsoft-operations-team-involvement"></a>Microsoft 운영 팀 참여 
기존 HLI 인스턴스를 기반으로 Azure Portal에서 티켓을 엽니다.  지원 티켓이 만들어지면 지원 엔지니어가 이메일을 통해 연락을 드립니다.  

### <a name="engage-microsoft-account-team"></a>Microsoft 계정 팀 참여
컴퓨팅 리소스에 대한 불필요한 초과 비용을 최소화하기 위해 HLI 계약의 갱신 기간이 다가올 때 마이그레이션을 계획합니다. HLI 블레이드를 서비스 해제하려면 단위의 계약 해지와 실제 종료를 맞춰서 조정해야 합니다.

## <a name="destination-planning"></a>대상 계획
기존 인프라를 대체할 새로운 인프라를 구축하는 경우, 새로운 추가 사항이 대규모 계획에 적합하도록 몇 가지를 생각해 두는 것이 좋습니다.  다음은 이러한 생각을 위한 몇 가지 주요 사항입니다.

### <a name="resource-availability-in-the-target-region"></a>대상 지역의 리소스 가용성 
현재 SAP 애플리케이션 서버의 배포 지역은 일반적으로 연결된 HLI와 매우 가까이 있습니다.  그러나 HLI는 사용 가능한 Azure 지역보다 적은 위치에서 제공됩니다.  물리적 HLI를 Azure VM으로 마이그레이션할 때 성능 최적화를 위해 모든 관련 서비스의 근접 거리도 '미세 조정'하는 것이 좋습니다.  이 작업을 수행하는 동안 한 가지 주요 고려 사항은 필요한 모든 리소스가 선택한 지역에 있는지 확인하는 것입니다.  예를 들어 특정 VM 제품군의 가용성 또는 고가용성 설정을 위한 Azure 영역 제공 등이 있습니다.

### <a name="virtual-network"></a>가상 네트워크 
고객은 기존 가상 네트워크에서 새 HANA 데이터베이스를 실행할지 아니면 새로 만들지를 선택해야 합니다.  주요 결정 요소는 SAP 환경에 대한 현재 네트워킹 레이아웃입니다.  또한, 인프라가 1개 영역에서 2개 영역 배포로 변하고 PPG를 사용하면 아키텍처 변경이 필요합니다. 자세한 내용은 [SAP 애플리케이션에서 최적의 네트워크 대기 시간을 위한 Azure PPG](./sap-proximity-placement-scenarios.md) 문서를 참조하세요.   

### <a name="security"></a>보안
새 SAP HANA VM을 신규 또는 기존 vnet/서브넷 중 어디에서 사용하든, 새로운 중요 비즈니스용 서비스에 보호가 필요합니다.  회사 정보 보안 정책을 준수하는 액세스 제어를 새로운 수준의 서비스에 맞게 평가하고 배포해야 합니다.

### <a name="vm-sizing-recommendation"></a>VM 크기 조정 권장 사항
마이그레이션은 HANA 컴퓨팅 엔진의 크기를 적절하게 조정할 수 있는 기회이기도 합니다.  HANA Studio와 함께 HANA [시스템 뷰](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)를 사용하여 시스템 리소스 사용을 파악하고, 적절한 크기 조정을 통해 지출 효율성을 높일 수 있습니다.

### <a name="storage"></a>스토리지 
스토리지 성능은 SAP 애플리케이션 사용자 환경에 영향을 주는 요인 중 하나입니다.  지정된 VM SKU를 기반으로 [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)에 게시된 최소 스토리지 레이아웃이 있습니다. 새 HANA VM에 대한 적절한 IO 용량 및 성능을 보장하기 위해 최소 사양을 검토하고 기존 HLI 시스템 통계와 비교하는 것이 좋습니다.

새 HANA VM 및 연결된 서버에 대해 PPG를 구성하는 경우에는 지원 티켓을 제출하여 스토리지와 VM의 공동 배치를 검사하고 확인합니다. 백업 솔루션을 변경해야 할 수도 있으니, 운영 비용을 갑자기 지출하지 않도록 스토리지 비용도 다시 검토하는 것이 좋습니다.

### <a name="storage-replication-for-disaster-recovery"></a>재해 복구를 위한 스토리지 복제
HLI에서는 스토리지 복제가 재해 복구를 위한 기본 옵션으로 제공됩니다. 이 기능은 Azure VM의 SAP HANA에 대한 기본 옵션이 아닙니다. HSR, 백업/복원 또는 비즈니스 요구 사항을 충족하는 기타 지원 솔루션을 고려하십시오.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>가용성 집합, 가용성 영역 및 근접 배치 그룹 
네트워크 지연 시간을 최소화하기 위해 애플리케이션 계층과 SAP HANA 사이의 거리를 줄이려면 새 데이터베이스 VM과 현재 SAP 애플리케이션 서버를 PPG에 배치해야 합니다. SAP 배포용 PPG에서 Azure 가용성 집합 및 가용성 영역이 작동하는 방식을 알아보려면 [근접 배치 그룹](./sap-proximity-placement-scenarios.md)을 참조하세요.
대상 HANA 시스템의 구성원이 둘 이상의 Azure 영역에 배포된 경우 고객은 선택한 영역의 대기 시간 프로필을 명확하게 볼 수 있어야 합니다. SAP 시스템 구성 요소의 배치는 SAP 애플리케이션과 데이터베이스 간의 근접 거리와 관련하여 최적입니다.  공용 도메인 [가용성 영역 대기 시간 테스트 도구](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)를 사용하면 측정을 보다 쉽게 수행할 수 있습니다.  


### <a name="backup-strategy"></a>Backup 전략
HLI에 SAP HANA용 타사 백업 솔루션을 이미 사용하는 고객이 많습니다.  보호된 VM 및 HANA 데이터베이스를 추가로 구성하기만 하면 됩니다.  마이그레이션 후 컴퓨터가 서비스 해제되는 경우에는 진행 중인 HLI 백업 작업의 예약을 취소할 수 있습니다.
VM의 SAP HANA에 대한 Azure Backup이 이제 일반 공급됩니다.  Azure VM에서 SAP HANA [백업](../../../backup/backup-azure-sap-hana-database.md), [복원](../../../backup/sap-hana-db-restore.md), [관리](../../../backup/sap-hana-db-manage.md)에 대한 자세한 내용은 링크를 참조하세요.

### <a name="dr-strategy"></a>DR 전략
서비스 수준 목표가 긴 복구 시간을 수용하는 경우, Blob Storage에 간단히 백업했다가 제자리에 복원하거나 새 VM으로 복원하는 것이 가장 간단하고 비용이 적게 드는 DR 전략입니다.  
일반적으로 HSR을 사용하여 HANA DR이 수행되는 대규모 인스턴스 플랫폼과 같습니다. Azure VM에서 HSR은 가장 자연스러운 네이티브 SAP HANA DR 솔루션이기도 합니다.  원본 배포가 단일 인스턴스인지 클러스터링되었는지에 관계없이 DR 지역에는 원본 인프라의 복제본이 필요합니다.
이러한 DR 복제본은 주 HLI에서 VM으로 마이그레이션이 완료된 후에 구성됩니다.  DR HANA DB는 VM 인스턴스의 주 SAP HANA에 보조 복제 사이트로 등록됩니다.  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 애플리케이션 서버 연결 대상 변경
HSR 마이그레이션으로 인해 새 HANA DB 호스트가 생성되므로 애플리케이션 계층에 대한 새 DB 호스트 이름, SAP 프로필은 새 호스트 이름을 반영하도록 수정해야 합니다.  호스트 이름을 유지하는 이름 확인으로 전환이 수행되는 경우 프로필을 변경할 필요가 없습니다.

### <a name="operating-system"></a>운영 체제
HLI 및 VM의 운영 체제 이미지는 릴리스 수준이 동일한 경우에도(예: SLES 12 SP4) 동일하지 않습니다. 대상에 동일한 패키지를 설치하기 위해 고객은 HLI에서 필수 패키지, 핫 픽스, 패치, 커널 및 보안 수정을 확인해야 합니다.  HSR을 사용하여 이전 OS에서 최신 OS 버전을 사용하는 VM으로 복제하는 것이 지원됩니다.  [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388)을 검토하여 지원되는 특정 버전을 확인하세요.

### <a name="new-sap-license-request"></a>새 SAP 라이선스 요청
VM으로 마이그레이션된 후 새 HANA 시스템에 대한 새 SAP 라이선스를 요청하는 간단한 호출입니다.

### <a name="service-level-agreement-sla-differences"></a>Service Level Agreement(서비스 수준 약정) 차이
HLI와 Azure VM 간의 가용성 SLA 차이에 대한 설명입니다.  예를 들어 클러스터형 HLI HA 쌍은 99.99%의 가용성을 제공합니다. 동일한 SLA를 얻으려면 가용성 영역에 VM을 배포해야 합니다. 이 [문서](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)에는 고객이 대상 인프라를 계획할 때 참고할 수 있도록, 연결된 배포 아키텍처의 가용성에 대한 설명이 있습니다.


## <a name="migration-strategy"></a>마이그레이션 전략
이 문서에서는 HLI에서 Azure VM으로 마이그레이션하기 위한 HANA 시스템 복제 방식만 다룹니다.  배포된 대상 스토리지 솔루션에 따라 프로세스는 약간 다릅니다. 대략적인 단계는 아래에 설명되어 있습니다.

### <a name="vm-with-premiumultra-disks-for-data"></a>데이터용 프리미엄 디스크/Ultra Disk가 있는 VM
프리미엄 디스크 또는 Ultra Disk로 배포된 VM의 경우 표준 SAP HANA 시스템 복제 구성이 HSR 설정에 적용됩니다.  [SAP 도움말 문서](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)에는 시스템 복제 설정, 보조 시스템 인수, 주 시스템으로 장애 복구(failover), 시스템 복제 비활성화와 관련된 단계에 대한 개요가 제공됩니다.  마이그레이션을 위해서는 복제 단계를 설정, 인수 및 비활성화하는 것만 필요합니다.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>데이터 및 로그 볼륨에 대해 ANF를 사용하는 VM
상위 수준에서 전체 데이터 및 로그 볼륨의 최신 HLI 스토리지 스냅샷은 대상 HANA VM에서 액세스 및 복구할 수 있는 Azure Storage에 복사해야 합니다.  복사 프로세스는 네이티브 Linux 복사 도구를 사용하여 수행할 수 있습니다.  

> [!IMPORTANT]
> 복사 및 데이터 전송은 HANA 데이터베이스 크기 및 네트워크 대역폭에 따라 시간이 오래 걸릴 수 있습니다.  대량 복사 프로세스는 주 HANA DB 가동 중지 시간 전에 수행해야 합니다.

### <a name="mcos-to-mdc-conversion"></a>MCOS에서 MDC로 변환
MCOS(Multiple Components in One System) 배포 모델은 일부 HLI 고객이 사용했습니다.  이전 SAP HANA 버전의 MDC(Multiple Databases Container) 스토리지 스냅샷 제한을 피하기 위해 사용되었습니다.  MCOS 모델에서는 독립적인 여러 SAP HANA 인스턴스가 하나의 HLI 블레이드에 누적됩니다.  마이그레이션에 HSR을 사용하면 잘 작동하지만 테넌트 DB가 각각 하나씩 있는 여러 HANA VM이 생성됩니다.  이러한 최종 상태는 고객이 익숙한 것보다 사용량이 더 많은 환경을 만듭니다.  SAP HANA 2.0 기본 배포가 MDC인 경우 실행 가능한 대안은 HSR 마이그레이션 후 [HANA 테넌트 이동](https://launchpad.support.sap.com/#/notes/2472478)을 수행하는 것입니다  이 프로세스는 독립 HANA 데이터베이스를 단일 HANA 컨테이너의 공동 테넌트로 '통합'합니다.  

### <a name="application-layer-consideration"></a>애플리케이션 계층 고려 사항
DB 서버는 SAP 시스템의 중심으로 간주됩니다.  모든 애플리케이션 서버는 SAP HANA DB 근처에 위치해야 합니다.  PPG를 새로 사용하려는 경우 HANA VM이 있는 PPG에 기존 애플리케이션 서버를 재배치하는 것이 필요할 수 있습니다.  이미 편리한 배포 템플릿이 있으면 새로운 애플리케이션 서버를 구축하는 것이 더 쉬울 수 있습니다.  
기존 애플리케이션 서버와 새 HANA VM이 최적의 위치에 있는 경우 추가 용량이 필요하지 않으면 새 애플리케이션 서버를 구축할 필요가 없습니다.  
서비스 가용성을 향상시키기 위해 새 인프라를 구축하는 경우 기존 애플리케이션 서버가 불필요해져서 종료하고 삭제해야 할 수도 있습니다.
대상 VM 호스트 이름이 변경되고 HLI 호스트 이름과 다른 경우 새 호스트를 가리키도록 SAP 애플리케이션 서버 프로필을 조정해야 합니다.  HANA DB IP 주소만 변경된 경우 들어오는 연결을 새 HANA VM으로 유도하려면 DNS 레코드를 업데이트해야 합니다.

### <a name="acceptance-test"></a>수용 테스트
HLI에서 VM으로 마이그레이션은 이기종 마이그레이션에 비해 데이터베이스 콘텐츠에 중요한 변경 사항이 없지만 그래도 새 설정의 주요 기능 및 성능 측면을 확인하는 것이 좋습니다.  

### <a name="cutover-plan"></a>전환 계획
이 마이그레이션은 간단하지만 기존 DB를 서비스 해제해야 합니다.  대체(fallback)가 필요한 경우, 원본 시스템을 연결된 콘텐츠 및 백업 이미지와 함께 보존하기 위해 신중한 계획을 세우는 것이 중요합니다.  계획을 잘 세우면 빠른 전환이 가능합니다.   


## <a name="post-migration"></a>마이그레이션 후
데이터 무결성을 보존하기 위해 HLI 종속 서비스 또는 연결을 안전하게 분리할 때까지 마이그레이션 작업은 완료되지 않습니다.  또한 불필요한 서비스를 종료하십시오.  이 섹션에서는 몇 가지 중요한 항목을 설명합니다.

### <a name="decommissioning-the-hli"></a>HLI 서비스 해제
HANA DB를 Azure VM으로 마이그레이션하는 데 성공한 후에는 HLI DB에서 생산적인 비즈니스 트랜잭션이 실행되지 않도록 합니다.  단, 필요한 경우 로컬 백업 보존 기간과 동일한 기간 동안 HLI를 실행하면 안전하고 빠른 복구가 가능합니다.  그런 다음, HLI 블레이드를 서비스 해제해야 합니다.  고객은 Microsoft 담당자에게 연락하여 Microsoft와의 계약을 통해 HLI 약정을 체결해야 합니다.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>HLI에 대해 구성된 프록시(예: Iptables, BIGIP) 제거 
IPTables와 같은 프록시 서비스를 사용하여 HLI와 주고 받는 온-프레미스 트래픽을 라우팅하는 경우 VM으로 마이그레이션하는 데 성공하면 해당 서비스가 더 이상 필요하지 않습니다.  단, HLI 블레이드가 아직 대기 상태이면 해당 연결 서비스를 유지해야 합니다.  HLI 블레이드가 완전히 서비스 해제된 후에만 서비스를 종료합니다.

### <a name="remove-global-reach-for-hli"></a>HLI에 대한 Global Reach 제거 
Global Reach는 고객의 ExpressRoute 게이트웨이를 HLI ExpressRoute 게이트웨이와 연결하는 데 사용됩니다.  이를 통해 고객의 온-프레미스 트래픽이 프록시 서비스를 사용하지 않고 HLI 테넌트에 직접 도달할 수 있습니다.  이 연결은 마이그레이션 후 HLI 유닛이 없으면 더 이상 필요하지 않습니다.  IPTables 프록시 서비스의 경우와 마찬가지로 GlobalReach도 HLI 블레이드가 완전히 서비스 해제될 때까지 유지되어야 합니다.

### <a name="operating-system-subscription--movereuse"></a>운영 체제 구독 - 이동/재사용
VM 서버가 가동되고 HLI 블레이드가 서비스 해제되는 경우 OS 구독을 교체하거나 재사용하면 OS 라이선스 비용 이중 지불을 방지할 수 있습니다.



## <a name="next-steps"></a>다음 단계
아래 문서를 참조하세요.
- [Azure에서 SAP HANA 인프라 구성 및 작업](./hana-vm-operations.md)
- [Azure의 SAP 워크로드: 계획 및 배포 검사 목록](./sap-deployment-checklist.md)
