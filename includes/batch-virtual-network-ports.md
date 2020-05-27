---
title: 포함 파일
description: 포함 파일
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086265"
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

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**사용 권한** - VNet 구독에서 보안 정책이나 잠금 또는 리소스 그룹이 VNet 관리를 위한 사용자 권한을 제한하고 있는지 확인합니다.

**추가 네트워킹 리소스** - Batch는 VNet을 포함하는 리소스 그룹에서 추가 네트워킹 리소스를 자동으로 할당합니다.

> [!IMPORTANT]
>각 50개의 전용 노드(또는 우선 순위가 낮은 각 20개 노드)에 대해 일괄 처리는 하나의 네트워크 보안 그룹(NSG), 하나의 공용 IP 주소 및 하나의 로드 밸런서를 할당합니다. 이러한 리소스는 구독의 [리소스 할당량](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)으로 제한됩니다. 대규모 풀의 경우 이러한 리소스 중 하나 이상에 대한 할당량 증가를 요청해야 할 수 있습니다.

#### <a name="network-security-groups-batch-default"></a>네트워크 보안 그룹: 일괄 처리 기본값

서브넷은 Batch 서비스의 인바운드 통신이 계산 노드에서 작업을 예약할 수 있도록 허용해야 하며, 워크로드에 필요한 경우 Azure Storage 또는 기타 리소스와 통신하기 위한 아웃바운드 통신을 허용해야 합니다. 가상 컴퓨터 구성의 풀의 경우 Batch는 계산 노드에 연결된 네트워크 인터페이스(NIC) 수준에서 NSG를 추가합니다. 이러한 NSG는 다음과 같은 추가 규칙으로 구성됩니다.

* 서비스 태그에 해당하는 일괄 처리 서비스 IP 주소에서 포트 29876 및 `BatchNodeManagement` 29877의 인바운드 TCP 트래픽입니다.
* 원격 액세스 허용을 위한 포트 22(Linux 노드) 또는 포트 3389(Windows 노드) 인바운드 TCP 트래픽 Linux의 특정 유형의 다중 인스턴스 작업(예: MPI)의 경우 Batch 계산 노드가 포함된 서브넷의 IP에 대해 SSH 포트 22 트래픽을 허용해야 합니다. 이는 서브넷 수준 NSG 규칙(아래 참조)에 따라 차단될 수 있습니다.
* 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽 이는 서브넷 수준 NSG 규칙에 따라 수정될 수 있습니다(아래 참조).
* 모든 포트에서 인터넷에 대한 아웃바운드 트래픽입니다. 이는 서브넷 수준 NSG 규칙에 따라 수정될 수 있습니다(아래 참조).

> [!IMPORTANT]
> Batch 구성 NSG에서 인바운드 또는 아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. 지정된 서브넷에서 컴퓨팅 노드와의 통신을 NSG에서 거부한 경우 Batch 서비스는 컴퓨팅 노드의 상태를 **사용할 수 없음**으로 설정합니다. 또한 Batch에서 만든 리소스에 리소스 잠금을 적용하지 않아야 하며, 그렇지 않으면 풀 삭제와 같은 사용자가 시작한 작업의 결과로 리소스정리가 방지될 수 있습니다.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>네트워크 보안 그룹: 서브넷 수준 규칙 지정

Batch는 자체 NSG를 구성하므로 가상 네트워크 서브넷 수준에서 NSG를 지정할 필요가 없습니다(위 참조). Batch 계산 노드가 배포되는 서브넷과 연결된 NSG가 있거나 적용된 기본값을 재정의하기 위해 사용자 지정 NSG 규칙을 적용하려는 경우 다음과 같이 최소한 인바운드 및 아웃바운드 보안 규칙으로 이 NSG를 구성해야 합니다. 테이블.

외부 소스에서 계산 노드에 대한 원격 액세스를 허용해야 하는 경우에만 포트 3389(Windows) 또는 22(Linux)에서 인바운드 트래픽을 구성합니다. 특정 MPI 런타임이 있는 다중 인스턴스 작업에 대한 지원이 필요한 경우 Linux에서 포트 22 규칙을 사용하도록 설정해야 할 수 있습니다. 풀 계산 노드를 사용할 수 있도록 이러한 포트의 트래픽을 허용하는 것은 엄격히 필요하지 않습니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 서비스 태그 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- | --- |
| 해당 없음 | `BatchNodeManagement`[서비스 태그(일괄](../articles/virtual-network/security-overview.md#service-tags) 처리 계정과 동일한 리전에서 지역 변형을 사용하는 경우) | * | 모두 | 29876-29877 | TCP | Allow |
| 필요한 경우 Linux 다중 인스턴스 작업에 대한 컴퓨팅 노드 및/또는 계산 노드 서브넷에 원격으로 액세스하기 위한 사용자 소스 IP입니다. | 해당 없음 | * | 모두 | 3389(Windows), 22(Linux) | TCP | Allow |

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 서비스 태그 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- | --- |
| 모두 | * | [서비스 태그](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(지역 별 변형을 사용하는 경우 Batch 계정과 동일한 지역에서) | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>Cloud Services 구성의 풀

**VNet 지원** -클래식 VNet 전용

**서브넷 식별자** - Batch API를 사용하여 서브넷을 지정할 경우 서브넷의 *리소스 식별자*를 사용합니다. 서브넷 식별자 형식은 다음과 같습니다.

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**사용 권한** - `Microsoft Azure Batch` 서비스 주체에는 지정된 VNet에 대한 `Classic Virtual Machine Contributor` RBAC(역할 기반 Access Control) 역할이 있어야 합니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹

서브넷에서는 컴퓨팅 노드에서 작업을 예약하기 위해 Batch 서비스로부터의 인바운드 통신을 허용하고 Azure Storage 또는 기타 리소스와의 통신을 위해 아웃바운트 통신을 허용해야 합니다.

Batch는 Batch IP 주소로부터 풀 노드로 가는 인바운드 통신만 구성하므로 NSG를 지정할 필요가 없습니다. 그러나 지정된 서브넷에 연결된 NSG 및/또는 방화벽이 있는 경우 다음 표에서처럼 인바운드 및 아웃바운드 보안 규칙을 구성합니다. 지정된 서브넷에서 컴퓨팅 노드와의 통신을 NSG에서 거부한 경우 Batch 서비스는 컴퓨팅 노드의 상태를 **사용할 수 없음**으로 설정합니다.

풀 노드에 대한 RDP 액세스를 허용해야 하는 경우 Windows용 포트 3389에서 인바운드 트래픽을 구성합니다. 풀 노드가 사용 가능할 필요는 없습니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- |
모두 <br /><br />이렇게 하면 결과적으로 “모두 허용”이 필요하지만 Batch 서비스는 모든 Batch 외 서비스 IP 주소를 필터링하는 각 노드 수준에서 ACL을 적용합니다. | * | 모두 | 10100, 20100, 30100 | TCP | Allow |
| 선택 사항으로, RDP 액세스를 허용하여 노드를 계산합니다. | * | 모두 | 3389 | TCP | Allow |

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 작업 |
| --- | --- | --- | --- | --- | --- |
| 모두 | * | 모두 | 443  | 모두 | Allow |
