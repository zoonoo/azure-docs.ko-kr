---
title: Oracle용 BareMetal의 이더넷 구성
description: Oracle 워크로드의 BareMetal 인스턴스에 관련된 이더넷 인터페이스 구성에 관해 알아봅니다.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c57cbc86d17090d6960a334c2790d80b43420aca
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588890"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Oracle용 BareMetal의 이더넷 구성

이 문서에서는 Oracle 워크로드의 BareMetal 인스턴스에 관련된 이더넷 인터페이스 구성을 살펴봅니다.

프로비저닝된 각 Oracle용 BareMetal 인스턴스는 이더넷 인터페이스 세트로 미리 구성되어 제공됩니다. 이더넷 인터페이스는 다음 네 가지 유형으로 분류됩니다.

- 클라이언트 액세스에 사용되거나 클라이언트 액세스에 의해 사용됩니다.
- 노드 간 통신에 사용됩니다. 이 인터페이스는 요청된 토폴로지와 관계없이 모든 서버에서 구성됩니다. 스케일 아웃 시나리오에만 사용됩니다.
- 노드와 스토리지 간 연결에 사용됩니다.
- 지역 간 연결을 위한 글로벌 환경에 대한 DR(재해 복구) 설정 및 연결에 사용됩니다.

## <a name="architecture"></a>Architecture

다음 다이어그램에서는 BareMetal Infrastructure 미리 구성된 이더넷 인터페이스의 아키텍처를 보여 줍니다. 

[![Oracle 워크로드를 위한 미리 구성된 이더넷 인터페이스의 아키텍처를 보여 주는 다이어그램](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

기본 구성에는 SSH(Secure Shell)를 사용하여 BareMetal 인스턴스에 액세스할 수 있는 Azure VNET(Virtual Network)에서 연결하는 하나의 클라이언트 IP 인터페이스(eth1)가 제공됩니다.

> [!NOTE]
> 다른 Azure VNET에서 연결하는 또 다른 클라이언트 인터페이스(eth10)의 경우 Microsoft CSA에 문의하여 서비스 요청을 제출합니다. 예를 들면 개발/테스트 및 프로덕션/DR 환경을 원하는 경우입니다.

| **NIC 논리 인터페이스** | **RHEL OS를 포함하는 이름** | **사용 사례** |
| --- | --- | --- |
| A | net1.tenant | 클라이언트-BareMetal 인스턴스 간 |
| C | net2.tenant | 노드-스토리지 간. 스토리지 환경을 관리하기 위해 스토리지 컨트롤러에 대한 조정 및 액세스를 지원합니다. |
| b | net3.tenant | 노드 간(프라이빗 상호 연결) |
| C | net4.tenant | 예약/iSCSI |
| C | net5.tenant | 예약/로그 백업 |
| C | net6.tenant | 노드-storage_Data Backup 간(RMAN, 스냅샷) |
| C | net7.tenant | 노드-storage_dNFS-Pri 간. NetApp 스토리지 배열에 대한 연결을 제공합니다. |
| C | net8.tenant | 노드-storage_dNFS-Sec 간. NetApp 스토리지 배열에 대한 연결을 제공합니다. |
| D | net9.tenant | 또 다른 지역의 BMI에 액세스하도록 글로벌 환경을 설정하기 위한 DR 연결입니다. |
| A | \*net10.tenant | \* 클라이언트-BareMetal 인스턴스 간
 |

필요한 경우 더 많은 NIC(네트워크 인터페이스 컨트롤러) 카드를 직접 정의할 수 있습니다. 그러나 기존 NIC의 구성은 변경할 수 ‘없습니다’.

## <a name="usage-rules"></a>사용 규칙

BareMetal 인스턴스의 경우 기본값에는 논리적 NIC 4개의 할당된 IP 주소 9개가 포함됩니다. 다음 사용 규칙이 적용됩니다.

- 이더넷 “A”에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 할당된 IP 주소가 있어야 합니다. 이 IP 주소는 OS의 etc/hosts 디렉터리에서 유지 관리하면 안 됩니다.
- 이더넷 “B”는 여러 인스턴스 간 통신을 위해 etc/hosts 디렉터리에서 단독으로 유지 관리해야 합니다. 이러한 IP 주소는 노드 간 구성에 사용되는 IP 주소로서 스케일 아웃 Oracle RAC(Real Application Clusters) 구성에서 유지 관리합니다.
- 이더넷 “C”에는 NFS 스토리지에 대한 통신에 사용되는 할당된 IP 주소가 있어야 합니다. 이 유형의 주소는 etc/hosts 디렉터리에서 유지 관리하면 안 됩니다.
- 이더넷 “D”는 DR 지역에서 BareMetal 인스턴스에 액세스하도록 글로벌 환경을 설정하는 데만 사용되어야 합니다.

## <a name="next-step"></a>다음 단계

Oracle용 BareMetal Infrastructure 아키텍처에 관해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle용 BareMetal Infrastructure 아키텍처](oracle-baremetal-architecture.md)
