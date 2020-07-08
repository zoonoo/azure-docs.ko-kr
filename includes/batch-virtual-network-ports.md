---
title: 파일 포함
description: 포함 파일
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 06/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1b21141a4b3f9ae92cdcf1d5a93a457012cb136a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506610"
---
### <a name="general-requirements"></a>일반 요구 사항

* VNet은 풀을 만들 때 사용한 Batch 계정과 동일한 구독 및 지역에 있어야 합니다.

* VNet을 사용하는 풀은 최대 4096개의 노드를 가질 수 있습니다.

* 풀에 대해 지정한 서브넷에는 풀에 대상이 되는 VM 수를 수용할 만큼 충분한 할당되지 않은 IP 주소가 있어야 합니다. 즉 풀의 `targetDedicatedNodes` 및 `targetLowPriorityNodes` 속성 합계입니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 풀은 컴퓨팅 노드를 부분적으로 할당하고 크기 조정 오류를 반환합니다.

* VNet을 서비스하는 모든 사용자 지정 DNS 서버에서 Azure Storage 엔드포인트를 확인할 수 있어야 합니다. 특히 `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` 및 `<account>.blob.core.windows.net` 양식의 URL을 확인할 수 있어야 합니다.

추가적인 VNet 요구 사항은 Batch 풀이 Virtual Machine 구성인지 또는 Cloud Services 구성인지에 따라 다릅니다. 새 풀을 VNet에 배포하는 경우 Virtual Machine 구성을 권장합니다.

### <a name="pools-in-the-virtual-machine-configuration"></a>Virtual Machine 구성의 풀

**VNet 지원** - Azure Resource Manager 기반 VNet만

**서브넷 식별자** - Batch API를 사용하여 서브넷을 지정할 경우 서브넷의 *리소스 식별자*를 사용합니다. 서브넷 식별자 형식은 다음과 같습니다.

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**사용 권한** - VNet 구독에서 보안 정책이나 잠금 또는 리소스 그룹이 VNet 관리를 위한 사용자 권한을 제한하고 있는지 확인합니다.

**추가 네트워킹 리소스** - Batch는 VNet을 포함하는 리소스 그룹에서 추가 네트워킹 리소스를 자동으로 할당합니다.

> [!IMPORTANT]
> 100 개의 전용 또는 우선 순위가 낮은 노드에 대해 일괄 처리는 하나의 NSG (네트워크 보안 그룹), 하나의 공용 IP 주소 및 부하 분산 장치 하나를 할당 합니다. 이러한 리소스는 구독의 [리소스 할당량](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)으로 제한됩니다. 대량 풀의 경우 이러한 리소스 중 하나 이상에 대 한 할당량 증가를 요청 해야 할 수 있습니다.

#### <a name="network-security-groups-batch-default"></a>네트워크 보안 그룹: 일괄 처리 기본값

서브넷은 Batch 서비스의 인바운드 통신이 계산 노드에 대 한 작업을 예약 하 고, 아웃 바운드 통신을 사용 하 여 작업에 필요한 대로 Azure Storage 또는 다른 리소스와 통신할 수 있도록 해야 합니다. 가상 컴퓨터 구성의 풀에 대해 Batch는 계산 노드에 연결 된 Nic (네트워크 인터페이스) 수준에서 NSGs를 추가 합니다. 이러한 NSGs는 다음과 같은 추가 규칙으로 구성 됩니다.

* 서비스 태그에 해당 하는 Batch 서비스 IP 주소에서 포트 29876 및 29877에 대 한 인바운드 TCP 트래픽 `BatchNodeManagement`
* 원격 액세스 허용을 위한 포트 22(Linux 노드) 또는 포트 3389(Windows 노드) 인바운드 TCP 트래픽 특정 유형의 다중 인스턴스 작업 (예: MPI)에 대해, Batch 계산 노드를 포함 하는 서브넷의 Ip에 대해 SSH 포트 22 트래픽만 허용 해야 합니다. 서브넷 수준 NSG 규칙에 따라 차단 될 수 있습니다 (아래 참조).
* 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽 이는 서브넷 수준 NSG 규칙에 따라 수정 될 수 있습니다 (아래 참조).
* 모든 포트에서 인터넷으로의 아웃 바운드 트래픽 이는 서브넷 수준 NSG 규칙에 따라 수정 될 수 있습니다 (아래 참조).

> [!IMPORTANT]
> 일괄 처리 구성 된 NSGs에서 인바운드 또는 아웃 바운드 규칙을 수정 하거나 추가 하는 경우 주의 해야 합니다. 지정 된 서브넷의 계산 노드에 대 한 통신을 NSG에서 거부 하는 경우 Batch 서비스는 계산 노드의 상태를 **사용할**수 없음으로 설정 합니다. 또한 일괄 처리로 생성 된 모든 리소스에는 리소스 잠금을 적용 하지 않아야 합니다 .이로 인해 풀 삭제와 같은 사용자가 시작한 작업의 결과로 리소스 정리가 수행 되지 않을 수 있기 때문입니다.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>네트워크 보안 그룹: 서브넷 수준 규칙 지정

Batch에서 자체 NSGs를 구성 하기 때문에 가상 네트워크 서브넷 수준에서 NSGs를 지정할 필요가 없습니다 (위 참조). Batch 계산 노드가 배포 되는 서브넷과 연결 된 NSG가 있거나 적용 된 기본값을 재정의 하는 사용자 지정 NSG 규칙을 적용 하려는 경우 다음 표에 표시 된 최소 인바운드 및 아웃 바운드 보안 규칙을 사용 하 여이 NSG를 구성 해야 합니다.

외부 원본에서 계산 노드에 대 한 원격 액세스를 허용 해야 하는 경우에만 포트 3389 (Windows) 또는 22 (Linux)에서 인바운드 트래픽을 구성 합니다. 특정 MPI 런타임을 사용 하 여 다중 인스턴스 작업을 지원 해야 하는 경우 Linux에서 포트 22 규칙을 사용 하도록 설정 해야 할 수 있습니다. 풀 계산 노드에는 이러한 포트에 대 한 트래픽을 허용 하는 것이 반드시 필요한 것은 아닙니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 서비스 태그 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- | --- |
| 해당 없음 | `BatchNodeManagement`[서비스 태그](../articles/virtual-network/security-overview.md#service-tags) (지역 변형을 사용 하는 경우 Batch 계정과 동일한 지역에 있는 경우) | * | 모두 | 29876-29877 | TCP | 허용 |
| 필요한 경우 컴퓨터에 원격으로 액세스 하기 위한 사용자 원본 Ip 및 Linux 다중 인스턴스 작업을 위한 계산 노드 서브넷 | 해당 없음 | * | 모두 | 3389(Windows), 22(Linux) | TCP | 허용 |

> [!WARNING]
> Batch 서비스 IP 주소는 시간이 지남에 따라 변경될 수 있습니다. 따라서 `BatchNodeManagement` NSG 규칙에 대 한 서비스 태그 (또는 지역 변형)를 사용 하는 것이 좋습니다. 특정 Batch 서비스 IP 주소를 사용 하 여 NSG 규칙을 채우지 않도록 합니다.

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 서비스 태그 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- | --- |
| 모두 | * | [서비스 태그](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(지역 변형을 사용 하는 경우 Batch 계정과 동일한 지역에서) | 443 | TCP | 허용 |

### <a name="pools-in-the-cloud-services-configuration"></a>Cloud Services 구성의 풀

**VNet 지원** -클래식 VNet 전용

**서브넷 식별자** - Batch API를 사용하여 서브넷을 지정할 경우 서브넷의 *리소스 식별자*를 사용합니다. 서브넷 식별자 형식은 다음과 같습니다.

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**사용 권한** - `Microsoft Azure Batch` 서비스 주체에는 지정된 VNet에 대한 `Classic Virtual Machine Contributor` RBAC(역할 기반 Access Control) 역할이 있어야 합니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹

서브넷에서는 컴퓨팅 노드에서 작업을 예약하기 위해 Batch 서비스로부터의 인바운드 통신을 허용하고 Azure Storage 또는 기타 리소스와의 통신을 위해 아웃바운트 통신을 허용해야 합니다.

Batch는 Batch IP 주소로부터 풀 노드로 가는 인바운드 통신만 구성하므로 NSG를 지정할 필요가 없습니다. 그러나 지정된 서브넷에 연결된 NSG 및/또는 방화벽이 있는 경우 다음 표에서처럼 인바운드 및 아웃바운드 보안 규칙을 구성합니다. 지정 된 서브넷의 계산 노드에 대 한 통신이 NSG에서 거부 되는 경우 Batch 서비스는 계산 노드의 상태를 **사용할**수 없음으로 설정 합니다.

풀 노드에 대 한 RDP 액세스를 허용 해야 하는 경우 Windows 용 포트 3389에서 인바운드 트래픽을 구성 합니다. 풀 노드를 사용할 수 있는 경우에는 필요 하지 않습니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- |
모두 <br /><br />이렇게 하면 결과적으로 “모두 허용”이 필요하지만 Batch 서비스는 모든 Batch 외 서비스 IP 주소를 필터링하는 각 노드 수준에서 ACL을 적용합니다. | * | 모두 | 10100, 20100, 30100 | TCP | 허용 |
| 선택 사항으로, RDP에서 계산 노드에 대 한 액세스를 허용 합니다. | * | 모두 | 3389 | TCP | 허용 |

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- |
| 모두 | * | 모두 | 443  | 모두 | Allow |
