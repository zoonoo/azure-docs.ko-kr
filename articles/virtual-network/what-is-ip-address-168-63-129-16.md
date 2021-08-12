---
title: IP 주소 168.63.129.16 | Microsoft 문서
description: 특히 Azure 플랫폼 리소스의 통신 채널을 용이하게 하는데 사용 되는 IP 주소 168.63.129.16에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 1e304bc30a48c92fdff576723dae6af1e26ef3da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222584"
---
# <a name="what-is-ip-address-1686312916"></a>IP 주소 168.63.129.16

IP 주소 168.63.129.16은 Azure 플랫폼 리소스에 대한 통신 채널을 원활하게 설정하는 데 사용되는 가상 공용 IP 주소입니다. 고객은 Azure에서 프라이빗 가상 네트워크용 주소 공간을 정의할 수 있습니다. 따라서 Azure 플랫폼 리소스가 고유 공용 IP 주소로 포함되어 있어야 합니다. 이 가상 공용 IP 주소가 있으면 다음 작업을 원활하게 수행할 수 있습니다.

- VM 에이전트가 Azure 플랫폼과 통신하여 "준비" 상태임을 알릴 수 있습니다.
- 사용자 지정 DNS 서버가 없는 VM 등의 리소스에 필터링된 이름 확인 기능을 제공하기 위해 DNS 가상 서버와 통신할 수 있습니다. 이 필터링을 통해 고객은 리소스의 호스트 이름만 확인할 수 있습니다.
- VM의 성능 상태를 확인하기 위해 [Azure load balancer에서 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md) 사용할 수 있습니다.
- VM이 Azure의 DHCP 서비스에서 동적 IP 주소를 가져올 수 있도록 합니다.
- PaaS 역할에 대해 게스트 에이전트 하트비트 메시지를 사용할 수 있습니다.

> [!NOTE]
> 비가상 네트워크 시나리오(클래식)에서는 168.63.129.16 대신 개인 IP 주소가 사용됩니다. 이 개인 IP 주소는 DHCP를 통해 동적으로 검색됩니다. 168.63.129.16과 관련된 방화벽 규칙은 적절하게 조정해야 합니다.

## <a name="scope-of-ip-address-1686312916"></a>IP 주소 168.63.129.16의 범위

공용 IP 주소 168.63.129.16은 모든 지역과 모든 국가별 클라우드에서 사용됩니다. 이 특수 공용 IP 주소는 Microsoft에서 소유하며 변경되지 않습니다. 모든 VM 내 로컬 방화벽 정책(아웃바운드 방향)에서 이 IP 주소를 허용하기를 권합니다. 이 IP 주소에서는 내부 Azure 플랫폼만이 메시지 원본을 제공할 수 있으므로, 이 특수 IP 주소와 리소스 간의 통신은 안전합니다. 이 주소가 차단되면 여러 시나리오에서 예기치 않은 동작이 발생할 수 있습니다. 168.63.129.16는 [호스트 노드의 가상 IP](./network-security-groups-overview.md#azure-platform-considerations) 로, 사용자 정의 경로가 적용되지 않습니다.

- VM 에이전트에는 포트 80/tcp 및 32526/tcp with WireServer(168.63.129.16)를 통한 아웃바운드 통신이 필요합니다. VM의 로컬 방화벽에서 열려 있어야 합니다. 168.63.129.16을 사용한 이러한 포트에서의 통신은 구성된 네트워크 보안 그룹이 적용되지 않습니다.

- 168.63.129.16은 VM에 DNS 서비스를 제공할 수 있습니다. 이를 원하지 않는 경우 168.63.129.16 포트 53/udp 및 53/tcp에 대한 아웃바운드 트래픽이 VM의 로컬 방화벽에서 차단될 수 있습니다.

  기본적으로 [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) 서비스 태그를 활용하는 경우를 제외하고는 DNS 통신이 구성된 네트워크 보안 그룹의 영향을 받지 않습니다. NSG를 통해 Azure DNS로 들어오는 DNS 트래픽을 차단하려면 [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)로 오는 트래픽을 거부 하는 아웃바운드 규칙을 만들고 "*"를 "대상 포트 범위"로, "Any"를 프로토콜로 지정합니다.

- VM이 부하 분산 장치 백 엔드 풀의 일부인 경우 [상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) 통신이 168.63.129.16에서 시작될 수 있도록 허용되어야 합니다. 기본 네트워크 보안 그룹 구성에는 이 통신을 허용하는 규칙이 있습니다. 이 규칙은 [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) 서비스 태그를 활용합니다. 필요한 경우 네트워크 보안 그룹을 구성하여 이 트래픽을 차단할 수 있습니다. 그러나 이 경우에는 프로브가 실패합니다.

## <a name="next-steps"></a>다음 단계

- [보안 그룹](./network-security-groups-overview.md)
- [네트워크 보안 그룹 만들기, 변경 또는 삭제](manage-network-security-group.md)