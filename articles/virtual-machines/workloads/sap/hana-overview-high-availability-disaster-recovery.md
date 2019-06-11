---
title: Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구 | Microsoft Docs
description: Azure(큰 인스턴스)에서 SAP HANA의 재해 복구 계획 및 고가용성 계획 수립
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b7cc1744ac285df8ed502256c30f77bdc8db81f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477586"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure의 SAP HANA 큰 인스턴스 고가용성 및 재해 복구 

>[!IMPORTANT]
>이 문서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체하지 않습니다. reader가 특히 백업, 복원, 고가용성 및 재해 복구의 항목에서 SAP HANA 관리 및 작업을 이해하고 전문 지식을 가졌다고 가정하겠습니다.

사용자 환경에서 수행되는 단계와 프로세스를 해당 HANA 버전 및 릴리스로 연습하는 것이 중요합니다. 이 문서에 설명된 일부 프로세스는 이해하기 쉽도록 간소화되었으며 최종 운영 핸드북의 자세한 단계로 사용하면 안 됩니다. 구성에 대한 작업 핸드북을 만들려는 경우 프로세스를 테스트 및 실행하고 특정 구성과 관련된 프로세스를 문서화해야 합니다. 


고가용성 및 DR(재해 복구)은 업무상 중요한 Azure의 SAP HANA(대규모 인스턴스) 서버를 실행하는 경우에 중요한 측면입니다. SAP, 시스템 통합업체 또는 Microsoft와 협의하여 적합한 고가용성 및 재해 복구 전략을 설계하고 구현하는 것이 중요합니다. 사용자 환경에 지정된 RPO(복구 지점 목표)와 복구 시간 목표를 고려하는 것도 중요합니다.

Microsoft에서는 HANA 큰 인스턴스를 통해 일부 SAP HANA 고가용성 기능을 지원합니다. 이러한 기능은 다음과 같습니다.

- **저장소 복제**: 다른 Azure 지역의 다른 HANA 큰 인스턴스 스탬프에 모든 데이터를 복제 하는 저장소 시스템의 기능입니다. SAP HANA는 이 메서드와 독립적으로 작동합니다. 이 기능은 HANA 대규모 인스턴스에 대해 제공되는 기본 재해 복구 메커니즘입니다.
- **HANA 시스템 복제**: 합니다 [SAP HANA의 모든 데이터의 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) 별도 SAP HANA 시스템으로 합니다. 복구 시간 목표는 정기적으로 데이터 복제를 통해 최소화됩니다. SAP HANA는 비동기 모드, 동기식 메모리 내 모드 및 동기 모드를 지원합니다. 동기 모드는 동일한 데이터 센터 내에 있거나 100km 미만의 거리에 있는 SAP HANA 시스템에만 사용됩니다. HANA 대규모 인스턴스 스탬프의 현재 디자인에서 HANA 시스템 복제는 한 지역 내의 고가용성을 위해서만 사용할 수 있습니다. HANA 시스템 복제에는 다른 Azure 지역으로 재해 복구를 구성하기 위해 타사 역방향 프록시 또는 라우팅 구성 요소가 필요합니다. 
- **호스트 자동 장애 조치**: HANA 시스템 복제의 대안인 SAP HANA에 대 한 로컬 오류 복구 솔루션입니다. 마스터 노드를 사용할 수 없게 되면 확장 모드에서 하나 이상의 대기 SAP HANA 노드를 구성하고 SAP HANA를 자동으로 대기 노드에 장애 조치합니다.

Azure의 SAP HANA(대규모 인스턴스)는 네 개의 지정학적 영역(미국, 오스트레일리아, 유럽 및 일본)에 있는 두 개의 Azure 지역에서 제공됩니다. HANA 대규모 인스턴스 스탬프를 호스팅하는 지정학적 영역 내의 두 지역을 별도의 전용 네트워크 회로에 연결합니다. 재해 복구 메서드를 제공하기 위해 저장소 스냅샷을 복제하는 데 이 기능을 사용합니다. 복제는 기본적으로 설정되지 않지만 재해 복구 기능을 주문한 고객에 대해 설정됩니다. 저장소 복제는 HANA 큰 인스턴스에 대한 저장소 스냅샷 사용 여부에 따라 달라집니다. 또한 다른 지리적 영역에 있는 DR 영역으로 Azure 지역을 선택할 수 없습니다. 

다음 표에는 현재 지원되는 고가용성 및 재해 복구 방법과 조합이 제공되어 있습니다.

| HANA 큰 인스턴스에서 지원되는 시나리오 | 고가용성 옵션 | 재해 복구 옵션 | 설명 |
| --- | --- | --- | --- |
| 단일 노드 | 사용할 수 없습니다. | 전용 DR 설치.<br /> 다목적 DR 설치. | |
| 호스트 자동 장애 조치 합니다. 수평 확장 (대기 없이 또는)<br /> 1+1 포함 | 활성 역할의 대기에서 가능.<br /> HANA에서 역할 전환 제어. | 전용 DR 설치.<br /> 다목적 DR 설치.<br /> 저장소 복제를 사용하여 DR 동기화. | HANA 볼륨 세트가 모든 노드에 연결됨.<br /> DR 사이트에는 동일한 수의 노드가 있어야 함. |
| HANA 시스템 복제 | 주 또는 보조 설치에서 가능.<br /> 장애 조치(failover)의 경우 보조가 주 역할로 전환.<br /> HANA 시스템 복제 및 OS 제어 장애 조치(Failover). | 전용 DR 설치.<br /> 다목적 DR 설치.<br /> 저장소 복제를 사용하여 DR 동기화.<br /> HANA 시스템 복제를 사용하는 DR은 아직 타사 구성 요소 없이 가능하지 않음. | 별도의 디스크 볼륨 세트가 각 노드에 연결됩니다.<br /> 프로덕션 사이트에 있는 보조 복제본의 디스크 볼륨만 DR 위치로 복제됩니다.<br /> DR 사이트에는 한 세트의 볼륨이 필요합니다. | 

전용 DR 설치는 DR 사이트의 HANA 큰 인스턴스 단위가 다른 워크로드 또는 비프로덕션 시스템을 실행하는 데 사용되지 않는 위치입니다. 이 단위는 수동이며 재해 장애 조치(failover)가 실행되는 경우에만 배포됩니다. 하지만 이 설치는 많은 고객이 선호하는 기본 선택 사항은 아닙니다.

아키텍처의 저장소 레이아웃 및 이더넷 세부 정보는 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.

> [!NOTE]
> 오버레이 시나리오로 구성된 [SAP HANA MCOD 배포](https://launchpad.support.sap.com/#/notes/1681092)(한 단위에 여러 개의 HANA 인스턴스가 있음)는 표에 나열된 HA 및 DR 방법으로 작동합니다. 단, Pacemaker 기반의 자동 장애 조치(failover) 클러스터에서 HANA 시스템 복제를 사용하는 경우는 예외입니다. 이 경우 단위당 하나의 HANA 인스턴스만 지원됩니다. [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) 배포에서 둘 이상의 테넌트가 배포된 경우 비저장소 기반 HA 및 DR 메서드만 작동합니다. 하나의 테넌트가 배포된 경우 나열된 모든 방법이 유효합니다.  

다목적 DR 설치는 DR 사이트의 HANA 큰 인스턴스 단위가 비프로덕션 워크로드를 실행하는 곳입니다. 재해가 발생하면 비프로덕션 시스템을 종료하고, 저장소 복제(추가) 볼륨 세트를 탑재한 다음, 프로덕션 HANA 인스턴스를 시작합니다. HANA 대규모 인스턴스 재해 복구 기능을 사용하는 대부분의 고객은 이 구성을 사용합니다. 


다음 SAP 문서에서 SAP HANA 고가용성에 대한 자세한 내용을 참조하세요. 

- [SAP HANA 고가용성 백서](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 관리 가이드](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA 시스템 복제에 대한 SAP HANA Academy 비디오](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 지원 참고 사항 #1999880 – SAP HANA 시스템 복제에 대한 FAQ](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP 지원 참고 사항 #2165547 – SAP HANA 시스템 복제 환경 내의 SAP HANA 백업 및 복원](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 지원 참고 사항 #1984882 – 가동 중지 시간이 최소/없는 하드웨어 Exchange에 대한 SAP HANA 시스템 복제 사용](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>HANA 큰 인스턴스를 사용한 재해 복구의 네트워크 고려 사항

HANA 대규모 인스턴스의 재해 복구 기능을 활용하려면 두 개의 Azure 지역에 네트워크 연결을 설계해야 합니다. 기본 Azure 지역의 온-프레미스에서 Azure ExpressRoute 회로 연결이 필요하고 온-프레미스에서 재해 복구 지역에 또 다른 회로 연결이 필요합니다. 이러한 방법은 MSEE(Microsoft Enterprise Edge 라우터) 위치를 포함하는 Azure 지역에 문제가 있는 상황을 다룹니다.

두 번째 방법으로 한 지역의 Azure(대규모 인스턴스)에서 SAP HANA에 연결된 모든 Azure 가상 네트워크를 다른 지역의 HANA 대규모 인스턴스를 연결하는 ExpressRoute 회로에 연결할 수 있습니다. 이러한 *교차 연결*을 사용하면 지역 1의 Azure 가상 네트워크에서 실행되는 서비스는 지역 2의 HANA 대규모 인스턴스 단위에 연결될 수 있으며 반대로도 가능합니다. 이 조치는 Azure와 온-프레미스 위치를 연결하는 MSEE 위치 중 하나가 오프라인 상태가 되는 경우에 문제를 해결합니다.

다음 그림은 재해 복구 사례에 대한 복원력 있는 구성을 보여줍니다.

![재해 복구에 대한 최적의 구성](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>재해 복구에 HANA 대규모 인스턴스 저장소 복제를 사용하는 경우 기타 요구 사항

HANA 대규모 인스턴스를 사용하는 재해 복구 설정에 대한 추가 요구 사항은 다음과 같습니다.

- 사용자의 프로덕션 SKU와 동일한 크기의 Azure의 SAP HANA(대규모 인스턴스) SKU를 주문하고 재해 복구 지역에 배포합니다. 현재 고객 배포에서는 비프로덕션 HANA 인스턴스를 실행하기 위해 이러한 인스턴스가 사용됩니다. 이러한 구성을 *다목적 DR 설치*라고 합니다.   
- 재해 복구 사이트에서 복구하려는 Azure의 SAP HANA(대규모 인스턴스) SKU 각각에 대해 DR 사이트의 추가 저장소를 주문합니다. 추가 저장소를 구입하면 저장소 볼륨을 할당할 수 있습니다. 프로덕션 Azure 지역의 저장소 복제 대상인 볼륨을 재해 복구 Azure 지역에 할당할 수 있습니다.
- 주 사이트에 HSR이 설치되어 있고 DR 사이트에 저장소 기반 복제를 설정한 경우, DR 사이트에서 추가 저장소를 구입해야 주 및 보조 노드 데이터가 모두 DR 사이트로 복제됩니다.

  **다음 단계**
- [백업 및 복원](hana-backup-restore.md)을 참조하세요.













