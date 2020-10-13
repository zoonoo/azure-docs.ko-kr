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
ms.openlocfilehash: 3e4bca058f554f60dfa5c237633d1fecf06dfea7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507167"
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
> Batch는 100개 전용 노드 또는 우선 순위가 낮은 노드 각각에 대해 NSG(네트워크 보안 그룹), 공용 IP 주소 및 부하 분산 장치를 하나씩 할당합니다. 이러한 리소스는 구독의 [리소스 할당량](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)으로 제한됩니다. 대형 풀의 경우 이러한 리소스 중 하나 이상에 대한 할당량을 늘리도록 요청해야 할 수 있습니다.

#### <a name="network-security-groups-batch-default"></a>네트워크 보안 그룹: Batch 기본값

서브넷은 컴퓨팅 노드에 대한 작업을 예약할 수 있도록 Batch 서비스로부터의 인바운드 통신을 허용하고, 워크로드에 필요한 Azure Storage 또는 기타 리소스와 통신할 수 있도록 아웃바운드 통신을 허용해야 합니다. Batch는 Virtual Machine 구성의 풀에 대해 컴퓨팅 노드에 연결된 NIC(네트워크 인터페이스) 수준에서 NSG를 추가합니다. 이러한 NSG를 구성하는 추가 규칙은 다음과 같습니다.

* `BatchNodeManagement` 서비스 태그에 해당하는 Batch 서비스 IP 주소로부터의 29876 및 29877 포트의 인바운드 TCP 트래픽
* 원격 액세스 허용을 위한 포트 22(Linux 노드) 또는 포트 3389(Windows 노드) 인바운드 TCP 트래픽 Linux에 대한 특정 유형의 다중 인스턴스 작업(예: MPI)의 경우 Batch 컴퓨팅 노드가 포함된 서브넷에서 IP에 대한 22 SSH 포트 트래픽을 허용해야 합니다. 이는 서브넷 수준 NSG 규칙에 따라 차단될 수 있습니다(아래 참조).
* 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽 이는 서브넷 수준 NSG 규칙에 따라 수정될 수 있습니다(아래 참조).
* 모든 포트에서 인터넷으로의 아웃바운드 트래픽. 이는 서브넷 수준 NSG 규칙에 따라 수정될 수 있습니다(아래 참조).

> [!IMPORTANT]
> Batch에서 구성한 NSG에서 인바운드 또는 아웃바운드 규칙을 수정하거나 추가하는 경우 주의해야 합니다. NSG에서 지정된 서브넷의 컴퓨팅 노드와 통신할 수 없도록 거부하는 경우 Batch 서비스는 컴퓨팅 노드의 상태를 **사용할 수 없음**으로 설정합니다. 또한 Batch에서 만든 리소스에는 리소스 잠금을 적용하지 않아야 합니다. 적용하는 경우 풀 삭제와 같은 사용자가 시작한 작업으로 인해 리소스가 정리되지 않을 수 있습니다.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>네트워크 보안 그룹: 서브넷 수준 규칙 지정

Batch는 자체 NSG를 구성하므로 가상 네트워크 서브넷 수준에서 NSG를 지정할 필요가 없습니다(위 참조). Batch 컴퓨팅 노드가 배포된 서브넷과 연결된 NSG가 있거나 적용된 기본값을 재정의하는 사용자 지정 NSG 규칙을 적용하려는 경우 적어도 다음 표에 나와 있는 인바운드 및 아웃바운드 보안 규칙을 사용하여 이 NSG를 구성해야 합니다.

외부 원본에서 컴퓨팅 노드에 대한 원격 액세스를 허용해야 하는 경우에만 3389(Windows) 또는 22(Linux) 포트에서 인바운드 트래픽을 구성합니다. 특정 MPI 런타임을 사용하여 다중 인스턴스 작업을 지원해야 하는 경우 Linux에서 22 포트 규칙을 사용하도록 설정해야 할 수 있습니다. 풀 컴퓨팅 노드를 사용하기 위해 이러한 포트에서 트래픽을 반드시 허용할 필요는 없습니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 서비스 태그 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- | --- |
| 해당 없음 | `BatchNodeManagement` [서비스 태그](../articles/virtual-network/security-overview.md#service-tags)(Batch 계정과 동일한 지역에서 지역별 변형을 사용하는 경우) | * | 모두 | 29876-29877 | TCP | Allow |
| 필요한 경우 Linux 다중 인스턴스 작업을 위해 컴퓨팅 노드 및/또는 컴퓨팅 노드 서브넷에 원격으로 액세스하기 위한 사용자 원본 IP입니다. | 해당 없음 | * | 모두 | 3389(Windows), 22(Linux) | TCP | Allow |

> [!WARNING]
> Batch 서비스 IP 주소는 시간이 지남에 따라 변경될 수 있습니다. 따라서 `BatchNodeManagement` 서비스 태그(또는 지역 변형)를 NSG 규칙에 사용하는 것이 좋습니다. NSG 규칙을 특정 Batch 서비스 IP 주소로 채우지 마세요.

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 서비스 태그 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- | --- |
| 모두 | * | [Service 태그](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(Batch 계정과 동일한 지역에서 지역별 변형을 사용하는 경우) | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>Cloud Services 구성의 풀

**VNet 지원** -클래식 VNet 전용

**서브넷 식별자** - Batch API를 사용하여 서브넷을 지정할 경우 서브넷의 *리소스 식별자*를 사용합니다. 서브넷 식별자 형식은 다음과 같습니다.

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**권한** - `Microsoft Azure Batch` 서비스 주체에는 지정된 VNet에 대한 `Classic Virtual Machine Contributor` Azure 역할이 있어야 합니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹

서브넷에서는 컴퓨팅 노드에서 작업을 예약하기 위해 Batch 서비스로부터의 인바운드 통신을 허용하고 Azure Storage 또는 기타 리소스와의 통신을 위해 아웃바운트 통신을 허용해야 합니다.

Batch는 Batch IP 주소로부터 풀 노드로 가는 인바운드 통신만 구성하므로 NSG를 지정할 필요가 없습니다. 그러나 지정된 서브넷에 연결된 NSG 및/또는 방화벽이 있는 경우 다음 표에서처럼 인바운드 및 아웃바운드 보안 규칙을 구성합니다. NSG에서 지정된 서브넷의 컴퓨팅 노드와 통신할 수 없도록 거부하는 경우 Batch 서비스는 컴퓨팅 노드의 상태를 **사용할 수 없음**으로 설정합니다.

풀 노드에 대한 RDP 액세스를 허용해야 하는 경우 Windows용 3389 포트에서 인바운드 트래픽을 구성합니다. 풀 노드를 사용할 수 있는 경우에는 필요하지 않습니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- |
모두 <br /><br />이렇게 하면 결과적으로 “모두 허용”이 필요하지만 Batch 서비스는 모든 Batch 외 서비스 IP 주소를 필터링하는 각 노드 수준에서 ACL을 적용합니다. | * | 모두 | 10100, 20100, 30100 | TCP | Allow |
| 선택 사항, 컴퓨팅 노드에 대한 RDP 액세스를 허용합니다. | * | 모두 | 3389 | TCP | Allow |

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- |
| 모두 | * | 모두 | 443  | 모두 | Allow |
