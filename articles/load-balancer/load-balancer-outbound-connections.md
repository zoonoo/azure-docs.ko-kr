---
title: "Azure에서 아웃바운드 연결 이해 | Microsoft Docs"
description: "이 문서에서는 Azure에서 VM이 공용 인터넷 서비스를 사용하여 통신하는 방법을 설명합니다."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: f02e17bb413f250fc4d980c62cfb46bc5359f7fb
ms.lasthandoff: 03/29/2017

---

# <a name="understanding-outbound-connections-in-azure"></a>Azure에서 아웃바운드 연결 이해

Azure의 VM(가상 컴퓨터)은 공용 IP 주소 공간에 있는 Azure 외부의 끝점과 통신할 수 있습니다. VM이 공용 IP 주소 공간에서 대상에 대한 아웃바운드 흐름을 시작하는 경우 Azure에서는 VM의 개인 IP 주소를 공용 IP 주소에 매핑하고 반환 트래픽이 VM에 도달할 수 있게 합니다.

Azure에서는 아웃바운드를 연결하기 위해 세 가지 방법을 제공합니다. 각 방법에는 고유한 기능 및 제약 조건이 있습니다. 각 방법을 신중하게 검토하여 필요에 맞는 방법을 선택합니다.

| 시나리오 | 메서드 | 참고 |
| --- | --- | --- |
| 독립 실행형 VM(부하 분산 장치 없음, 인스턴스 수준 공용 IP 주소 없음) |기본 SNAT |Azure에서는 SNAT에 공용 IP 주소를 연결합니다. |
| 부하 분산 VM(VM에 인스턴스 수준 공용 IP 주소 없음) |부하 분산 장치를 사용하는 SNAT |Azure에서는 SNAT에 부하 분산 장치의 공용 IP 주소를 사용합니다. |
| 인스턴스 수준 공용 IP 주소를 가진 VM(부하 분산 장치 있음 또는 없음) |SNAT를 사용하지 않습니다. |Azure에서는 VM에 할당된 공용 IP를 사용합니다. |

VM이 공용 IP 주소 공간에 있는 Azure 외부에서 끝점과 통신하지 않게 하려면 액세스를 차단하도록 NSG(네트워크 보안 그룹)를 사용할 수 있습니다. NSG를 사용하는 작업은 [공용 연결 방지](#preventing-public-connectivity)에서 더 자세하게 설명합니다.

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>인스턴스 수준 공용 IP 주소 없는 독립 실행형 VM

이 시나리오에서 VM은 Azure Load Balancer 풀의 일부이며 할당된 ILPIP(인스턴스 수준 공용 IP) 주소가 없습니다. VM이 아웃바운드 흐름을 만든 경우 Azure에서는 아웃바운드 흐름의 개인 원본 IP 주소를 공용 원본 IP 주소로 변환합니다. 이 아웃바운드 흐름에 사용된 공용 IP 주소는 구성할 수 없으며 구독의 공용 IP 리소스 제한에 불리하게 작용하지 않습니다. Azure에서는 SNAT(원본 네트워크 주소 변환)를 사용하여 이 기능을 수행합니다. 공용 IP 주소의 임시 포트는 VM에서 발생하는 개별 흐름을 구별하는 데 사용됩니다. SNAT은 흐름이 만들어질 때 임시 포트를 동적으로 할당합니다. 이 컨텍스트에서 SNAT에 사용되는 임시 포트를 SNAT 포트라고 합니다.

SNAT 포트는 소진될 수 있는 한정된 리소스입니다. 사용 방법을 이해하는 것이 중요합니다. 단일 대상 IP 주소에 대한 흐름 당 하나의 SNAT 포트를 사용합니다. 동일한 대상 IP 주소에 대한 여러 흐름의 경우 각 흐름은 단일 SNAT 포트를 사용합니다. 이렇게 하면 흐름은 동일한 대상 IP 주소에 대한 동일한 공용 IP 주소에서 시작하는 경우에 고유합니다. 다른 대상 IP 주소에 대한 여러 흐름은 각각 대상 당 단일 SNAT 포트를 사용합니다. 대상 IP 주소를 사용하면 흐름은 고유합니다.

[부하 분산 장치에 대한 Log Analytics](load-balancer-monitor-log.md) 및 [SNAT 포트 소모 메시지를 모니터링할 경고 이벤트 로그](load-balancer-monitor-log.md#alert-event-log)를 사용할 수 있습니다. SNAT 포트 리소스를 모두 사용한 경우 SNAT 포트가 기존 흐름에 의해 릴리스될 때까지 아웃바운드 흐름은 실패합니다. 부하 분산 장치는 SNAT 포트를 회수하는 데 4분의 유휴 시간 제한을 사용합니다.

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>인스턴스 수준 공용 IP 주소가 없는 부하 분산 VM

이 시나리오에서 VM은 Azure Load Balancer 풀의 일부입니다. VM에는 할당된 공용 IP 주소가 없습니다. 부하 분산 VM이 아웃바운드 흐름을 만든 경우 Azure에서는 아웃바운드 흐름의 개인 원본 IP 주소를 공용 부하 분산 장치 프론트 엔드의 공용 IP 주소로 변환합니다. Azure에서는 SNAT(원본 네트워크 주소 변환)를 사용하여 이 기능을 수행합니다. 부하 분산 장치 공용 IP 주소의 임시 포트는 VM에서 발생하는 개별 흐름을 구별하는 데 사용됩니다. SNAT은 아웃바운드 흐름이 만들어질 때 임시 포트를 동적으로 할당합니다. 이 컨텍스트에서 SNAT에 사용되는 임시 포트를 SNAT 포트라고 합니다.

SNAT 포트는 소진될 수 있는 한정된 리소스입니다. 사용 방법을 이해하는 것이 중요합니다. 단일 대상 IP 주소에 대한 흐름 당 하나의 SNAT 포트를 사용합니다. 동일한 대상 IP 주소에 대한 여러 흐름의 경우 각 흐름은 단일 SNAT 포트를 사용합니다. 이렇게 하면 흐름은 동일한 대상 IP 주소에 대한 동일한 공용 IP 주소에서 시작하는 경우에 고유합니다. 다른 대상 IP 주소에 대한 여러 흐름은 각각 대상 당 단일 SNAT 포트를 사용합니다. 대상 IP 주소를 사용하면 흐름은 고유합니다.

[부하 분산 장치에 대한 Log Analytics](load-balancer-monitor-log.md) 및 [SNAT 포트 소모 메시지를 모니터링할 경고 이벤트 로그](load-balancer-monitor-log.md#alert-event-log)를 사용할 수 있습니다. SNAT 포트 리소스를 모두 사용한 경우 SNAT 포트가 기존 흐름에 의해 릴리스될 때까지 아웃바운드 흐름은 실패합니다. 부하 분산 장치는 SNAT 포트를 회수하는 데 4분의 유휴 시간 제한을 사용합니다.

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>인스턴스 수준 공용 IP 주소를 가진 VM(부하 분산 장치 있음 또는 없음)

이 시나리오에서 VM에는 ILPIP(인스턴스 수준 공용 IP)가 할당되어 있습니다. VM이 부하 분산되었는지 여부는 중요하지 않습니다. ILPIP를 사용하는 경우 SNAT(원본 네트워크 주소 변환)을 사용하지 않습니다. VM은 모든 아웃바운드 흐름에 ILPIP를 사용합니다. 응용 프로그램이 아웃바운드 흐름을 시작하고 SNAT 소모가 발생하는 경우 SNAT 제약 조건을 회피하려면 ILPIP를 할당하도록 고려해야 합니다.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>지정된 VM에서 사용하는 공용 IP 검색

여러 가지 방법으로 아웃바운드 연결의 공용 원본 IP 주소를 확인할 수 있습니다. OpenDNS는 VM의 공용 IP 주소를 표시할 수 있는 서비스를 제공합니다. nslookup 명령을 사용하여 OpenDNS 확인자에 myip.opendns.com이라는 이름에 대한 DNS 쿼리를 전송할 수 있습니다. 서비스는 쿼리를 보내는 데 사용된 원본 IP 주소를 반환합니다. VM에서 다음 쿼리를 실행하는 경우 응답은 해당 VM에 사용되는 공용 IP입니다.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>공용 연결 방지

VM이 아웃바운드 흐름을 만들 수 있는 것이 바람직하지 않은 경우도 있습니다. 또는 어떤 대상이 아웃바운드 흐름에 접근할지를 관리하는 요구 사항이 있을 수 있습니다. 이러한 경우에 [NSG(네트워크 보안 그룹)](../virtual-network/virtual-networks-nsg.md)을 사용하여 VM에서 도달할 수 있는 대상을 관리합니다. 부하 분산 VM에 NSG를 적용할 때 [태그 기본](../virtual-network/virtual-networks-nsg.md#default-tags) 및 [규칙 기본](../virtual-network/virtual-networks-nsg.md#default-rules)에 주의해야 합니다.

VM에서 Azure Load Balancer의 상태 프로브 요청을 수신할 수 있는지 확인해야 합니다. NSG가 AZURE_LOADBALANCER 기본 태그의 상태 프로브 요청을 차단할 경우 VM 상태 프로브에 실패하고 VM가 표시됩니다. 부하 분산 장치는 해당 VM에 새 흐름을 보내는 작업을 중지합니다.

## <a name="limitations"></a>제한 사항

보장되지는 않지만 현재 사용할 수 있는 SNAT 포트의 최대 수는 64,511개입니다(65,535 - 1024개 권한 있는 포트).  이 제한은 연결 수로 직접 변환되지 않습니다. SNAT 포트가 할당된 시기와 방법 및 이러한 소모성 리소스를 관리하는 방법에 대한 자세한 내용은 위에서 설명한 내용을 참조하세요.

