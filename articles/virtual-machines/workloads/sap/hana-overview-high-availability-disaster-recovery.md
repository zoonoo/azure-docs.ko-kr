---
title: "Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구 | Microsoft Docs"
description: "Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성을 달성하고 및 재해 복구를 계획합니다."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 4356e35609fa7a9727d8ad4f20fd18df01a05e84
ms.contentlocale: ko-kr
ms.lasthandoff: 08/30/2017

---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure(큰 인스턴스)의 SAP HANA 고가용성 및 재해 복구 

Azure(큰 인스턴스) 서버에서 업무상 중요한 SAP HANA를 실행할 때 고가용성 및 재해 복구는 중요한 측면입니다. SAP, 시스템 통합업체 또는 Microsoft와 협의하여 적합한 고가용성/재해복구 전략을 설계하고 구현하는 것이 중요합니다. 사용자 환경에 지정된 복구 지점 목표와 복구 시간 목표를 고려하는 것도 중요합니다.

Microsoft에서는 HANA 큰 인스턴스를 사용하여 일부 SAP HANA 고가용성 메서드를 "기본적으로" 지원합니다. 내용은 다음과 같습니다.

- **저장소 복제:** 다른 Azure 지역의 다른 HANA 큰 인스턴스 스탬프로 모든 데이터를 복제하는 저장소 시스템의 기능입니다. SAP HANA는 이 메서드와 독립적으로 작동합니다.
- **HANA 시스템 복제:** 별도 SAP HANA 시스템으로 SAP HANA에 있는 모든 데이터를 복제합니다. 복구 시간 목표는 정기적으로 데이터 복제를 통해 최소화됩니다. SAP HANA는 비동기 모드, 메모리 내 동기 모드 및 동기 모드를 지원합니다(동일한 데이터 센터 내에 있거나 100KM 이내에 있는 SAP HANA 시스템에만 권장됨). HANA 큰 인스턴스 스탬프의 현재 디자인에서 고가용성을 위해 HANA 시스템 복제만을 사용할 수 있습니다. 현재 네트워크 디자인에 따라, 다른 Azure 지역에 대한 재해 복구 구성을 위한 타사 역방향 프록시 구성 요소가 없으면 HANA 시스템 복제를 사용할 수 없습니다. 
- **자동 장애 조치 호스트:** SAP HANA에서 HANA 시스템 복제의 대안으로 사용할 로컬 오류 복구 솔루션입니다. 하나 이상의 대기 SAP HANA 노드를 확장 모드로 구성하고 마스터 노드를 사용할 수 없을 때 SAP HANA를 자동으로 대기 노드에 장애 조치합니다.

한편 세 개의 서로 다른 지리적 지역(미국 , 오스트레일리아 및 유럽)의 두 Azure 지역에 Azure(큰 인스턴스)의 SAP HANA가 제공됩니다. HANA 큰 인스턴스 스탬프를 호스트하는 두 개의 다른 지역이 재해 복구 방법을 제공하기 위해 저장소 스냅숏을 복제하는 데 사용되는 별도의 전용 네트워크 회로에 연결됩니다. 기본적으로 복제는 설정되지 않습니다. 재해 복구 기능을 주문한 고객을 위해 설정됩니다. 저장소 복제는 HANA 큰 인스턴스에 대한 저장소 스냅숏 사용 여부에 따라 달라집니다. 또한 다른 지리적 영역에 있는 DR 영역으로 Azure 지역을 선택할 수도 없습니다. 

다음 표에서는 현재 지원되는 고가용성 및 재해 복구 방법과 구성 조합을 제공합니다.

| HANA 큰 인스턴스에서 지원되는 시나리오 | 고가용성 옵션 | 재해 복구 옵션 | 설명 |
| --- | --- | --- | --- |
| 단일 노드 | 사용할 수 없음 | 전용 DR 설치<br /> 다목적 DR 설치 | |
| 호스트 자동 장애 조치(failover): N+m<br /> 1+1 포함 | 활성 역할의 대기에서 가능<br /> HANA에서 역할 전환 제어 | 전용 DR 설치<br /> 다목적 DR 설치<br /> 저장소 복제를 사용하여 DR 동기화 | HANA 볼륨 세트가 모든 노드에 연결(n+m)<br /> DR 사이트에는 동일한 수의 노드가 있어야 함 |
| HANA 시스템 복제 | 주/보조 설치에서 가능<br /> 장애 조치(failover)의 경우 보조 설치가 주 역할로 전환<br /> HANA 시스템 복제 및 OS에서 장애 조치(Failover) 제어 | 전용 DR 설치<br /> 다목적 DR 설치<br /> 저장소 복제를 사용하여 DR 동기화<br /> HANA 시스템 복제를 사용하는 DR은 타사 구성 요소 없이 가능하지 않음 | 각 노드에 연결된 별도의 디스크 볼륨 세트<br /> 프로덕션 사이트에 있는 보조 복제본의 디스크 볼륨만 DR 위치로 복제<br /> DR 사이트에 하나의 볼륨 세트 필요 | 

전용 DR 설치는 DR 사이트의 HANA 큰 인스턴스 단위가 다른 워크로드 실행 또는 비프로덕션 시스템에 사용되지 않는 설치입니다. 이 단위는 수동이며 재해 장애 조치(failover)가 실행될 경우의 작업 실행을 위해서만 배포됩니다. 지금까지 이러한 구성을 사용하는 고객은 한 번도 없었습니다.

다목적 DR 설치는 HANA 큰 인스턴스 단위가 비프로덕션 워크로드를 실행하는 DR 사이트의 설치를 나타냅니다. 비프로덕션 시스템이 종료되는 재해 발생의 경우 저장소 복제(추가) 볼륨 세트가 탑재되고 프로덕션 HANA 인스턴스가 시작됩니다. 지금까지 HANA 큰 인스턴스 재해 복구 기능을 사용하는 모든 고객은 구성을 대신 사용하고 있습니다. 


다음 SAP 문서에서 SAP HANA 고가용성에 대한 자세한 내용을 참조하세요. 

- [SAP HANA 고가용성 백서](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 관리 가이드](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA 시스템 복제에 대한 SAP Academy 비디오](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 지원 참고 사항 #1999880 – SAP HANA 시스템 복제에 대한 FAQ](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 지원 참고 사항 #2165547 – SAP HANA 시스템 복제 환경 내의 SAP HANA 백업 및 복원](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 지원 참고 사항 #1984882 – 가동 중지 시간이 최소/없는 하드웨어 Exchange에 대한 SAP HANA 시스템 복제 사용](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>HANA 큰 인스턴스를 사용한 재해 복구의 네트워크 고려 사항

HANA 큰 인스턴스의 재해 복구 기능을 활용하려면 두 개의 서로 다른 Azure 지역에 대한 네트워크 연결을 디자인하기 시작해야 합니다. 이를 위해 사용자의 기본 Azure 지역에 있는 온-프레미스에서 Azure ExpressRoute 회로 연결 및 온-프레미스에서 사용자의 재해 복구 지역에 다른 회로 연결이 필요합니다. 이러한 방법은 MSEE(Microsoft Enterprise Edge 라우터) 위치를 포함하는 전체 Azure 지역에 문제가 발생한 상황에 적용됩니다.

두 번째 방법으로 지역 중 하나에 있는 Azure(큰 인스턴스)의 SAP HANA에 연결된 모든 Azure VNet을 다른 지역의 HANA 큰 인스턴스를 연결하는 ExpressRoute 회로에 연결할 수 있습니다. 해당 '교차 연결'을 사용하여 지역 #1의 Azure VNet에서 실행되는 서비스는 지역 #2의 HANA 큰 인스턴스 단위에 연결될 수 있으며 반대 방향으로도 가능할 수 있습니다. 이 측정값은 Azure와 온-프레미스 위치를 연결하는 MSEE 위치 중 하나가 작동하지 않는 경우에 문제를 해결합니다.

아래의 그래픽은 재해 복구에 대한 복원력 있는 구성을 보여 줍니다.

![재해 복구에 대한 최적의 구성](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-for-using-hana-large-instances-storage-replication-for-disaster-recovery"></a>재해 복구에 HANA 큰 인스턴스 저장소 복제를 사용하기 위한 기타 요구 사항

HANA 큰 인스턴스를 사용하는 재해 복구 설치에 대한 추가 요구 사항은 다음과 같습니다.

- 사용자의 프로덕션 SKU와 동일한 크기인 Azure(큰 인스턴스)의 SAP HANA를 주문하고 재해 복구 지역에서 배포해야 합니다. 지금까지 고객 배포에서는 비프로덕션 HANA 인스턴스를 실행하기 위해 이러한 인스턴스가 사용되고 있습니다. 이러한 설치를 다목적 DR 설치라고 합니다.   
- 필요한 경우 재해 복구 사이트에서 복구하려는 Azure(큰 인스턴스)의 SAP HANA SKU 각각에 대해 DR 사이트의 추가 저장소를 주문해야 합니다. 이 작업으로 인해 프로덕션 Azure 지역에서 재해 복구 Azure 지역까지 저장소 복제의 대상인 저장소 볼륨을 할당하게 됩니다.

재해 복구에 대한 자세한 내용은 문서의 마지막 장에 나와 있습니다.
 

## <a name="backup-and-restore"></a>백업 및 복원

운영 데이터베이스에 대한 가장 중요한 측면 중 하나는 다양한 치명적인 이벤트에서 데이터베이스를 보호할 수 있다는 점입니다. 자연 재해부터 간단한 사용자 오류까지 다양한 원인으로 인해 이러한 이벤트가 발생할 수 있습니다.

지정 시점(예: 누군가가 중요한 데이터를 삭제하기 전에)으로 복원하는 기능을 사용하여 데이터베이스를 백업하면 중단이 발생하기 전의 방식에 가장 가까운 상태로 복원할 수 있습니다.

최상의 결과를 위해 두 가지 백업을 수행해야 합니다.

- 데이터베이스 백업 - 전체, 증분 또는 차등 백업
- 트랜잭션 로그 백업

응용 프로그램 수준에서 수행되는 전체 데이터베이스 백업 외에도 저장소 스냅숏에 백업을 수행하여 더욱 철저해질 수 있습니다. 하지만 저장소 스냅숏은 트랜잭션 로그 백업을 대체하지 않습니다. 트랜잭션 로그 백업은 데이터베이스를 특정 시점으로 복원하는 데 중요하며 이미 커밋된 트랜잭션에서 로그를 비우게 됩니다. 하지만 저장소 스냅숏은 데이터베이스의 롤포워드 이미지를 빠른 방으로 제공하여 복구를 가속화할 수 있습니다. 

Azure(큰 인스턴스)의 SAP HANA는 다음과 같은 두 개의 백업과 옵션을 제공합니다.

- DIY(Do It Yourself) 계산하여 디스크 공간이 충분한지 확인한 후에는 HANA 큰 인스턴스 단위에 연결된 볼륨에 직접 또는 Azure VM에 설정된 NFS 공유에 대해 디스크 백업 방법을 사용하여 전체 데이터베이스 및 로그 백업을 수행합니다. 후자의 경우 고객은 Azure에서 Linux VM을 설정하고, VM에 Azure Storage를 연결하고, 해당 VM의 구성된 NFS 서버를 통해 저장소를 공유합니다. HANA 큰 인스턴스 단위에 직접 연결된 볼륨에 대해 백업을 수행하는 경우 백업을 Azure Storage 계정에 복사하거나(Azure Storage를 기준으로 NFS 공유를 내보내는 Azure VM을 설정한 후) Azure Backup Vault 또는 Azure 콜드 저장소를 사용합니다. 또 다른 옵션은 백업을 Azure Storage 계정에 복사한 후에 저장하기 위해 타사 데이터 보호 도구를 사용하는 것입니다. DIY 백업 옵션은 준수 및 감사 목적으로 인해 장기간 저장되어야 하는 데이터에 필요할 수도 있습니다. 모든 경우에 백업은 VM 및 Azure Storage를 통해 표시되는 NFS 공유에 복사됩니다.
- Azure(큰 인스턴스)의 SAP HANA 기본 인프라가 제공하는 백업 및 복원 기능을 사용합니다. 이 옵션은 백업 및 빠른 복원에 대한 필요를 충족합니다. 이 섹션의 나머지 부분에서는 HANA 큰 인스턴스와 함께 제공되는 백업 및 복원 기능을 설명합니다. 또한 HANA 큰 인스턴스에서 제공하는 재해 복구 기능과의 관계도 설명합니다.

> [!NOTE]
> HANA 큰 인스턴스의 기본 인프라에서 사용하는 스냅숏 기술은 SAP HANA 스냅숏에 대한 종속성을 갖습니다. 현재, SAP HANA 스냅숏은 SAP HANA 다중 테넌트 데이터베이스 컨테이너의 다중 테넌트와 함께 작동하지 않습니다. 따라서 SAP HANA 다중 테넌트 데이터베이스 컨테이너에서 여러 테넌트를 배포한 경우에는 이 백업 방법을 사용할 수 없습니다. 그렇지만 테넌트가 하나만 배포된 경우에는 이 방법이 가능합니다.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA 저장소 스냅숏 사용

Azure(큰 인스턴스)의 SAP HANA에 기반한 저장소 인프라는 볼륨 저장소 스냅숏의 개념을 지원합니다. 볼륨의 백업 및 복원이 모두 지원되며 다음 사항을 고려해야 합니다.

- 전체 데이터베이스 백업 대신 저장소 볼륨 스냅숏을 자주 사용합니다.
- /hana/data, 'hana/log 및 /hana/shared(/usr/sap 포함) 볼륨을 통해 스냅숏을 트리거할 때 저장소 스냅숏은 저장소 스냅숏을 실행하기 전에 SAP HANA 스냅숏을 시작합니다. 이 SAP HANA 스냅숏은 저장소 스냅숏을 복구한 후에 최종 로그 복원에 대한 설치 지점입니다.
- 저장소 스냅숏이 성공적으로 실행된 후에 SAP HANA 스냅숏이 삭제됩니다.
- 트랜잭션 로그 백업은 자주 사용되며 /hana/logbackups 볼륨 또는 Azure에 저장됩니다. 트랜잭션 로그 백업을 포함하는 /hana/logbackups 볼륨은 별도로 스냅숏을 실행하도록 트리거될 수 있습니다. 이 경우 HANA 스냅숏을 실행할 필요가 없습니다.
- 데이터베이스를 특정 시점으로 복원해야 하는 경우 특정 저장소 스냅숏을 복원(예: 샌드박스 시스템을 원래 상태로의 계획된 복원)하도록 Microsoft Azure 지원(프로덕션 작동 중단) 또는 Azure의 SAP HANA Service Management에 대한 요청이 이루어집니다.
- 저장소 스냅숏에 포함된 SAP HANA 스냅숏은 저장소 스냅숏을 만든 후에 실행되고 저장된 트랜잭션 로그 백업에 적용하는 오프셋 지점입니다.
- 이러한 트랜잭션 로그 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원합니다.

고객은 다음과 같은 세 가지 서로 다른 볼륨 클래스를 대상으로 하는 저장소 스냅숏을 수행할 수 있습니다.

- /hana/data, /hana/log 및 /hana/shared(/usr/sap 포함)에 대해 함께 결합된 스냅숏. 이 스냅숏을 사용하려면 SAP HANA 스냅숏을 실행해야 합니다.
- /Hana/logbackups에 대한 별도 스냅숏
- OS 파티션(유형 1 HANA 큰 인스턴스의 경우만 해당)


### <a name="storage-snapshot-considerations"></a>저장소 스냅숏 고려 사항

>[!NOTE]
>저장소 스냅숏은 HANA 큰 인스턴스 단위에 할당된 저장소 공간을 사용합니다. 따라서 저장소 스냅숏을 예약하거나 저장소 스냅숏 수를 유지할 경우 아래 측면을 고려해야 합니다. 

Azure(큰 인스턴스)의 SAP HANA에 대한 저장소 스냅숏의 특정 역학에는 다음이 포함됩니다.

- 만들어진 시점에서 특정 저장소 스냅숏은 작은 저장소를 사용합니다.
- SAP HANA 데이터 파일의 데이터 콘텐츠 변경 내용 및 콘텐츠는 저장소 볼륨에서 변경되고 스냅숏은 원래 블록 콘텐츠를 저장해야 합니다.
- 저장소 스냅숏의 크기가 증가합니다. 스냅숏이 오래 존재할수록 저장소 스냅숏은 커집니다.
- 스토리지 스냅샷의 수명 동안 SAP HANA 데이터베이스 볼륨에 변경 내용이 많을수록 스토리지 스냅샷이 소진하는 공간이 커집니다.

Azure(큰 인스턴스)의 SAP HANA는 SAP HANA 데이터 및 로그 볼륨에 대한 고정된 볼륨 크기로 제공됩니다. 이러한 볼륨의 스냅숏을 수행하면 볼륨 공간이 줄어듭니다. 저장소 스냅숏을 예약하고, 저장소 볼륨의 공간 소비를 모니터링하고, 저장되는 스냅숏 수를 관리하는 것은 사용자의 책임입니다. 대량의 데이터를 가져오거나 HANA 데이터베이스에 대해 다른 중요한 변경을 수행하기 전에 저장소 스냅숏을 사용하지 않도록 설정하기로 결정할 수 있는 것도 사용자 뿐입니다. 


다음 섹션에서는 일반적인 권장 사항을 비롯한 이러한 스냅숏을 수행하는 방법에 대한 정보를 제공합니다.

- 하드웨어가 볼륨당 255개의 스냅숏을 유지할 수 있지만 이 숫자보다 훨씬 낮게 유지하는 것이 좋습니다.
- 스냅숏 저장소를 수행하기 전에 여유 공간을 모니터링하고 추적합니다.
- 사용 가능한 공간에 따라 저장소 스냅숏의 수를 줄입니다. 유지할 수 있는 스냅숏 수를 줄이거나 볼륨을 확장해야 할 수 있습니다. (추가 저장소를 1TB 단위로 주문할 수 있습니다.)
- SAP 플랫폼 마이그레이션 도구(R3load)를 사용하여 데이터를 SAP HANA로 이동하거나 백업에서 SAP HANA에 데이터를 복원하는 것과 같은 작업 중에 /hana/data 볼륨에 대해 저장소 스냅숏을 사용하지 않도록 설정해야 합니다. 
- SAP HANA 테이블을 크게 재구성하는 동안 저장소 스냅숏을 가능하면 사용하지 않아야 합니다.
- 저장소 스냅숏은 Azure(큰 인스턴스)의 SAP HANA 중 재해 복구 기능을 사용하기 위한 필수 구성 요소입니다.

### <a name="setting-up-storage-snapshots"></a>저장소 스냅숏 설정

HANA 큰 인스턴스를 사용하여 저장소 스냅숏을 설정하는 대략적인 단계는 다음과 같습니다.
1. Perl이 HANA(큰 인스턴스) 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다.
2. /etc/ssh/ssh\_config를 수정하여 _MACs hmac-sha1_ 줄을 추가합니다.
3. 적용할 수 있는 경우 실행하는 SAP HANA 인스턴스마다 마스터 노드에서 SAP HANA 백업 사용자 계정을 만듭니다.
4. SAP HANA HDB 클라이언트는 모든 SAP HANA(큰 인스턴스) 서버에 설치되어야 합니다.
5. 각 지역의 첫 번째 SAP HANA(큰 인스턴스) 서버에서 스냅숏 생성을 제어하는 기본 저장소 인프라에 액세스하기 위해 공개 키를 만들어야 합니다.
6. [이 github 위치](https://github.com/Azure/hana-large-instances-self-service-scripts)의 스크립트 및 구성 파일을 SAP HANA 설치의 **hdbsql** 위치에 복사합니다.
7. 적절한 고객 사양에 대한 필요에 따라 HANABackupDetails.txt 파일을 수정합니다.

### <a name="step-1-install-sap-hana-hdbclient"></a>1단계: SAP HANA HDBClient 설치

Azure(큰 인스턴스)의 SAP HANA에 설치된 Linux 운영 체제는 백업 및 재해 복구를 위해 SAP HANA 저장소 스냅숏을 실행하는 데 필요한 폴더와 스크립트를 포함합니다. [github](https://github.com/Azure/hana-large-instances-self-service-scripts)에서 최신 릴리스를 확인하세요. 스크립트의 가장 최신 릴리스 버전은 2.0입니다.
그러나 SAP HANA를 설치하는 동안 HANA 큰 인스턴스 단위에 SAP HANA HDBclient를 설치하는 것도 사용자의 책임입니다. (Microsoft에서는 HDBclient나 SAP HANA를 모두 설치하지 않습니다.)

### <a name="step-2-change-etcsshsshconfig"></a>2단계: /etc/ssh/ssh\_config 변경

여기에 표시된 _MACs hmac-sha1_ 줄을 추가하여 /etc/ssh/ssh\_config를 변경합니다.
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>3단계: 공개 키 만들기

HANA 큰 인스턴스 테넌트의 저장소 스냅숏 인터페이스에 대한 액세스를 사용하도록 설정하려면 공개 키를 통해 로그인을 설정해야 합니다. 테넌트의 첫 번째 Azure(큰 인스턴스)의 SAP HANA 서버에서 스냅숏을 만들 수 있도록 저장소 인프라에 액세스하는 데 사용되는 공개 키를 만들어야 합니다. 공개 키를 통해 저장소 스냅숏 인터페이스에 로그인하는 데 암호를 요구하지 않도록 하면 해당 암호 자격 증명이 유지되지 않습니다. SAP HANA(큰 인스턴스) 서버의 Linux에서 공개 키를 생성하려면 다음 명령을 실행합니다.
```
  ssh-keygen –t dsa –b 1024
```
새 위치는 _/root/.ssh/id\_dsa.pub입니다. 실제 암호를 입력하지 마세요. 그렇지 않으면 로그인할 때마다 암호를 입력해야 합니다. 대신, **Enter** 키를 두 번 눌러서 로그인에 대한 ‘입력 암호’ 요구 사항을 제거합니다.

폴더를 /root/.ssh/로 변경한 다음 **ls** 명령을 실행하여 예상 대로 공개 키가 수정되었는지 확인해야 합니다. 키가 있는 경우 다음 명령을 실행하여 복사할 수 있습니다.

![이 명령을 실행하여 공개 키를 복사합니다.](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

이 시점에서 Azure Service Management의 SAP HANA에 문의하고 공개 키를 제공합니다. 서비스 담당자는 공개 키를 사용하여 HANA 큰 인스턴스 테넌트에 대해 획득되는 기본 저장소 인프라에 등록합니다.

### <a name="step-4-create-an-sap-hana-user-account"></a>4단계: SAP HANA 사용자 계정 만들기

SAP HANA 스냅숏 만들기를 시작할 수 있으려면 저장소 스냅숏 스크립트에서 사용할 수 있는 SAP HANA에 사용자를 만들어야 합니다. 이러한 목적으로 SAP HANA Studio 내에서 SAP HANA 사용자 계정을 만듭니다. 이 계정에는 _백업 관리_ 및 _카탈로그 읽기_ 권한이 있어야 합니다. 이 예제에서는 사용자 이름 SCADMIN을 만듭니다. HANA Studio에서 만든 사용자 계정 이름은 대/소문자를 구분합니다.  ‘아니요’를 클릭하여 사용자에게 다음 로그인 시 암호를 변경하도록 요구합니다.

![HANA Studio에서 사용자 만들기](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>5단계: SAP HANA 사용자 계정 권한 부여

이 단계에서는 스크립트가 런타임 시 암호를 제출하지 않아도 되도록 사용자가 만든 SAP HANA 사용자 계정에 권한을 부여해야 합니다. SAP HANA 명령 `hdbuserstore`을 사용하면 하나 이상의 SAP HANA 노드에 저장되는 SAP HANA 사용자 키를 만들 수 있습니다. 사용자 키를 사용하면 나중에 설명할 스크립팅 프로세스 내에서 암호를 관리하지 않고도 SAP HANA에 액세스할 수 있습니다.

>[!IMPORTANT]
>다음 명령을 `root`로 실행합니다. 그렇지 않으면 스크립트가 제대로 작동할 수 없습니다.

`hdbuserstore` 명령을 다음과 같이 입력합니다.

![hdbuserstore 명령을 입력합니다.](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

사용자가 SCADMIN01이고, 호스트 이름이 lhanad01이고, 인스턴스 번호가 01인 다음 예제에서 명령은 다음과 같습니다.
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
SAP HANA 확장 구성이 있는 경우 단일 서버에서 모든 스크립팅을 관리해야 합니다. 이 예제에서는 키에 관련된 호스트를 반영하는 방식으로 각 호스트에 대해 SAP HANA 키 SCADMIN01를 변경해야 합니다. 즉, SAP HANA 백업 계정은 HANA DB의 인스턴스 번호를 사용하여 수정됩니다. 키는 할당된 호스트에 대한 관리자 권한이 있어야 하며, 확장에 대한 백업 사용자는 모든 SAP HANA 인스턴스에 대한 액세스 권한이 있어야 합니다. 이름이 lhanad01, lhanad02 및 lhanad03인 3개의 확장 노드가 있는 경우 명령 시퀀스는 다음과 같습니다.

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad02:30215 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad03:30315 SCADMIN <password>
```

### <a name="step-6-get-snapshot-scripts-configure-snapshots-and-test-configuration-and-connectivity"></a>6단계: 스냅숏 스크립트 가져오기, 스냅숏 구성, 구성 및 연결 테스트

[여기](https://github.com/Azure/hana-large-instances-self-service-scripts)에서 최신 버전의 스크립트를 다운로드합니다. 다운로드한 스크립트 및 텍스트 파일을 **hdbsql**에 대한 작업 디렉터리에 복사합니다. 현재 HANA 설치의 경우 이 디렉터리는 /hana/shared/D01/exe/linuxx86\_64/hdb와 같습니다.
```
azure\_hana\_backup.pl
azure\_hana\_replication\_status.pl
azure\_hana\_snapshot\_details.pl
azure\_hana\_snapshot\_delelte.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```

다른 스크립트 및 파일의 용도는 다음과 같습니다.

- **azure\_hana\_backup.pl**: 이 스크립트는 HANA data/log/shared 볼륨, /hana/logbackups 볼륨 또는 OS(HANA 큰 인스턴스의 유형 I SKU)에서 저장소 스냅숏이 실행되도록 하기 위해 cron을 통해 예약해야 합니다.
- **azure\_hana\_replication\_status.pl**: 이 스크립트는 프로덕션 사이트에서 재해 복구 사이트로 복제 상태에 대한 기본 정보를 제공하도록 디자인되었습니다.  이 스크립트는 복제가 발생하고 있음을 모니터링하고 복제되는 항목의 크기를 표시하는 데 사용됩니다.  또한 복제 너무 오래 걸리거나 연결이 잠재적으로 다운될 경우에 대한 지침을 제공합니다.
- **azure\_hana\_snapshot\_details.pl**: 이 스크립트의 목적은 고객에게 환경에 존재하는 볼륨별 모든 스냅숏에 대한 기본적인 정보 목록을 제공하는 것입니다. 이 스크립트는 주 서버 또는 재해 복구 위치의 서버 단위에서 실행할 수 있습니다. 이 스크립트는 스냅숏이 포함된 볼륨별로 분석된 다음 정보를 제공합니다. 즉, 볼륨의 총 스냅숏 크기를 제공한 다음 해당 볼륨의 각 스냅숏에 대해 스냅숏 이름, 만든 시간, 스냅숏의 크기, 스냅숏의 빈도 및 해당 스냅숏과 연결된 HANA 백업 ID(해당되는 경우)를 제공합니다.
- **azure\_hana\_snapshot\_delelte.pl**: 이 스크립트는 HANA Studio에서 확인된 SAP HANA 백업 ID를 사용하거나 저장소 스냅숏 이름 자체를 사용하여 저장소 스냅숏 또는 스냅숏 집합을 삭제하도록 디자인되었습니다.  현재, 백업 ID는 HANA data/log/shared 볼륨에 대해 생성된 스냅숏에만 연결됩니다.  그렇지 않은 경우 스냅숏 ID를 입력하는 경우 입력한 스냅숏 ID와 일치하는 모든 스냅숏을 찾습니다.  
- **testHANAConnection.pl**: 이 스크립트는 SAP HANA 인스턴스에 대한 연결을 테스트하며, 저장소 스냅숏을 설정하는 데 필요합니다.
- **testStorageSnapshotConnection.pl**: 이 스크립트는 두 가지 용도로 사용됩니다. 첫째, 스크립트를 실행하는 HANA 큰 인스턴스 단위가 할당된 저장소 가상 컴퓨터 및 HANA 큰 인스턴스의 저장소 스냅숏 인터페이스에 대해 액세스 권한을 가지도록 하는 데 필요합니다. 둘째, 테스트하려는 HANA 인스턴스에 대한 임시 스냅숏을 만드는 데 필요합니다. 이 스크립트는 백업 스크립트가 예상대로 작동되도록 하기 위해 서버의 모든 HANA 인스턴스에 대해 실행해야 합니다.
- **removeTestStorageSnapshot.pl**: 이 스크립트는 스크립트 testStorageSnapshotConnection.pl을 사용하여 만든 테스트 스냅숏을 삭제하는 데 사용합니다. 
- **HANABackupCustomerDetails.txt**: SAP HANA 구성에 맞게 수정해야 하는 수정 가능한 구성 파일입니다.

 
HANABackupCustomerDetails.txt 파일은 저장소 스냅숏을 실행하는 스크립트의 제어 및 구성 파일입니다. 사용자의 목적 및 설치에 맞게 파일을 조정해야 합니다. 사용자의 인스턴스를 배포한 Azure의 SAP HANA Service Management에서 _저장소 백업 이름_ 및 _저장소 IP 주소_를 수신했어야 합니다. 이 파일에서는 변수의 시퀀스, 순서 또는 간격을 수정할 수 없습니다. 수정하면 스크립트가 제대로 실행되지 않습니다. 또한 Azure Service Management의 SAP HANA에서 확장 노드 또는 마스터 노드의 IP 주소(확장의 경우)를 수신했을 것입니다. SAP HANA를 설치한 후에는 HANA 인스턴스 번호를 알고 있을 것이며 백업 이름을 구성 파일에 추가해야 합니다.

강화 또는 규모 확장 배포의 경우 저장소 백업 이름과 저장소 IP 주소를 입력한 후 구성 파일이 아래와 같을 수 있습니다. 구성 파일에 입력해야 하는 추가 데이터는 단일 노드 또는 마스터 노드의 IP 주소, HANA 인스턴스 번호 및 선택할 수 있는 백업 이름입니다.
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA Backup Name:
```

>[!NOTE]
>현재 노드 1 세부 정보만 실제 HANA 저장소 스냅숏 스크립트에 사용됩니다. 마스터 백업 노드가 변경되는 경우 노드 1의 세부 정보를 수정하여 다른 노드가 해당 위치를 사용할 수 있도록 모든 HANA 노드 간의 액세스를 테스트하는 것이 좋습니다.

HANABackupCustomerDetails.txt 파일에 모든 구성 데이터를 추가한 후에는 스크립트 testHANAConnection.pl을 사용하여 HANA 인스턴스 데이터와 관련해서 구성이 올바른지 여부를 확인해야 합니다. 이 스크립트는 SAP HANA 강화 또는 규모 확장과는 별개입니다.

```
testHANAConnection.pl
```

SAP HANA 규모 확장 구성이 있는 경우 마스터 HANA 인스턴스에서 필요한 모든 HANA 서버 및 인스턴스에 액세스할 수 있어야 합니다. 테스트 스크립트에 대한 매개 변수가 없지만 이 스크립트를 적절하게 실행하려면 HANABackupCustomerDetails.txt 구성 파일에 대한 데이터 추가를 완료해야 합니다. 셸 명령 오류 코드만 반환되기 때문에 스크립트가 모든 인스턴스의 오류를 검사하는 것은 불가능합니다. 이런 경우에도 스크립트는 다시 확인하기 위한 몇 가지 유용한 설명을 제공하지 않습니다.

스크립트를 실행하려면
```
 ./testHANAConnection.pl
```
스크립트가 HANA 인스턴스의 상태를 가져오는 데 성공한 경우 HANA 연결이 성공적이라는 메시지를 출력합니다.


다음 테스트 단계는 HANABackupCustomerDetails.txt 구성 파일에 추가한 데이터를 기준으로 저장소에 대한 연결을 확인하고 테스트 스냅숏으로 실행하는 것입니다.  azure\_hana\_backup.pl 스크립트를 실행하기 전에 다음 테스트를 실행해야 합니다. 볼륨에 스냅숏이 없는 경우 볼륨이 비어 있는지 아니면 스냅숏 정보를 포함한 ssh 오류가 있는지를 결정하는 것이 불가능합니다. 이러한 이유로 스크립트는 다음 두 단계를 실행합니다.

- 스크립트에서 스냅숏을 실행할 수 있도록 테넌트의 저장소 가상 컴퓨터 및 인터페이스에 액세스할 수 있는지 확인합니다.
- HANA 인스턴스에서 각 볼륨의 테스트, 더미 또는 스냅숏을 만듭니다.

이러한 이유로 HANA 인스턴스를 인수로 포함합니다. 실행이 실패하는 경우 저장소 연결에 대한 오류 검사를 제공할 수 없습니다. 그렇지만 스크립트는 이러한 경우에 유용한 힌트를 제공합니다.

스크립트는 다음의 경우 실행됩니다.
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
다음 단계로 이 스크립트는 이전에 설치 단계에 제공된 공개 키와 HANABackupCustomerDetails.txt에 구성된 데이터를 사용하여 저장소에 로그인하려고 합니다. 성공하면 다음 콘텐츠가 표시됩니다.

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

문제가 발생하는 경우 저장소 콘솔에 연결하면 다음과 같은 출력이 표시됩니다.

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

저장소 가상 컴퓨터 인프라에 성공적으로 로그인한 후 스크립트는 단계 #2를 계속 진행하고 SAP HANA의 3 노드 규모 확장 구성에 대해 여기에 표시된 것과 같은 테스트 스냅숏을 만듭니다.

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

이 스크립트를 사용하여 테스트 스냅숏을 성공적으로 실행한 경우 실제 저장소 스냅숏 구성을 진행할 수 있습니다. 실패하는 경우 계속하기 전에 문제를 조사합니다. 만든 테스트 스냅숏은 첫 번째 실제 스냅숏이 완료될 때까지 그대로 두어야 합니다.



### <a name="step-7-perform-snapshots"></a>7단계: 스냅숏 수행

모든 준비 단계를 완료하면 실제 저장소 스냅숏 구성을 시작할 수 있습니다. 예약할 스크립트는 SAP HANA 강화 및 규모 확장 구성에 작동합니다. cron을 통해 스크립트 실행을 예약합니다. 

수행할 수 있는 세 가지 유형의 스냅숏 백업은 다음과 같습니다.
- HANA: 조정된 스냅숏에 hana/data, /hana/log, /hana/shared(/usr/sap도 포함)가 들어 있는 볼륨이 포함되는 조합된 스냅숏 백업. 이 스냅숏에서 단일 파일을 복원할 수 있습니다.
- Logs: /hana/logbackups 볼륨의 스냅숏 백업. 이 저장소 스냅숏을 실행하기 위해 트리거되는 HANA 스냅숏은 없습니다. 이 저장소 볼륨은 SAP HANA 트랜잭션 로그 백업을 포함하기 위한 볼륨입니다. 이러한 로그 백업은 로그 증가를 제한하고 잠재적인 데이터 손실을 방지하기 위해 좀 더 자주 수행됩니다. 이 스냅숏에서 단일 파일을 복원할 수 있습니다. 해당 빈도를 5분 미만으로 낮추지 않아야 합니다.
- Boot: HANA 큰 인스턴스의 부팅 LUN을 포함하는 볼륨의 스냅숏. 이 스냅숏 백업은 HANA 큰 인스턴스의 유형 I SKU에서만 가능합니다. 부팅 LUN을 포함하는 볼륨의 스냅숏에서 단일 파일 복원을 수행할 수 없습니다.  


이러한 세 가지 다른 유형의 스냅숏에 대한 호출 구문은 다음과 같습니다.
```
HANA backup covering /hana/data, /hana/log, /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot manual 30

```

다음 매개 변수를 지정해야 합니다.

- 첫 번째로 지정할 매개 변수는 스냅숏 백업의 유형을 지정합니다. 허용되는 값에는 'hana', 'logs' 및 'boot'가 있습니다.
- 두 번째 값은 HANA SID(예: HM3)입니다. 부팅 볼륨의 백업을 수행하는 데는 이 매개 변수가 필요하지 않습니다.
- 스냅숏의 세 번째 매개 변수는 스냅숏 유형에 대한 백업 레이블입니다. 특정 유형 또는 빈도의 예약된 스냅숏에 대해서는 동일하게 유지됩니다.
- 네 번째 매개 변수는 유지할 동일한 스냅숏 접두사(레이블)를 갖는 스냅숏 수를 정의하여 간접적으로 스냅숏의 보존을 정의합니다. 이 매개 변수는 cron 통해 예약된 실행에 중요합니다. 

규모 확장의 경우 스크립트는 모든 HANA 서버에 액세스할 수 있도록 추가 검사를 수행하고, 모든 HANA 인스턴스는 SAP HANA 스냅숏을 만든 후 저장소 스냅숏을 계속 만들기 전에 적절한 인스턴스 상태를 반환합니다.

스크립트 azure\_hana\_backup.pl을 실행하면 이러한 세 가지 단계에서 저장소 스냅숏이 만들어집니다.

- SAP HANA 스냅숏을 실행합니다.
- 저장소 스냅숏을 실행합니다.
- 저장소 스냅숏을 실행하기 전에 생성된 SAP HANA 스냅숏을 제거합니다.

스크립트가 복사되었던 HDB 실행 파일 폴더에서 호출하여 실행합니다. 

보존 기간은 스크립트를 실행할 경우 매개 변수로 제출되는 스냅숏 개수와 함께 관리됩니다(예: 이전에 표시된 30). 따라서 저장소 스냅숏에 해당되는 기간은 실행 기간 및 스크립트 실행 시 매개 변수로 제출된 스냅숏 수의 함수입니다. 유지되는 스냅숏 개수가 스냅숏 스크립트의 호출에서 매개 변수로 이름이 지정된 수를 초과하면 새 스냅숏을 실행하기 전에 동일한 레이블의 가장 오래된 저장소가 삭제됩니다(앞의 경우 _수동_). 즉, 호출의 마지막 매개 변수로 지정한 숫자는 유지되는 스냅숏 개수를 제어하는 데 사용할 수 있는 숫자입니다. 이 매개 변수를 사용하여 스냅숏에 사용되는 디스크 공간도 간접적으로 제어할 수 있습니다. 

>레이블을 변경하면 즉시 계수가 다시 시작됩니다. 레이블을 엄격하게 지정해야 한다는 의미입니다.

### <a name="snapshot-strategies"></a>스냅숏 전략
다른 유형에 대한 스냅숏 빈도는 HANA 큰 인스턴스 재해 복구 기능을 사용하는지 여부에 따라 달라집니다. HANA 큰 인스턴스의 재해 복구 기능은 저장소 스냅숏에 의존하므로 저장소 스냅숏의 빈도 및 실행 기간과 관련해서 몇 가지 특수한 권장 지침이 적용될 수 있습니다. 아래 제공되는 고려 사항 및 권장 사항에서는 HANA 큰 인스턴스가 제공하는 재해 복구 기능은 사용하지 않는다고 가정합니다. 대신, 저장소 스냅숏을 백업을 보유하기 위한 수단으로 사용하며, 지난 30일 동안의 지정 시간 복구를 제공할 수 있다고 가정합니다. 스냅숏 수와 공간이 제한된다고 가정할 경우 배포된 고객은 다음과 같은 요구 사항을 고려했습니다.

- 지정 시간 복구에 대한 복구 시간 목표의 요구 사항
- 공간 사용
- 잠재적 재해 복구를 위한 복구 지점 목표 및 복구 시간 목표의 요구 사항
- 디스크에 대한 HANA 전체 데이터베이스 백업의 최종 실행 디스크 또는 _backint_ 인터페이스에 대한 전체 데이터베이스 백업이 실행되는 경우 저장소 스냅숏의 실행에 실패합니다. 저장소 스냅숏을 기반으로 전체 데이터베이스 백업을 실행하려는 경우 이 시간 동안 저장소 스냅숏이 실행되지 않는지 확인합니다.
- 볼륨당 스냅숏 수가 255로 제한됩니다.


HANA 큰 인스턴스의 재해 복구 기능을 사용하지 않는 고객의 경우 스냅숏 기간이 덜 빈번합니다. 이러한 경우 고객이 12시간 또는 24시간 기간으로 /hana/data, /hana/log, /hana/shared(/usr/sap 포함)에 대해 결합된 스냅숏을 수행하고, 1달 동안 해당 스냅숏을 유지하는 것을 알 수 있습니다. 로그 백업 볼륨의 스냅숏과 동일합니다. 반면 로그 백업 볼륨에 대한 SAP HANA 트랜잭션 로그 백업은 5분~15분 기간에 실행됩니다.

cron를 사용하여 예약된 저장소 스냅숏을 수행하는 것이 좋으며, 모든 백업 및 재해 복구 요구 사항에 동일한 스크립트를 사용하는 것이 좋습니다(요청된 다른 백업 시간과 일치하도록 스크립트 입력 수정). 이러한 스냅숏은 해당 실행 시간(매시간, 12시간, 매일 또는 매주)에 따라 cron에서 모두 다르게 예약됩니다. 

/etc/crontab의 cron 일정 예제는 다음과 같을 수 있습니다.
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
위 예제에서는 /hana/data, /hana/log, /hana/shared(/usr/sap 포함) 위치가 있는 볼륨을 포괄하는 시간별로 결합된 스냅숏이 있습니다. 이 유형의 스냅숏은 2일 이내의 더 빠른 지정 시간 복구에 사용됩니다. 또한 해당 볼륨에 대한 일별 스냅숏이 있습니다. 따라서 2일 간의 시간별 스냅숏과 4주 간의 일별 스냅숏을 보유하게 되었습니다. 그뿐 아니라 트랜잭션 로그 백업 볼륨은 매일 한 번 백업됩니다. 또한 이러한 백업은 4주 동안 유지됩니다. crontab의 세 번째 줄에 표시된 대로 HANA 트랜잭션 로그의 백업은 5분마다 실행되도록 예약됩니다. 또한 저장소 스냅숏을 실행하는 여러 다른 cron 작업의 시간(분)이 지연되므로 해당 스냅숏이 특정 시점에 한꺼번에 모두 실행되는 경우는 없다는 것도 알게 됩니다. 

아래 예제에서는 /hana/data, /hana/log, /hana/shared(/usr/sap 포함) 위치가 있는 볼륨을 포괄하는 결합된 스냅숏을 시간별로 수행합니다. 2일 동안 이러한 스냅숏을 유지합니다. 트랜잭션 로그 백업 볼륨의 스냅숏은 5분 단위로 실행되고 4시간 동안 유지됩니다. 이전과 마찬가지로, HANA 트랜잭션 로그 파일의 백업은 5분 간격으로 실행되도록 예약됩니다. 트랜잭션 로그 백업 볼륨의 스냅숏은 트랜잭션 로그 백업이 시작되고 2분 지연된 후 수행됩니다. 정상 상태에서는 이러한 2분 이내에 SAP HANA 트랜잭션 로그 백업이 완료됩니다. 이전과 마찬가지로, 부팅 LUN을 포함하는 볼륨은 저장소 스냅숏에 의해 하루 1번 백업되고 4주 동안 유지됩니다.

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

그래픽에 표시된 것처럼, 부팅 LUN을 제외하고 위 예제의 시퀀스는 다음과 같습니다.

![백업 및 스냅숏 간 관계](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot.PNG)

SAP HANA는 /hana/log 볼륨에 대해 정기적인 쓰기를 수행하여 데이터베이스에 대한 커밋된 변경 내용을 문서화합니다. 정기적으로 SAP HANA는 /hana/data 볼륨에 저장점을 씁니다. crontab에 지정된 대로, SAP HANA 트랜잭션 로그 백업은 5분마다 실행됩니다. /hana/data, /hana/log 및 /hana/shared 볼륨에 대해 결합된 저장소 스냅숏을 트리거한 결과로, SAP HANA 스냅숏이 매시간 실행되는 것을 볼 수 있습니다. HANA 스냅숏이 성공한 후 결합된 저장소 스냅숏이 실행됩니다. 5분마다 crontab에 지정되는 것처럼 HANA 트랜잭션 로그 백업이 있고 약 2분 후에 /hana/logbackup 볼륨의 저장소 스냅숏이 실행됩니다.


>[!IMPORTANT]
> SAP HANA 트랜잭션 백업에 대한 저장소 스냅숏 사용은 스냅숏이 SAP HANA 로그 백업과 함께 수행된 경우에만 유용합니다. 이러한 트랜잭션 로그 백업은 저장소 스냅숏 간에 기간에 적용되어야 합니다. 

30일이라는 지정 시간 복구의 사용자에 대한 커밋을 설정한 경우 다음 항목이 필요합니다.

- 극단적으로 30일 된 /hana/data, /hana/log 및 /hana/shared의 조합된 저장소 스냅숏에 액세스하는 기능
- 조합된 저장소 스냅숏 사이의 시간을 보완하는 인접한 트랜잭션 로그 백업. 따라서 트랜잭션 로그 백업 볼륨의 가장 오래된 스냅숏은 30일 이전이어야 합니다. 트랜잭션 로그 백업을 Azure Storage에 있는 다른 NFS 공유로 복사하지 않은 경우 해당 NFS 공유에서 오래된 트랜잭션 로그 백업을 끌어올 수 있습니다.

저장소 스냅숏 및 트랜잭션 로그 백업의 최종 저장소 복제를 활용하려면 SAP HANA가 트랜잭션 로그 백업을 쓰는 위치를 변경해야 합니다. 이러한 변경은 아래와 같이 HANA Studio에서 수행할 수 있습니다. SAP HANA가 전체 로그 세그먼트를 자동으로 백업하지만 확정된 로그 백업 간격을 지정해야 합니다. 특히 재해 복구 옵션을 사용하는 경우 일반적으로 확정된 기간으로 로그 백업을 실행하려고 할 것입니다. 아래 경우에서는 로그 백업 간격으로 15분을 적용했습니다.

![SAP HANA Studio에서 SAP HANA 백업 로그 예약](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15분보다 더 자주 수행된 백업을 선택할 수 있습니다. 재해 복구의 경우에 특히 그렇습니다. 일부 고객은 5분마다 트랜잭션 로그 백업을 수행합니다.  

이전에 데이터베이스를 백업한 적이 없는 경우 최종 단계는 파일 기반 백업을 수행하여 백업 카탈로그 내에 있어야 하는 단일 백업 항목을 만드는 것입니다. 그렇지 않으면 SAP HANA는 지정된 로그 백업을 시작할 수 없습니다.

![단일 백업 항목을 만드는 파일 기반 백업 수행](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


첫 번째로 성공한 저장소 스냅숏을 실행한 후 6단계에서 실행된 테스트 스냅숏도 삭제할 수 있습니다. 이를 위해 아래와 같이 스크립트 removeTestStorageSnapshot.pl을 실행해야 합니다.
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>디스크 볼륨에서 스냅숏의 개수 및 크기 모니터링

특정 저장소 볼륨에서 스냅숏의 개수 및 스냅숏의 저장소 사용을 모니터링할 수 있습니다. `ls` 명령은 스냅숏 디렉터리 또는 파일을 표시하지 않습니다. 그러나 Linux OS 명령 `du`는 해당 저장소 스냅숏이 동일한 볼륨에 저장되므로 이러한 저장소 스냅숏에 대한 세부 정보를 보여 줍니다. 이 명령은 다음 옵션과 함께 사용할 수 있습니다.

- `du –sh .snapshot`는 스냅숏 디렉터리 내에서 모든 스냅숏을 제공합니다.
- `du –sh --max-depth=1`은 .snapshot 폴더에 저장된 모든 스냅숏 및 각 스냅숏의 크기를 나열합니다.
- `du –hc`는 모든 스냅숏에서 사용하는 전체 크기를 제공합니다.

이러한 명령을 사용하여 만들고 저장한 스냅숏이 볼륨에서 모든 저장소를 사용하지 않도록 합니다.

>[!NOTE]
>부팅 LUN의 스냅숏은 위 명령으로 표시되지 않습니다.

### <a name="getting-details-of-snapshots"></a>스냅숏 세부 정보 가져오기
스냅숏에 대한 보다 자세한 정보를 얻기 위해 스크립트 azure\_hana\_snapshot\_details.pl을 사용할 수도 있습니다. 이 스크립트는 재해 복구 위치에 활성 서버가 있을 때 어떤 위치에서도 실행될 수 있습니다. 이 스크립트는 스냅숏이 포함된 볼륨별로 분석된 다음 정보를 제공합니다. 즉, 볼륨의 총 스냅숏 크기를 제공한 다음 해당 볼륨의 각 스냅숏에 대해 스냅숏 이름, 만든 시간, 스냅숏의 크기, 스냅숏의 빈도 및 해당 스냅숏과 연결된 HANA 백업 ID(해당되는 경우)를 제공합니다. 이 스크립트의 실행 구문은 다음과 같습니다.

```
./azure_hana_snapshot_details.pl 
```

이 스크립트는 HANA 백업 ID를 검색하려고 하므로 SAP HANA 인스턴스에 연결해야 합니다. 따라서 구성 파일 HANABackupCustomerDetails.txt가 올바르게 설정되어 있어야 합니다. 한 볼륨의 두 스냅숏에 대한 출력은 다음과 같을 수 있습니다.

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-storage-snapshot"></a>저장소 스냅숏에서 파일 수준 복원
스냅숏 유형 'hana' 및 'logs'의 경우 '.snapshot' 디렉터리의 볼륨에서 직접 해당 스냅숏에 액세스할 수 있습니다. 각 스냅숏에 대해 하위 디렉터리를 찾을 수 있습니다. 스냅숏 시점의 상태에서 해당 스냅숏에 포함되던 각 파일을 해당 디렉터리에서 실제 디렉터리 구조로 복사할 수 있습니다.

>[!NOTE]
>부팅 LUN의 스냅숏에 대해서는 단일 파일 복원이 작동하지 않습니다. '.snapshot' 디렉터리는 부팅 LUN에서 노출되지 않습니다. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>서버에서 스냅숏 개수 감소

앞에서 설명한 대로 사용자가 저장한 스냅숏의 특정 레이블 수를 줄일 수 있습니다. 스냅숏을 시작하는 명령의 마지막 두 매개 변수는 레이블과 보존할 스냅숏 개수입니다.

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

앞의 예제에서 스냅숏 레이블은 _고객_이고, 보존할 이 레이블의 스냅숏 개수는 _30_입니다. 디스크 공간 사용량에 응답하는 대로 저장된 스냅숏 수를 줄이려고 할 수 있습니다. 스냅숏 수를 (예를 들어 15개로) 줄이는 가장 좋은 방법은 마지막 매개 변수를 15로 설정한 스크립트를 실행하는 것입니다.

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

이 설정으로 스크립트를 실행한 결과로 새 저장소 스냅숏을 포함한 스냅숏 개수는 _15_입니다. 15개의 오래된 스냅숏은 삭제되지만 15개의 가장 최근 스냅숏은 유지됩니다.

 >[!NOTE]
 > 이 스크립트는 1시간 보다 오래된 스냅숏이 있는 경우에만 스냅숏 수를 줄입니다. 스크립트는 한 시간이 되지 않은 스냅숏을 삭제하지 않습니다. 이러한 제한 사항은 제공되는 선택적 재해 복구 기능과 관련되어 있습니다.

특정 백업 레이블(구문 예제에서는 hanadaily)을 갖는 스냅숏 집합을 더 이상 유지하지 않으려면 _0_을 보존 숫자로 포함하는 스크립트를 실행하여 해당 레이블과 일치하는 모든 스냅숏을 제거할 수 있습니다. 그러나 모든 스냅숏을 제거하면 재해 복구의 기능에 영향을 미칠 수 있습니다.

특정 스냅숏을 삭제할 수 있는 또 다른 방법은 스크립트 azure\_hana\_snapshot\_delelte.pl을 사용하는 것입니다. 이 스크립트는 HANA Studio에서 확인된 HANA 백업 ID를 사용하거나 스냅숏 이름 자체를 사용하여 스냅숏 또는 스냅숏 집합을 삭제하도록 디자인되었습니다.  현재, 백업 ID는 ‘hana’ 스냅숏 유형에 대해 생성된 스냅숏에만 연결됩니다. 유형이 ‘logs’ 및 ‘boot’인 스냅숏 백업은 SAP HANA 스냅숏을 수행하지 않습니다. 따라서 해당 스냅숏에 대해서는 백업 ID를 찾을 수 없습니다. 스냅숏 이름을 입력하는 경우 입력한 스냅숏 이름과 일치하는 다른 볼륨의 모든 스냅숏을 찾습니다. 이 스크립트의 호출 구문은 다음과 같습니다.

```
./azure_hana_snapshot_delete.pl 

```

_root_ 사용자 권한으로 스크립트를 실행해야 합니다.

스냅숏을 선택하는 경우 각 스냅숏을 개별적으로 삭제할 수 있습니다. 먼저 스냅숏을 포함하는 볼륨을 지정한 후 실제 스냅숏 이름을 지정하도록 요구됩니다. 스냅숏이 해당 볼륨에 존재하고 1시간 이상 경과된 경우 삭제됩니다. azure_hana_snapshot_details 스크립트를 실행하여 볼륨 이름 및 스냅숏 이름을 확인할 수 있습니다. 

>[!IMPORTANT]
>삭제하려는 스냅숏에만 존재하는 데이터가 있는 경우 삭제를 실행하면 해당 데이터가 영구히 손실됩니다.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>가장 최근 HANA 스냅숏으로 복구

프로덕션 중단 시나리오를 경험한 이벤트에서 Microsoft Azure 지원 서비스를 사용한 고객 인시던트처럼 저장소 스냅숏에서 복구하는 프로세스를 시작할 수 있습니다. 예기치 않은 시나리오는 데이터가 프로덕션 시스템에서 삭제되고 데이터를 다시 가져올 유일한 방법이 프로덕션 데이터베이스를 복원하는 것인 경우 긴급한 문제입니다.

다른 경우에는 지정 시간 복구가 낮은 긴급도로 몇 일 앞서 계획될 수도 있습니다. 우선 순위 문제를 발생시키는 대신 Azure의 SAP HANA Service Management를 사용하여 이 복구를 계획할 수 있습니다. 예를 들어, 새로운 Enhancement Package를 적용하여 SAP 소프트웨어의 업그레이드를 시도하려는 경우 EHP 업그레이드 전에 상태를 나타내는 스냅숏으로 다시 전환해야 합니다.

요청을 실행하기 전에 몇 가지 준비를 수행해야 합니다. Azure의 SAP HANA Service Management 팀은 요청을 처리하고 복원된 볼륨을 제공할 수 있습니다. 나중에 사용자는 스냅숏을 기반으로 HANA 데이터베이스를 복원할 수 있습니다. 요청에 대해 준비하는 방법은 다음과 같습니다.

>[!NOTE]
>사용자 인터페이스는 사용하는 SAP HANA 릴리스에 따라 다음 스크린샷에서 달라질 수 있습니다.

1. 복원할 스냅숏을 결정합니다. 달리 지시하지 않으면 hana/data 볼륨은 복원됩니다. 

2. HANA 인스턴스를 종료합니다.

 ![HANA 인스턴스를 종료합니다.](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 각 HANA 데이터베이스 노드에서 데이터 볼륨을 분리합니다. 데이터 볼륨이 운영 체제에 계속 탑재되는 경우 스냅숏 복원이 실패합니다.
 ![각 HANA 데이터베이스 노드에서 데이터 볼륨 분리](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Azure 지원 요청을 열어서 특정 스냅숏을 복원하도록 지시합니다.

 - 복원 중에 Azure Service Management의 SAP HANA는 전화 회의에 참여하여 올바른 저장소 스냅숏이 복원될 수 있도록 조정, 검증 및 확인을 수행할 것을 요청 받을 수 있습니다. 

 - 복원한 후에 Azure의 SAP HANA Service Management에서는 저장소 스냅숏이 복원된 시기를 알립니다.

5. 복원 프로세스가 완료된 후에 모든 데이터 볼륨을 다시 탑재합니다.

 ![모든 데이터 볼륨 다시 탑재](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. SAP HANA Studio를 통해 HANA DB에 다시 연결하는 경우 자동으로 표시되지 않으면 SAP HANA Studio 내에서 복구 옵션을 선택합니다. 다음 예제에서는 최신 HANA 스냅숏에 대한 복원을 보여 줍니다. 저장소 스냅숏에는 하나의 HANA 스냅숏이 포함되고, 가장 최근의 저장소 스냅숏으로 복원하는 경우 가장 최근의 HANA 스냅숏이어야 합니다. (이전 저장소 스냅숏으로 복원하는 경우 저장소 스냅숏이 만들어진 시간에 따라 HANA 스냅숏을 배치해야 합니다.)

 ![SAP HANA Studio 내에서 복구 옵션 선택](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. **특정 데이터 백업이나 저장소 스냅숏으로 데이터베이스 복구**를 선택합니다.

 !["복구 유형 지정" 창](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. **카탈로그 없이 백업 지정**을 선택합니다.

 !["백업 위치 지정" 창](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. **대상 형식** 목록에서 **스냅숏**을 선택합니다.

 !["복구할 백업 지정" 창](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. **마침**을 클릭하여 복구 프로세스를 시작합니다.

 !["마침"을 클릭하여 복구 프로세스를 시작합니다.](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 데이터베이스는 저장소 스냅숏이 포함된 HANA 스냅숏으로 복원되고 복구됩니다.

 ![HANA 데이터베이스는 HANA 스냅숏으로 복원되고 복구됩니다.](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>가장 최근 상태로 복구

다음 프로세스는 저장소 스냅숏에 포함되는 HANA 스냅숏을 복원합니다. 그런 다음 저장소 스냅숏을 복원하기 전에 트랜잭션 로그 백업을 데이터베이스의 가장 최근 상태로 복원합니다.

>[!IMPORTANT]
>계속 진행하기 전에 트랜잭션 로그 백업의 완전한 연속 체인이 있는지 확인합니다. 이러한 백업 없이 데이터베이스의 현재 상태를 복원할 수 없습니다.

1. "가장 최근 HANA 스냅숏으로 복구"에서 이전 절차의 1~6단계를 완료합니다.

2. **가장 최근 상태로 데이터베이스 복구**를 선택합니다.

 !["가장 최근 상태로 데이터베이스 복구"를 선택합니다.](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 가장 최근인 HANA 로그 백업의 위치를 지정합니다. 위치는 HANA 스냅숏에서 가장 최근 상태까지 모든 HANA 트랜잭션 로그 백업을 포함해야 합니다.

 ![가장 최근인 HANA 로그 백업의 위치를 지정합니다.](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 데이터베이스를 복구하는 기본으로 백업을 선택합니다. 이 예제에서 스크린샷의 HANA 스냅숏은 저장소 스냅숏이 포함되었던 HANA 스냅숏입니다. 

 ![데이터베이스를 복구하는 기본으로 백업을 선택합니다.](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 델타가 HANA 스냅숏 시간과 가장 최근의 상태 간에 존재하지 않는 경우 **델타 백업 사용** 확인란의 선택을 취소합니다.

 ![델타가 존재하지 않는 경우 "델타 백업 사용" 확인란의 선택을 취소합니다](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 요약 화면에서 **마침**을 클릭하여 복원 절차를 시작합니다.

 ![요약 페이지에서 "마침"을 클릭합니다.](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>다른 지정 시점으로 복구
저장소 스냅숏에 포함된 HANA 스냅숏과 HANA 스냅숏 지정 시점 복구보다 이후인 스냅숏 간의 지정 시점으로 복구하려면 다음을 수행합니다.

1. HANA 스냅숏에서 복구하려는 시간까지 모든 트랜잭션 로그 백업이 있는지 확인합니다.
2. "가장 최근 상태로 복구"에서 절차를 시작합니다.
3. 프로시저의 2단계에 있는 **복구 유형 선택** 창에서 **데이터베이스를 다음 지정 시점으로 복구**를 선택한 다음 3~6단계를 완료합니다.

### <a name="monitoring-the-execution-of-snapshots"></a>스냅숏의 실행 모니터링

HANA 큰 인스턴스의 저장소 스냅숏을 사용할 때는 해당 저장소 스냅숏의 실행도 모니터링해야 합니다. 저장소 스냅숏을 실행하는 스크립트는 파일에 출력을 작성한 다음 Perl 스크립트와 동일한 위치에 저장합니다. 각 저장소 스냅숏에 대해 별도 파일을 기록합니다. 각 파일의 출력은 스냅숏 스크립트가 실행되는 다양한 단계를 명확하게 표시합니다.

- 스냅숏을 만드는 데 필요한 볼륨 찾기
- 이러한 볼륨에서 만든 스냅숏 찾기
- 최종 기존 스냅숏을 삭제하여 지정한 스냅숏 개수와 일치
- SAP HANA 스냅숏 만들기
- 볼륨을 통해 저장소 스냅숏 만들기
- SAP HANA 스냅숏 삭제
- 가장 최근의 스냅숏 이름을 **‘.0’**으로 지정

스크립트 기능의 가장 중요한 부분은 다음 부분으로 식별됩니다.
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
스크립트가 HANA 스냅숏의 생성을 기록하는 방법을 이 샘플에서 볼 수 있습니다. 확장 사례의 경우 이 프로세스는 마스터 노드에서 시작됩니다. 마스터 노드는 각 작업자 노드에서 SAP HANA 스냅숏을 동기적으로 생성하기 시작합니다. 그런 다음 저장소 스냅숏이 만들어집니다. 저장소 스냅숏을 성공적으로 실행한 후에 HANA 스냅숏이 삭제됩니다. 마스터 노드에서 다시 시작합니다.


## <a name="disaster-recovery-principles"></a>재해 복구 원칙
HANA 큰 인스턴스를 사용하여 다른 Azure 지역의 HANA 큰 인스턴스 스탬프 간에 재해 복구 기능을 제공합니다. Azure의 미국 서부 지역에서 HANA 큰 인스턴스 단위를 배포하는 경우 미국 동부 지역의 HANA 큰 인스턴스 단위를 재해 복구 단위로 활용할 수 있습니다. 앞서 언급한 것처럼 재해 복구의 경우 DR 지역의 다른 HANA 큰 인스턴스 단위에 비용이 부과되므로 자동으로 구성되지 않습니다. 재해 복구 설치의 경우 강화 및 규모 확장이 둘 다 가능합니다. 

지금까지 배포된 시나리오에서는 고객이 DR 지역에서 해당 단위를 사용하여 설치된 HANA 인스턴스를 사용하는 비프로덕션 시스템을 실행했습니다. 이 HANA 큰 인스턴스 단위는 프로덕션 용도로 사용되는 SKU와 동일한 SKU여야 합니다. 또한 해당 디스크 볼륨이 프로덕션 시스템에서 재해 복구 사이트로의 저장소 복제 대상이 되도록 하려면 일부 추가 디스크 볼륨을 주문해야 합니다. 실제로 DR 사이트에 복제되는 볼륨에는 다음이 포함됩니다.

- /hana/data
- /hana/log
- /hana/shared
- /hana/log/backup(/usr/sap 포함)


제공되는 재해 복구 기능 중 기본 기능은 HANA 큰 인스턴스 인프라에서 제공하는 저장소 복제 기능입니다. 저장소 쪽에서 사용되는 기능은 저장소에 변경이 수행될 때와는 비동기 방식으로 변경 내용의 일정한 스트림이 복제되는 방식이 아닙니다. 대신, 이러한 볼륨에서 정기적으로 스냅숏이 생성된다는 사실을 토대로 진행되는 메커니즘입니다. 이미 복제된 스냅숏과 아직 복제되지 않은 새 스냅숏 간의 델타는 재해 복구 사이트를 거쳐 대상 디스크 볼륨으로 전송됩니다(프로덕션 볼륨과 크기가 동일함). 처음에는 데이터 양이 스냅숏 간 델타보다 더 작아지기 전에 볼륨의 전체 데이터를 전송해야 합니다. 결과적으로 DR 사이트의 볼륨에는 프로덕션 사이트에서 수행되는 모든 볼륨 스냅숏이 포함됩니다. 이러한 사실을 통해 프로덕션 시스템으로 롤백하지 않고도 손실된 데이터를 복구하기 위해 해당 DR 시스템을 사용하여 이전 상태로 돌아갈 수 있습니다.

프로덕션 사이트에서 HANA 시스템 복제를 고가용성 기능으로 사용하는 경우에는 계층 2(또는 복제본) 인스턴스의 볼륨만 복제됩니다. 이러한 구성으로 인해 보조 복제본(계층 2) 서버 단위 또는 이 단위의 SAP HANA 인스턴스를 유지 관리하거나 중단할 경우 DR 사이트로의 저장소 복제가 지연될 수 있습니다. 

>[!IMPORTANT]
>다계층 HANA 시스템 복제와 마찬가지로 HANA 큰 인스턴스 재해 복구 기능을 사용하는 경우 계층 2 HANA 인스턴스 또는 서버 단위를 종료하면 재해 복구 사이트로의 복제가 차단됩니다.


>[!NOTE]
>HANA 큰 인스턴스 저장소 복제 기능은 저장소 스냅숏을 미러링/복제합니다. 따라서 이 문서의 백업 섹션에서 설명된 것처럼 저장소 스냅숏을 수행하지 않는 경우 재해 복구 사이트로 복제가 수행될 수 없습니다. 재해 복구 사이트로 저장소를 복제하려면 저장소 스냅숏을 반드시 실행해야 합니다.

복구 지점 목표를 최소화하기 위해 HANA 큰 인스턴스 서비스에서 다음 복제 간격이 설정됩니다.
- 조합된 스냅숏(스냅숏 유형 = 'hana')에 포함되는 볼륨은 재해 복구 사이트의 동일한 저장소 볼륨 대상으로 15분마다 복제됩니다.
- 트랜잭션 로그 백업 볼륨(스냅숏 유형 = 'logs')은 재해 복구 사이트의 동일한 저장소 볼륨 대상으로 3분마다 복제됩니다.

복구 지점 목표를 최소화하기 위해서는 다음을 설정해야 합니다.
- 'Hana' 유형 저장소 스냅숏을 30분~1시간마다 수행합니다(7단계 - 스냅샷 수행 참조).
- SAP HANA 트랜잭션 로그 백업은 5분마다 수행합니다.
- ‘logs’ 유형의 저장소 스냅숏은 5~15분마다 수행합니다. 이 기간을 사용하면 약 15~25분의 RPO를 얻을 수 있습니다.

이 설치를 사용할 경우 HANA 트랜잭션 로그 백업 볼륨과 /hana/data, /hana/log, /hana/shared(/usr/sap 포함)의 트랜잭션 로그 백업 시퀀스, 저장소 스냅숏 및 복제가 다음 그래픽과 같이 표시될 수 있습니다.

 ![시간 축에서 트랜잭션 로그 백업 스냅숏과 스냅 미러 간 관계](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

재해 복구 사례에서 복구 지점 목표를 향상시키려면 Azure(큰 인스턴스)의 SAP HANA에 있는 HANA 트랜잭션 로그 백업을 다른 Azure 지역에 복사할 수 있습니다. 이와 같이 복구 지점 목표를 추가로 감소하기 위해서는 다음과 같은 대략적인 단계를 수행합니다.

1. HANA 트랜잭션 로그를 가능한 한 자주 /hana/logbackups에 백업합니다.
2. 트랜잭션 로그 백업이 Azure 프로덕션 영역 및 DR 영역의 Azure Virtual Network에 있는 NFS 공유 호스티드 Azure VM(Virtual Machines)에서 끝날 때 rsync를 사용하여 해당 트랜잭션 로그 백업을 복사합니다. 프로덕션 HANA 큰 인스턴스를 Azure에 연결하는 ExpressRoute 회로에 두 Azure VNet을 연결해야 합니다(이 문서의 ‘재해 복구 요구 사항’ 섹션의 그래픽 참조). 
3. 해당 지역의 트랜잭션 로그 백업을 VM 연결 NFS에서 내보낸 저장소에 유지합니다.
4. 재해 장애 조치(failover)의 경우 /hana/logbackups 볼륨에서 찾은 트랜잭션 로그 백업을 최근에 재해 복구 사이트의 NFS 공유에 작성한 트랜잭션 로그 백업으로 보완합니다. 
5. 이제 DR 영역에 저장할 수 있는 최신 백업에 대한 트랜잭션 로그 백업 복원을 시작할 수 있습니다.

## <a name="disaster-recovery-fail-over-procedure"></a>재해 복구 장애 조치(failover) 절차
DR 사이트로의 장애 조치(failover)를 원하거나 장애 조치가 필요한 경우 Azure Operations에서 SAP HANA와 상호 작용해야 하는 프로세스를 확인할 수 있습니다. 대략적인 단계에서 지금까지의 프로세스 다음과 같습니다.

- HANA 큰 인스턴스의 재해 복구 단위에서 HANA의 비프로덕션 인스턴스를 실행하고 있으므로 이 인스턴스를 종료해야 합니다. 유휴 HANA 프로덕션 인스턴스가 미리 설치되어 있다고 가정합니다.
- SAP HANA 프로세스가 더 이상 실행되고 있지 않은지 확인해야 합니다. 다음 명령으로 이러한 확인을 수행할 수 있습니다. /usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList. 출력에는 hdbdaemon 프로세스가 중지된 상태로 표시되고, 실행 중이거나 시작된 상태의 다른 HANA 프로세스는 표시되지 않습니다.
- 이제 재해 복구 사이트를 복원하려는 스냅숏 이름 또는 SAP HANA 백업 ID를 확인해야 합니다. 실제 재해 복구의 경우 이 스냅숏이 일반적으로 최신 스냅숏입니다. 그러나 손실된 데이터를 복구해야 하는 경우가 있을 수 있습니다. 따라서 이전 스냅숏을 선택해야 합니다.
- 우선 순위가 높은 지원 요청을 통해 Azure Service Managemen에서 SAP HANA에 연결하고 DR 사이트의 해당 스냅숏/HANA 백업 ID 복원을 요청해야 합니다. 작업 측면에서 다음 단계가 수행됩니다.
    - 프로덕션 볼륨에서 재해 복구 볼륨으로의 스냅숏 복제는 중지됩니다. 이러한 중지는 프로덕션 사이트의 중단 원인으로 인해 발생한 것이 아닙니다.
    - 선택한 저장소 스냅숏/백업 ID가 재해 복구 볼륨에 복원됩니다.
    - 복원 후에는 재해 복구 볼륨을 재해 복구 지역의 HANA 큰 인스턴스 단위에 탑재할 수 있습니다.
- 고객 쪽에서 볼 때 다음 단계는 재해 복구 볼륨을 재해 복구 사이트의 HANA 큰 인스턴스 단위에 탑재하는 것입니다. 
- 그런 후 유휴 SAP HANA 프로덕션 인스턴스를 시작할 수 있습니다.
- 또한 RPO 시간을 추가적으로 줄이기 위해 트랜잭션 로그 백업 로그를 복사하도록 선택한 경우 해당 트랜잭션 로그 백업을 새로 탑재한 DR/hana/logbackups 디렉터리에 병합해야 합니다. 기존 백업은 덮어쓰지 않아야 하며 저장소에서 복제되지 않은 새 백업만 복사합니다.

단계의 다음 시퀀스에서는 복원된 저장소 스냅숏 및 사용 가능한 트랜잭션 로그 백업에 따라 SAP HANA 프로덕션 인스턴스가 복구됩니다. 단계는 다음과 같습니다.

![DR 복구에 대한 백업 위치 변경](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png) 아래에 표시된 것처럼 SAP HANA Studio를 사용하여 백업 위치를 /hana/logbackups로 변경합니다.

SAP HANA는 백업 파일 위치를 검색하고, 가장 최근 트랜잭션 로그 백업을 복원할 것을 제안합니다. 아래와 같은 화면이 표시될 때까지 검색에 몇 분 정도 소요될 수 있습니다. ![DR 복구에 대한 트랜잭션 로그 백업 목록](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

다음 단계에서는 다음을 수행하여 일부 기본 설정을 조정해야 합니다.

- **델타 백업 사용** 선택 취소
- 아래와 같이 **로그 영역 초기화** 선택

 ![로그 영역 초기화 설정](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

다음 화면에서 아래와 같이 **마침**을 누릅니다.

 ![DR 복원 완료](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

여기에 표시된 것처럼 진행률 창이 표시됩니다. 해당 예제는 3노드 규모 확장 SAP HANA 구성의 재해 복구 복원에 대한 것입니다.

 ![복원 진행률](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

복원이 '마침 '화면에서 중단된 것처럼 보이고 진행률 화면이 표시되지 않는 경우 작업자 노드의 모든 SAP HANA 인스턴스가 실행되고 있는지 확인합니다. 필요한 경우 SAP HANA 인스턴스를 수동으로 시작합니다.


### <a name="fail-back-from-dr-to-production-site"></a>DR에서 프로덕션 사이트로 장애 복구(Failback)
사용자가 손실된 데이터를 다시 가져오도록 요구하는 경우가 아니라 프로덕션 Azure 영역의 문제로 인해 재해 복구 사이트로의 장애 조치(failover)가 야기되었다고 가정합니다. 즉, 이제 프로덕션 환경이거나 재해 복구 사이트에서 작업하고 있습니다. 프로덕션 사이트의 문제가 해결되었으므로 프로덕션 사이트로 장애 복구(Failback)하려고 할 수 있습니다. 데이터를 손실할 수 없으므로 프로덕션 사이트로의 복귀 중에는 여러 단계가 수행되며 Azure Operations의 SAP HANA와의 긴밀한 협력 작업이 진행됩니다. 문제가 해결되었을 때 프로덕션 사이트와 다시 동기화를 시작하도록 작업을 트리거하는 것은 고객의 책임입니다.

단계 시퀀스는 다음과 같습니다.

- Azure Operations의 SAP HANA는 현재의 프로덕션 상태를 나타내는 재해 복구 저장소 볼륨에서 프로덕션 저장소 볼륨을 동기화하는 트리거를 가져옵니다. 이 상태에서 프로덕션 사이트에 있는 HANA 큰 인스턴스 단위가 종료됩니다.
- Azure Operations의 SAP HANA는 복제를 모니터링하고 고객에게 알리기 전에 가져오려고 합니다.
- 재해 복구 사이트에서 프로덕션 HANA 인스턴스를 사용하는 응용 프로그램을 종료해야 합니다. 그런 후 HANA 트랜잭션 로그 백업을 수행하고 재해 복구 사이트의 HANA 큰 인스턴스 단위에서 실행 중인 HANA 인스턴스를 중지합니다.
- 재해 복구 사이트의 HANA 큰 인스턴스 단위에서 실행 중인 HANA 인스턴스가 종료된 후에는 디스크 볼륨을 수동으로 다시 동기화해야 합니다.
- Azure Operations의 SAP HANA는 프로덕션 사이트에서 HANA 큰 인스턴스 단위를 시작한 후 사용자에게 전달합니다. HANA 큰 인스턴스 단위의 시작 시간에 SAP HANA 인스턴스가 종료된 상태인지 확인합니다.
- 이전에 장애 복구 사이트로 장애 조치(failover)할 때 수행했던 것과 동일한 데이터베이스 복원 단계에 수행해야 합니다.

### <a name="monitoring-disaster-recovery-replication"></a>재해 복구 복제 모니터링

스크립트 azure\_hana\_replication\_status.pl을 실행하여 저장소 복제 진행 상태를 모니터링할 수 있습니다. 이 스크립트는 재해 복구 위치에서 실행되는 단위에서 실행되어야 합니다. 그렇지 않으면 예상대로 작동하지 않습니다. 이 스크립트는 복제가 활성 상태인지에 관계없이 작동합니다. 이 스크립트는 재해 복구 위치에서 테넌트의 모든 HANA 큰 인스턴스 단위에 대해 실행할 수 있습니다. 부팅 볼륨에 대한 세부 정보를 가져오는 데는 사용할 수 없습니다.

다음과 같은 스크립트를 호출합니다.
```
./replication_status.pl <HANA SID>
```

출력은 볼륨별로 다음 섹션으로 세분화됩니다.  

- 연결 상태
- 현재 복제 작업
- 마지막에 복제된 스냅숏 
- 최신 스냅숏의 크기
- 마지막에 완료된 스냅숏 복제부터 지금까지의 지연 시간  

연결 상태는 위치 간의 연결이 끊어지거나 장애 조치(failover) 이벤트가 현재 진행 중이지 않는 한, ‘활성’으로 표시됩니다. 복제 작업은 현재 복제 중이거나 유휴 상태인 데이터가 있는지 여부 또는 현재 연결에서 수행되고 있는 다른 작업이 있는지를 확인합니다. 마지막에 복제된 스냅숏은 'snapmirror...'로만 표시됩니다. 그런 후 마지막 스냅숏의 크기가 표시됩니다. 마지막으로 지연 시간이 표시됩니다. 지연 시간은 예약된 복제 시간부터 복제가 완료될 때까지의 시간을 나타냅니다. 복제가 시작된 경우에도 지연 시간이 데이터 복제를 위한 1시간보다 크게 표시될 수 있습니다. 특히 첫 번째 복제인 경우 이러한 문제가 나타납니다. 지연 시간은 진행 중인 복제가 완료될 때까지 계속 증가하게 됩니다.

출력 예제는 다음과 같을 수 있습니다.

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












