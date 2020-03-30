---
title: IP 주소 168.63.129.16 | Microsoft Docs
description: IP 주소 168.63.129.16을 소개하고 이 주소에서 리소스를 사용하는 방법에 대해 설명합니다.
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
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114368"
---
# <a name="what-is-ip-address-1686312916"></a>IP 주소 168.63.129.16

IP 주소 168.63.129.16은 Azure 플랫폼 리소스에 대한 통신 채널을 원활하게 설정하는 데 사용되는 가상 공용 IP 주소입니다. 고객은 Azure에서 프라이빗 가상 네트워크용 주소 공간을 정의할 수 있습니다. 따라서 Azure 플랫폼 리소스가 고유 공용 IP 주소로 포함되어 있어야 합니다. 이 가상 공용 IP 주소가 있으면 다음 작업을 원활하게 수행할 수 있습니다.

- VM 에이전트가 Azure 플랫폼과 통신하여 "준비" 상태임을 알릴 수 있습니다.
- 사용자 지정 DNS 서버가 없는 VM 등의 리소스에 필터링된 이름 확인 기능을 제공하기 위해 DNS 가상 서버와 통신할 수 있습니다. 이 필터링을 통해 고객은 리소스의 호스트 이름만 확인할 수 있습니다.
- [Azure 로드 밸러버의 상태 프로브를](../load-balancer/load-balancer-custom-probe-overview.md) 사용하여 VM의 상태를 확인할 수 있습니다.
- VM이 Azure의 DHCP 서비스에서 동적 IP 주소를 가져올 수 있도록 합니다.
- PaaS 역할에 대해 게스트 에이전트 하트비트 메시지를 사용할 수 있습니다.

## <a name="scope-of-ip-address-1686312916"></a>IP 주소 168.63.129.16의 범위

공용 IP 주소 168.63.129.16은 모든 지역과 모든 국가 클라우드에서 사용됩니다. 이 특별한 공용 IP 주소는 Microsoft가 소유하며 변경되지 않습니다. 모든 로컬(VM) 방화벽 정책(아웃바운드 방향)에서 이 IP 주소를 허용하는 것이 좋습니다. 이 IP 주소에서는 내부 Azure 플랫폼만이 메시지 원본을 제공할 수 있으므로, 이 특수 IP 주소와 리소스 간의 통신은 안전합니다. 이 주소가 차단되면 여러 시나리오에서 예기치 않은 동작이 발생할 수 있습니다. 168.63.129.16은 [호스트 노드의 가상 IP이며](../virtual-network/security-overview.md#azure-platform-considerations) 사용자 정의 경로의 적용을 받지 않습니다.

- VM 에이전트는 WireServer(168.63.129.16)를 통해 포트 80, 443, 32526을 통해 아웃바운드 통신을 요구합니다. VM의 로컬 방화벽에서 열려 있어야 합니다. 168.63.129.16이 있는 이러한 포트의 통신은 구성된 네트워크 보안 그룹의 적용을 받지 않습니다.
- 168.63.129.16 VM에 DNS 서비스를 제공할 수 있습니다. 원하지 않는 경우 이 트래픽은 VM의 로컬 방화벽에서 차단될 수 있습니다. 기본적으로 DNS 통신은 [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) 서비스 태그를 활용하는 특별히 대상으로 하지 않는 한 구성된 네트워크 보안 그룹의 적용을 받지 않습니다.
- VM이 로드 밸록터 백엔드 풀의 일부인 경우 [상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md) 통신은 168.63.129.16에서 시작하도록 허용되어야 합니다. 기본 네트워크 보안 그룹 구성에는 이 통신을 허용하는 규칙이 있습니다. 이 규칙은 [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) 서비스 태그를 활용합니다. 원하는 경우 네트워크 보안 그룹을 구성하여 이 트래픽을 차단할 수 있지만 이로 인해 프로브가 실패하게 됩니다.

비가상 네트워크 시나리오(Classic)에서는 상태 프로브가 개인 IP에서 공급되고 168.63.129.16이 사용되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [보안 그룹](security-overview.md)
- [네트워크 보안 그룹 만들기, 변경 또는 삭제](manage-network-security-group.md)
