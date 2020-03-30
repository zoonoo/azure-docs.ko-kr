---
title: Azure에서 SAP HANA에 대한 지원되는 시나리오(대규모 인스턴스)| 마이크로 소프트 문서
description: Azure(큰 인스턴스)의 SAP HANA에 대해 지원되는 시나리오 및 해당 아키텍처 세부 정보
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617175"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 큰 인스턴스의 지원되는 시나리오
이 문서에서는 HANA 대형 인스턴스(HLI)에 대해 지원되는 시나리오 및 아키텍처 세부 정보를 설명합니다.

>[!NOTE]
>이 문서에서 필요한 시나리오를 언급하지 않은 경우 Microsoft 서비스 관리 팀에 문의하여 요구 사항을 평가하십시오.
HLI 장치를 설정하기 전에 SAP 또는 서비스 구현 파트너와 함께 설계의 유효성을 검사합니다.

## <a name="terms-and-definitions"></a>용어 및 정의
이 문서에서 사용되는 용어와 정의를 이해해 보겠습니다.

- **SID**: HANA 시스템의 시스템 식별자
- **HLI**: 하나 대형 인스턴스
- **DR**: 재해 복구
- **일반 DR**: DR 전용 리소스가 있는 시스템 설정
- **다목적 DR**: DR 이벤트에 대해 구성된 프로덕션 인스턴스와 함께 비프로덕션 환경을 사용하도록 구성된 DR 사이트 시스템 
- **단일 SID**: 하나의 인스턴스가 설치된 시스템
- **다중 SID**: 여러 인스턴스가 구성된 시스템; MCOS 환경이라고도 합니다.
- **HSR**: SAP HANA 시스템 복제

## <a name="overview"></a>개요
HANA 대형 인스턴스는 비즈니스 요구 사항을 충족할 수 있도록 다양한 아키텍처를 지원합니다. 다음 섹션에서는 아키텍처 시나리오 및 구성 세부 정보를 다룹니다. 

파생 된 아키텍처 디자인은 인프라 관점에서만 수행되며 HANA 배포에 대한 SAP 또는 구현 파트너와 상의해야 합니다. 시나리오가 이 문서에 나열되어 있지 않으면 Microsoft 계정 팀에 문의하여 아키텍처를 검토하고 해결 방법을 도출하십시오.

> [!NOTE]
> 이러한 아키텍처는 맞춤형 데이터 통합(TDI) 설계를 완벽하게 준수하며 SAP에서 지원합니다.

이 문서에서는 지원되는 각 아키텍처의 두 구성 요소에 대해 자세히 설명합니다.

- 이더넷
- 스토리지

### <a name="ethernet"></a>이더넷

프로비저닝된 각 서버는 이더넷 인터페이스 집합으로 미리 구성됩니다. 각 HLI 장치에 구성된 이더넷 인터페이스는 다음 네 가지 유형으로 분류됩니다.

- **A**: 클라이언트 액세스용 또는 클라이언트 액세스에 사용됩니다.
- **B**: 노드 간 통신에 사용됩니다. 이 인터페이스는 요청된 토폴로지와 관계없이 모든 서버에서 구성되지만 확장 시나리오에만 사용됩니다.
- **C**: 노드 간 연결에 사용됩니다.
- **D**: STONITH 설정을 위한 노드-iSCSI 장치 연결에 사용됩니다. 이 인터페이스는 HSR 설정이 요청된 경우에만 구성됩니다.  

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간|
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 노드 간|
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | STONITH |

HLI 장치에 구성된 토폴로지에서 인터페이스를 선택합니다. 예를 들어 인터페이스 "B"는 노드 간 통신을 위해 설정되며, 이는 확장 토폴로지가 구성된 경우에 유용합니다. 이 인터페이스는 단일 노드, 확장 구성에 사용되지 않습니다. 인터페이스 사용에 대한 자세한 내용은 필요한 시나리오를 검토합니다(이 문서의 후반부). 

필요한 경우 직접 추가 NIC 카드를 정의할 수 있습니다. 그러나 기존 NIC의 구성은 변경할 *수 없습니다.*

>[!NOTE]
>물리적 인터페이스 또는 본딩인 추가 인터페이스를 찾을 수 있습니다. 사용 사례에 대해 앞에서 언급한 인터페이스만 고려해야 합니다. 다른 모든 무시할 수 있습니다.

두 개의 할당된 IP 주소가 있는 단위의 분포는 다음과 같아야 합니다.

- 이더넷 "A"는 Microsoft에 제출한 서버 IP 풀 주소 범위 내에 할당된 IP 주소가 있어야 합니다. 이 IP 주소는 OS의 */etc/hosts* 디렉터리에서 유지 관리해야 합니다.

- 이더넷 "C"에는 NFS통신에 사용되는 할당된 IP 주소가 있어야 합니다. 이 주소는 테넌트 내에서 인스턴스 간 트래픽을 허용하기 위해 *etc/hosts* 디렉터리에서 유지 관리할 필요가 *없습니다.*

HANA 시스템 복제 또는 HANA 확장 배포의 경우 두 개의 할당된 IP 주소가 있는 블레이드 구성이 적합하지 않습니다. 할당된 IP 주소가 두 개뿐이고 이러한 구성을 배포하려는 경우 Azure 서비스 관리에서 SAP HANA에 문의하십시오. 세 번째 VLAN에서 세 번째 IP 주소를 할당할 수 있습니다. 3개의 NIC 포트에 3개의 할당된 IP 주소가 있는 HANA 대형 인스턴스 단위의 경우 다음 사용 규칙이 적용됩니다.

- 이더넷 "A"는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어난 할당된 IP 주소가 있어야 합니다. 이 IP 주소는 OS의 *etc/hosts* 디렉터리에서 유지 관리하면 안 됩니다.

- 이더넷 "B"는 다양한 인스턴스 간의 통신을 위해 *etc/hosts* 디렉터리에서만 유지 관리되어야 합니다. 이러한 IP 주소는 HANA가 노드 간 구성에 사용하는 IP 주소로서 확장 된 HANA 구성에서 유지 관리해야 하는 IP 주소입니다.

- 이더넷 "C"에는 NFS 스토리지에 통신하는 데 사용되는 할당된 IP 주소가 있어야 합니다. 이러한 유형의 주소는 *etc/hosts* 디렉터리에서 유지 관리하면 안 됩니다.

- 이더넷 "D"는 페이스메이커용 STONITH 장치에 액세스하기 위해독점적으로 사용해야 합니다. 이 인터페이스는 HANA 시스템 복제를 구성하고 SBD 기반 장치를 사용하여 운영 체제의 자동 장애 조치(failover)를 달성하려는 경우에 필요합니다.


### <a name="storage"></a>스토리지
저장소는 요청된 토폴로지기반으로 미리 구성됩니다. 볼륨 크기와 탑재 지점은 서버 수, SCO 수 및 구성된 토폴로지에 따라 달라집니다. 자세한 내용은 필요한 시나리오(이 문서의 후반부)를 검토합니다. 더 많은 저장소가 필요한 경우 1TB 단위로 구입할 수 있습니다.

>[!NOTE]
>마운트 지점 /usr/sap/\<SID> /hana/공유 마운트 포인트에 대한 상징적 링크입니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

다음 섹션의 아키텍처 다이어그램은 다음 표기조를 사용합니다.

[![아키텍처 다이어그램 표](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

지원되는 시나리오는 다음과 같습니다.

* SID가 1개인 단일 노드
* 단일 노드 MCOS
* DR이 있는 단일 노드(일반)
* DR이 있는 단일 노드(다목적)
* STONITH가 있는 HSR
* DR이 있는 HSR(일반/다목적) 
* 호스트 자동 장애 조치(Failover)(1+1) 
* 대기 상태로 스케일 아웃
* 대기 없이 스케일 아웃
* DR을 사용하여 스케일 아웃

## <a name="single-node-with-one-sid"></a>SID가 1개인 단일 노드

이 토폴로지에서는 하나의 SID를 통해 확장 구성에서 하나의 노드를 지원합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![SID가 1개인 단일 노드](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | 하나 설치 | 
|/hana/data/SID/mnt00001 | 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 로그 파일 설치 | 
|/hana/logbackups/SID | 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="single-node-mcos"></a>단일 노드 MCOS

이 토폴로지에서는 여러 개의 SID가 있는 확장 구성에서 하나의 노드를 지원합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![단일 노드 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID1 | SID1용 HANA 설치 | 
|/hana/data/SID1/mnt00001 | SID1용 데이터 파일 설치 | 
|/hana/log/SID1/mnt00001 | SID1용 로그 파일 설치 | 
|/hana/logbackups/SID1 | SID1의 다시 실행 로그 |
|/hana/shared/SID2 | SID2용 HANA 설치 | 
|/hana/data/SID2/mnt00001 | SID2용 데이터 파일 설치 | 
|/hana/log/SID2/mnt00001 | SID2용 로그 파일 설치 | 
|/hana/logbackups/SID2 | SID2의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기반으로 합니다. 다중 SID 환경에서 메모리의 데이터베이스 크기를 지원하는 것에 대해 알아보려면 [개요 및 아키텍처를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)참조하십시오.

## <a name="single-node-with-dr-using-storage-replication"></a>스토리지 복제를 사용하여 DR이 있는 단일 노드
 
이 토폴로지는 하나 또는 여러 SID가 있는 확장 구성에서 하나의 노드를 지원하며, 기본 SID에 대한 DR 사이트에 대한 저장소 기반 복제를 지원합니다. 다이어그램에서는 단일 SID 시스템만 기본 사이트에 설명되지만 MCOS 시스템도 지원됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제를 사용하여 DR이 있는 단일 노드](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | SID용 HANA 설치 | 
|/hana/data/SID/mnt00001 | SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | SID용 로그 파일 설치 | 
|/hana/logbackups/SID | SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS의 경우: 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기반으로 합니다. 다중 SID 환경에서 메모리의 데이터베이스 크기를 지원하는 것에 대해 알아보려면 [개요 및 아키텍처를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)참조하십시오.
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다("HANA 설치에 필요한 것"으로 표시). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨("저장소 복제"로 표시)은 프로덕션 사이트의 스냅숏을 통해 복제됩니다. 이러한 볼륨은 장애 조치 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 절차를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)참조하십시오.
- *SKU Type I 클래스의* 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>스토리지 복제를 사용하는 DR(다목적)이 있는 단일 노드
 
이 토폴로지는 하나 또는 여러 SID가 있는 확장 구성에서 하나의 노드를 지원하며, 기본 SID에 대한 DR 사이트에 대한 저장소 기반 복제를 지원합니다. 다이어그램에서는 단일 SID 시스템만 기본 사이트에 설명되지만 다중 SID(MCOS) 시스템도 지원됩니다. DR 사이트에서 는 생산 작업이 기본 사이트에서 실행되는 동안 QA 인스턴스에 HLI 단위가 사용됩니다. DR 장애 조치(또는 장애 조치 테스트) 중에 DR 사이트의 QA 인스턴스가 다운됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제를 사용하는 DR(다목적)이 있는 단일 노드](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID용 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID용 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID용 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS의 경우: 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기반으로 합니다. 다중 SID 환경에서 메모리의 데이터베이스 크기를 지원하는 것에 대해 알아보려면 [개요 및 아키텍처를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)참조하십시오.
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다("HANA 설치에 필요한 것"으로 표시). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨("저장소 복제"로 표시)은 프로덕션 사이트의 스냅숏을 통해 복제됩니다. 이러한 볼륨은 장애 조치 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 절차를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)참조하십시오. 
- DR 사이트에서: QA에 대한 데이터, 로그 백업, 로그 및 공유 볼륨("QA 인스턴스 설치"로 표시)은 QA 인스턴스 설치를 위해 구성됩니다.
- *SKU Type I 클래스의* 부팅 볼륨이 DR 노드에 복제됩니다.

## <a name="hsr-with-stonith-for-high-availability"></a>고가용성을 위한 스토니트(STONITH)가 있는 HSR
 
이 토폴로지는 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원합니다. 이 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원됩니다. 즉, MCOS 시나리오는 *지원되지 않습니다.*

> [!NOTE]
> 2019년 12월부터 이 아키텍처는 SUSE 운영 체제에대해서만 지원됩니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![고가용성을 위한 스토니트(STONITH)가 있는 HSR](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**보조 노드**|
|/hana/shared/SID | 보조 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS의 경우: 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기반으로 합니다. 다중 SID 환경에서 메모리의 데이터베이스 크기를 지원하는 것에 대해 알아보려면 [개요 및 아키텍처를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)참조하십시오.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH의 사용은 선택 사항입니다.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>스토리지 복제를 통해 HSR 및 DR을 갖춘 고가용성
 
이 토폴로지는 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원합니다. 일반 및 다목적 DR이 모두 지원됩니다. 이러한 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원됩니다. 즉, MCOS 시나리오는 이러한 구성에서 *지원되지 않습니다.*

다이어그램에서 다목적 시나리오는 DR 사이트에 묘사되며, 여기서 HLI 단위는 QA 인스턴스에 사용되며 프로덕션 작업은 기본 사이트에서 실행되고 있습니다. DR 장애 조치(또는 장애 조치 테스트) 중에 DR 사이트의 QA 인스턴스가 다운됩니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제를 통해 HSR 및 DR을 갖춘 고가용성](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트의 주 노드**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**기본 사이트의 보조 노드**|
|/hana/shared/SID | 보조 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID용 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID용 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID용 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS의 경우: 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기반으로 합니다. 다중 SID 환경에서 메모리의 데이터베이스 크기를 지원하는 것에 대해 알아보려면 [개요 및 아키텍처를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)참조하십시오.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH의 사용은 선택 사항입니다.
- DR 사이트에서: 기본 및 보조 노드 복제에는 *두 개의 저장소 볼륨 집합이 필요합니다.*
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다("HANA 설치에 필요한 것"으로 표시). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨("저장소 복제"로 표시)은 프로덕션 사이트의 스냅숏을 통해 복제됩니다. 이러한 볼륨은 장애 조치 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 절차를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)참조하십시오. 
- DR 사이트에서: QA에 대한 데이터, 로그 백업, 로그 및 공유 볼륨("QA 인스턴스 설치"로 표시)은 QA 인스턴스 설치를 위해 구성됩니다.
- *SKU Type I 클래스의* 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="host-auto-failover-11"></a>호스트 자동 장애 조치(Failover)(1+1)
 
이 토폴로지는 호스트 자동 장애 조치(Failover) 구성의 두 노드를 지원합니다. 마스터/작업자 역할이 있는 노드와 대기로 다른 노드가 있습니다. *SAP는 S/4 HANA에 대해서만 이 시나리오를 지원합니다.* 자세한 내용은 [OSS 노트 2408419 - SAP S/4HANA - 다중 노드 지원을](https://launchpad.support.sap.com/#/notes/2408419)참조하십시오.



### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![호스트 자동 장애 조치(Failover)(1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터 및 대기 노드**|
|/hana/shared | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |



### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 대기 중: 대기 장치에 HANA 인스턴스 설치를 위해 볼륨 및 탑재 지점이 구성됩니다("HANA 설치에 필요한 것"으로 표시).
 

## <a name="scale-out-with-standby"></a>대기 상태로 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 마스터 역할이 있는 노드가 하나, 작업자 역할이 있는 노드가 하나 이상 있고, 하나 이상의 노드가 대기 로 있습니다. 그러나 단일 시점에 하나의 마스터 노드만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![대기 상태로 스케일 아웃](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터, 작업자 및 대기 노드**|
|/hana/shared | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


## <a name="scale-out-without-standby"></a>대기 없이 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 마스터 역할이 있는 노드가 하나 있고 작업자 역할이 있는 노드가 하나 이상 있습니다. 그러나 단일 시점에 하나의 마스터 노드만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![대기 없이 스케일 아웃](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터 및 작업자 노드**|
|/hana/shared | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="scale-out-with-dr-using-storage-replication"></a>스토리지 복제를 사용하여 DR을 사용하여 확장
 
이 토폴로지는 DR이 있는 스케일 아웃의 여러 노드를 지원합니다. 일반 및 다목적 DR이 모두 지원됩니다. 이 다이어그램에는 단일 용도 DR만 표시됩니다. 대기 노드가 있거나 없는 상태로 이 토폴로지를 요청할 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![스토리지 복제를 사용하여 DR을 사용하여 확장](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 노드**|
|/hana/shared | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
-  DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다("HANA 설치에 필요한 것"으로 표시). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨("저장소 복제"로 표시)은 프로덕션 사이트의 스냅숏을 통해 복제됩니다. 이러한 볼륨은 장애 조치 중에만 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 절차를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)참조하십시오. 
- *SKU Type I 클래스의* 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="single-node-with-dr-using-hsr"></a>HSR을 사용하는 DR이 있는 단일 노드
 
이 토폴로지는 하나의 SID를 사용하여 확장 구성에서 하나의 노드를 지원하며, HANA 시스템 복제는 기본 SID에 대한 DR 사이트에 복제됩니다. 다이어그램에서는 단일 SID 시스템만 기본 사이트에 설명되지만 다중 SID(MCOS) 시스템도 지원됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR을 사용하는 DR이 있는 단일 노드](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HSR |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI/HSR |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 HLI 장치(기본 및 DR)에서 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | SID용 HANA 설치 | 
|/hana/data/SID/mnt00001 | SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | SID용 로그 파일 설치 | 
|/hana/logbackups/SID | SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS의 경우: 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기반으로 합니다. 다중 SID 환경에서 메모리의 데이터베이스 크기를 지원하는 것에 대해 알아보려면 [개요 및 아키텍처를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)참조하십시오.
- 기본 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [글로벌 리치는](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용됩니다.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>단일 노드 HSR에서 DR까지(비용 최적화) 
 
 이 토폴로지는 하나의 SID를 사용하여 확장 구성에서 하나의 노드를 지원하며, HANA 시스템 복제는 기본 SID에 대한 DR 사이트에 복제됩니다. 다이어그램에서는 단일 SID 시스템만 기본 사이트에 설명되지만 다중 SID(MCOS) 시스템도 지원됩니다. DR 사이트에서 는 생산 작업이 기본 사이트에서 실행되는 동안 QA 인스턴스에 HLI 단위가 사용됩니다. DR 장애 조치(또는 장애 조치 테스트) 중에 DR 사이트의 QA 인스턴스가 다운됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![단일 노드 HSR에서 DR까지(비용 최적화)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HSR |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI/HSR |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|/hana/shared/QA-SID | QA SID용 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID용 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID용 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS의 경우: 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기반으로 합니다. 다중 SID 환경에서 메모리의 데이터베이스 크기를 지원하는 것에 대해 알아보려면 [개요 및 아키텍처를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)참조하십시오.
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다(DR 사이트에서 "PROD 인스턴스"로 표시). 
- DR 사이트에서: QA에 대한 데이터, 로그 백업, 로그 및 공유 볼륨("QA 인스턴스 설치"로 표시)은 QA 인스턴스 설치를 위해 구성됩니다.
- 기본 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [글로벌 리치는](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용됩니다.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>HSR을 통해 고가용성 및 재해 복구 
 
 이 토폴로지는 로컬 지역의 고가용성을 위해 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원합니다. DR의 경우 DR 영역의 세 번째 노드는 HSR(비동기 모드)을 사용하여 기본 사이트와 동기화됩니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR을 통해 고가용성 및 재해 복구](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HSR |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI/HSR |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다("PROD DR 인스턴스"로 표시). 
- 기본 사이트 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [글로벌 리치는](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용됩니다.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>HSR을 통해 고가용성 및 재해 복구(비용 최적화)
 
 이 토폴로지는 로컬 지역의 고가용성을 위해 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원합니다. DR의 경우 DR 영역의 세 번째 노드는 HSR(비동기 모드)을 사용하여 기본 사이트와 동기화되는 반면 다른 인스턴스(예: QA)는 DR 노드에서 이미 부족합니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR을 통해 고가용성 및 재해 복구(비용 최적화)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HSR |
| b | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI/HSR |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|/hana/shared/QA-SID | QA SID용 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID용 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID용 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다("PROD DR 인스턴스"로 표시). 
- DR 사이트에서: QA에 대한 데이터, 로그 백업, 로그 및 공유 볼륨("QA 인스턴스 설치"로 표시)은 QA 인스턴스 설치를 위해 구성됩니다.
- 기본 사이트 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [글로벌 리치는](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용됩니다.

## <a name="scale-out-with-dr-using-hsr"></a>HSR을 사용하여 DR을 사용한 확장
 
이 토폴로지는 DR이 있는 스케일 아웃의 여러 노드를 지원합니다. 대기 노드가 있거나 없는 상태로 이 토폴로지를 요청할 수 있습니다. 기본 사이트 노드는 HANA 시스템 복제(비동기 모드)를 사용하여 DR 사이트 노드와 동기화됩니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

[![HSR을 사용하여 DR을 사용한 확장](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 타입 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HSR |
| b | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-스토리지 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<테넌트> 없음 | team0.tenant | 클라이언트-HLI/HSR |
| b | 유형 II | vlan\<테넌트No+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<테넌트No+1> | team0.tenant+1 | 노드-스토리지 |
| D | 유형 II | vlan\<테넌트No+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>스토리지
다음 마운트 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 노드**|
|/hana/shared | 생산 SID를 위한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID용 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID용 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 장치의 프로덕션 HANA 인스턴스 설치를 위해 구성됩니다. 
- 기본 사이트 노드는 HANA 시스템 복제를 사용하여 DR 노드와 동기화됩니다. 
- [글로벌 리치는](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) ExpressRoute 회로를 함께 연결하여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용됩니다.


## <a name="next-steps"></a>다음 단계
- HANA 대형 인스턴스를 위한 [인프라 및 연결성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- HANA 대형 인스턴스를 위한 [고가용성 및 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
