---
title: Azure(큰 인스턴스)의 SAP HANA 지원 시나리오 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA에 대해 지원되는 시나리오 및 해당 아키텍처 세부 정보
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
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 501c5ffa86f2360e44c187e087f7285bbf4084fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477792"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 큰 인스턴스의 지원되는 시나리오
이 문서에서는 HLI(HANA 큰 인스턴스)에 대한 아키텍처 세부 정보와 지원되는 시나리오에 대해 설명합니다.

>[!NOTE]
>필요한 시나리오가 여기에 언급되지 않은 경우 Microsoft 서비스 관리 팀에 문의하여 요구 사항을 확인하세요.
HLI 단위 프로비전을 계속 진행하기 전에 SAP 또는 서비스 구현 파트너와 함께 디자인이 유효한지 검사합니다.

## <a name="terms-and-definitions"></a>용어 및 정의
문서에 사용되는 용어 및 정의를 살펴보겠습니다.

- SID: HANA 시스템에 대한 시스템 식별자입니다.
- HLI: Hana 대규모 인스턴스:
- DR: 재해 복구 사이트입니다.
- 일반 DR: DR 용도로만 사용되는 전용 리소스가 있는 시스템 설치입니다.
- 다목적 DR: DR 이벤트에 사용하도록 구성된 프로덕션 인스턴스와 비프로덕션 환경을 사용하도록 구성된 DR 사이트의 시스템입니다. 
- 단일 SID:  단일 인스턴스가 설치된 시스템입니다.
- 다중 SID: 여러 인스턴스가 구성된 시스템입니다. MCOS 환경이라고도 합니다.


## <a name="overview"></a>개요
HANA 큰 인스턴스는 사용자의 비즈니스 요구를 충족하기 위해 다양한 아키텍처를 지원합니다. 다음 목록에서는 시나리오 및 해당 구성 세부 정보를 다룹니다. 

파생된 아키텍처 디자인은 순수하게 인프라 관점을 따르며, HANA 배포에 대해서는 SAP 또는 구현 파트너에게 문의해야 합니다. 사용자 시나리오가 목록에 없는 경우 Microsoft 계정 팀에 문의하여 아키텍처를 검토하고 사용자에게 맞는 솔루션을 파생하세요.

**이러한 아키텍처는 TDI(조정된 데이터 통합) 디자인에 완전히 부합되며 SAP에서 지원됩니다.**

이 문서에서는 지원되는 각 아키텍처의 두 구성 요소에 대해 자세히 설명합니다.

- 이더넷
- Storage

### <a name="ethernet"></a>이더넷

프로비전된 각 서버는 이더넷 인터페이스 집합으로 미리 구성됩니다. 다음은 각 HLI 단위에서 구성된 이더넷 인터페이스에 대한 세부 정보입니다.

- **A**: 이 인터페이스는 클라이언트 액세스 간에 사용됩니다.
- **B**: 이 인터페이스는 노드 간 통신에 사용됩니다. 이 인터페이스는 모든 서버(요청된 토폴로지와 관계 없음)에서 구성되지만 
- 스케일 아웃 시나리오에서만 사용됩니다.
- **C**: 이 인터페이스는 노드-스토리지 연결에 사용됩니다.
- **D**: 이 인터페이스는 STONITH 설치에 대한 노드-ISCSI 디바이스 연결에 사용됩니다. 이 인터페이스는 HSR 설치가 필요할 때만 구성됩니다.  

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드-노드 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드-노드 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

HLI 단위에 구성된 토폴로지에 따라 인터페이스를 사용합니다. 예를 들어, 인터페이스 "B"는 스케일 아웃 토폴로지를 구성한 경우에 유용한 노드-노드 통신을 위해 설정됩니다. 단일 노드 강화 구성의 경우 이 인터페이스가 사용되지 않습니다. 이 문서 뒷부분에서 필요한 시나리오를 검토하여 인터페이스 사용에 대한 자세한 정보를 얻으세요. 

필요한 경우 추가 NIC 카드를 직접 정의할 수 있습니다. 그러나 기존 NIC에 대한 구성은 변경할 수 없습니다.

>[!NOTE]
>실제 인터페이스 또는 결합에 해당하는 추가 인터페이스를 계속 찾을 수도 있습니다. 사용된 사례에 대해서는 위에서 언급한 인터페이스를 고려하고, 나머지는 무시하거나 영향을 받지 않도록 할 수 있습니다.

할당된 두 IP 주소가 있는 단위에 대한 배포는 다음과 같습니다.

- 이더넷 “A”에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 IP 주소가 할당되어 있어야 합니다. 이 IP 주소는 OS의 /etc/hosts에서 유지 관리에 사용됩니다.

- 이더넷 “C”에는 NFS와의 통신에 사용되는 IP 주소가 할당되어 있어야 합니다. 따라서 이러한 주소는 테넌트 내에서 인스턴스 트래픽에 대한 인스턴스를 허용하기 위해 etc/hosts에서 유지 관리할 필요가 **없습니다**.

HANA 시스템 복제 또는 HANA 확장 배포의 경우 두 개의 IP 주소가 할당된 블레이드 구성은 적합하지 않습니다. IP 주소가 두 개만 할당되어 있는 경우 이러한 구성을 배포하려면 할당된 세 번째 VLAN의 세 번째 IP 주소를 Azure의 SAP HANA Service Management에 문의하세요. 세 개의 NIC 포트에 새 개의 IP 주소가 할당되어 있는 HANA 큰 인스턴스 유닛에는 다음 사용 규칙이 적용됩니다.

- 이더넷 “A”에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 IP 주소가 할당되어 있어야 합니다. 따라서 이 IP 주소는 OS의 /etc/hosts에서 유지 관리에 사용됩니다.

- 이더넷 “B”는 다른 인스턴스 간의 통신을 위해 etc/hosts에서 유지 관리되도록 독점적으로 사용되어야 합니다. 이 주소는 HANA가 노드 간 구성에 사용하는 IP 주소로 규모 확장 HANA 구성에서 유지해야 하는 IP 주소이기도 합니다.

- 이더넷 “C”에는 NFS 저장소와의 통신에 사용되는 IP 주소가 할당되어 있어야 합니다. 따라서 이 주소 유형은 etc/hosts에서 유지 관리되지 않아야 합니다.

- 이더넷 “D”는 Pacemaker용 STONITH 디바이스에 액세스하는 데만 사용해야 합니다. 이 인터페이스는 HSR(HANA 시스템 복제)을 구성하며, SBD 기반 디바이스를 사용하여 운영 체제에서 자동 장애 조치(Failover)를 수행하려고 할 때 필요합니다.


### <a name="storage"></a>Storage
저장소는 요청된 토폴로지에 따라 미리 구성됩니다. 볼륨 크기와 탑재 지점은 서버 수, SKU 및 구성된 토폴로지에 따라 달라집니다. 이 문서 뒷부분에서 필요한 시나리오를 검토하여 자세한 정보를 얻으세요. 더 많은 저장소가 필요한 경우 1TB씩 증분해서 구입할 수 있습니다.

>[!NOTE]
>탑재 지점r/sap/\<SID >/hana/공유 탑재 지점에 대 한 기호화 된 링크입니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

아키텍처 다이어그램의 그래픽에 다음과 같은 표기법이 사용됩니다.

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

다음 목록은 지원되는 시나리오를 보여 줍니다.

1. SID가 1개인 단일 노드
2. 단일 노드 MCOS
3. DR(일반)이 있는 단일 노드
4. DR(다용도)이 있는 단일 노드
5. STONITH가 있는 HSR
6. DR(일반/다용도)이 있는 HSR 
7. 호스트 자동 장애 조치(Failover)(1+1) 
8. 대기 상태로 스케일 아웃
9. 대기 없이 스케일 아웃
10. DR을 사용하여 스케일 아웃



## <a name="1-single-node-with-one-sid"></a>1. SID가 1개인 단일 노드

이 토폴로지는 단일 SID가 있는 강화 구성의 단일 노드를 지원합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | HANA 설치 | 
|/hana/data/SID/mnt00001 | 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 로그 파일 설치 | 
|/hana/logbackups/SID | 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="2-single-node-mcos"></a>2. 단일 노드 MCOS

이 토폴로지는 여러 SID가 있는 강화 구성의 단일 노드를 지원합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID1 | SID1의 HANA 설치 | 
|/hana/data/SID1/mnt00001 | SID1의 데이터 파일 설치 | 
|/hana/log/SID1/mnt00001 | SID1의 로그 파일 설치 | 
|/hana/logbackups/SID1 | SID1의 다시 실행 로그 |
|/hana/shared/SID2 | SID2의 HANA 설치 | 
|/hana/data/SID2/mnt00001 | SID2의 데이터 파일 설치 | 
|/hana/log/SID2/mnt00001 | SID2의 로그 파일 설치 | 
|/hana/logbackups/SID2 | SID2의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 볼륨 크기 분산은 메모리의 데이터베이스 크기를 기준으로 합니다. [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 섹션을 참조하여 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보세요.

## <a name="3-single-node-with-dr-normal"></a>3. DR(일반)이 있는 단일 노드
 
이 토폴로지는 기본 SID에 대한 DR 사이트의 저장소 기반 복제를 포함하는 하나 이상의 SID가 있는 스케일 아웃 구성의 단일 노드를 지원합니다. 이 다이어그램에서는 단일 SID만 기본 사이트에 표시되지만 다중 SID(MCOS)도 지원됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | SID의 로그 파일 설치 | 
|/hana/logbackups/SID | SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 분산은 메모리의 데이터베이스 크기를 기준으로 합니다. [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 섹션을 참조하여 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보세요.
- DR: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR: 데이터, 로그 백업 및 공유 볼륨("스토리지 복제"로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 장애 조치(Failover) 동안에만 이러한 볼륨이 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 프로시저](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 문서를 참고하세요.
- **SKU 유형 I 클래스**에 대한 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="4-single-node-with-dr-multipurpose"></a>4. DR(다용도)이 있는 단일 노드
 
이 토폴로지는 기본 SID에 대한 DR 사이트의 저장소 기반 복제를 포함하는 하나 이상의 SID가 있는 스케일 아웃 구성의 단일 노드를 지원합니다. 이 다이어그램에서는 단일 SID만 기본 사이트에 표시되지만 다중 SID(MCOS)도 지원됩니다. DR 사이트에서는 프로덕션 작업이 기본 사이트에서 실행되는 동안 QA 인스턴스에 HLI 단위가 사용됩니다. DR 장애 조치(Failover)(또는 테스트 장애 조치) 시 DR 사이트의 QA 인스턴스가 DR 사이트에서 제거됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID의 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID의 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID의 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 분산은 메모리의 데이터베이스 크기를 기준으로 합니다. [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 섹션을 참조하여 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보세요.
- DR: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR: 데이터, 로그 백업 및 공유 볼륨("스토리지 복제"로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 장애 조치(Failover) 동안에만 이러한 볼륨이 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 프로시저](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 문서를 참고하세요. 
- DR: QA("QA 인스턴스 설치"로 표시)에 대한 데이터, 로그 백업, 로그, 공유 볼륨이 QA 인스턴스 설치에 대해 구성됩니다.
- **SKU 유형 I 클래스**에 대한 부팅 볼륨이 DR 노드에 복제됩니다.

## <a name="5-hsr-with-stonith"></a>5. STONITH가 있는 HSR
 
이 토폴로지는 HSR(HANA 시스템 복제) 구성에 대한 2개의 노드를 지원합니다. 이 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원됩니다. 즉, 시나리오는 MCOS 지원되지 않습니다.

**이제부터 이 아키텍처는 SUSE 운영 체제에 대해서만 지원됩니다.**


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**보조 노드**|
|/hana/shared/SID | 보조 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 분산은 메모리의 데이터베이스 크기를 기준으로 합니다. [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 섹션을 참조하여 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보세요.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH의 사용은 선택 사항입니다.


## <a name="6-hsr-with-dr"></a>6. DR이 있는 HSR
 
이 토폴로지는 HSR(HANA 시스템 복제) 구성에 대한 2개의 노드를 지원합니다. 일반 및 다용도 DR이 둘 다 지원됩니다. 이 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원됩니다. 즉, MCOS 시나리오는 이러한 구성에서 지원되지 않습니다.

이 다이어그램에는 DR 사이트에서 프로덕션 작업이 기본 사이트에서 실행되는 동안 QA 인스턴스에 HLI 단위가 사용되는 다용도 시나리오가 표시됩니다. DR 장애 조치(Failover)(또는 테스트 장애 조치) 시 DR 사이트의 QA 인스턴스가 DR 사이트에서 제거됩니다. 



### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트의 주 노드**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**기본 사이트의 보조 노드**|
|/hana/shared/SID | 보조 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID의 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID의 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID의 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 분산은 메모리의 데이터베이스 크기를 기준으로 합니다. [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 섹션을 참조하여 다중 SID 환경에서 지원되는 메모리의 데이터베이스 크기에 대해 알아보세요.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH의 사용은 선택 사항입니다.
- DR: 주 및 보조 노드 복제에 **두 스토리지 볼륨 세트가 필요**합니다.
- DR: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR: 데이터, 로그 백업 및 공유 볼륨("스토리지 복제"로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 장애 조치(Failover) 동안에만 이러한 볼륨이 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 프로시저](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 문서를 참고하세요. 
- DR: QA("QA 인스턴스 설치"로 표시)에 대한 데이터, 로그 백업, 로그, 공유 볼륨이 QA 인스턴스 설치에 대해 구성됩니다.
- **SKU 유형 I 클래스**에 대한 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="7-host-auto-failover-11"></a>7. 호스트 자동 장애 조치(Failover)(1+1)
 
이 토폴로지는 호스트 자동 장애 조치(Failover) 구성의 두 노드를 지원합니다. 마스터/작업자 역할의 하나의 노드와 대기용 추가 노드가 있습니다. **SAP은 S/4 HANA에 대해서만 이 시나리오를 지원합니다.** 자세한 내용은 OSS 참고 "[2408419 - SAP S/4HANA - 다중 노드 지원](https://launchpad.support.sap.com/#/notes/2408419)"을 참조하세요.



### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![sca](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터 및 대기 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |



### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 대기: 볼륨 및 탑재 지점이 대기 단위의 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시).
 

## <a name="8-scale-out-with-standby"></a>8. 대기 상태로 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 마스터 역할의 노드 1개, 작업자 역할의 노드 1개 이상 및 대기용 노드 1개 이상이 있습니다. 그러나 마스터 노드는 지정된 한 시점에 하나만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터, 작업자 및 대기 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


## <a name="9-scale-out-without-standby"></a>9. 대기 없이 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 마스터 역할의 노드 1개, 작업자 역할의 노드 1개 이상이 있습니다. 그러나 마스터 노드는 지정된 한 시점에 하나만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터 및 작업자 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="10-scale-out-with-dr"></a>10. DR을 사용하여 스케일 아웃
 
이 토폴로지는 DR이 있는 스케일 아웃의 여러 노드를 지원합니다. 일반 및 다용도 DR이 둘 다 지원됩니다. 이 다이어그램에는 단일 용도 DR만 표시됩니다. 대기 노드가 있거나 없는 상태로 이 토폴로지를 요청할 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리적 인터페이스 | SKU 형식 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성됩니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 노드**|
|/hana/shared | 프로덕션 SID의 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID의 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID의 로그 파일 설치 | 


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
-  DR: 볼륨 및 탑재 지점이 DR HLI 단위의 프로덕션 HANA 인스턴스 설치에 대해 구성됩니다("HANA 설치에 필요"로 표시). 
- DR: 데이터, 로그 백업 및 공유 볼륨("스토리지 복제"로 표시)이 프로덕션 사이트의 스냅샷을 통해 복제됩니다. 장애 조치(Failover) 동안에만 이러한 볼륨이 탑재됩니다. 자세한 내용은 [재해 복구 장애 조치 프로시저](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 문서를 참고하세요. 
- **SKU 유형 I 클래스**에 대한 부팅 볼륨이 DR 노드에 복제됩니다.


## <a name="next-steps"></a>다음 단계
- HLI에 대해서는 [인프라 및 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)을 참조하세요.
- HLI에 대해서는 [고가용성 및 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)를 참조하세요.
