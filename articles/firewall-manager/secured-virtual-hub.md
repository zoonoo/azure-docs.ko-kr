---
title: 보안 가상 허브란?
description: 보안 가상 허브에 대해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91948071"
---
# <a name="what-is-a-secured-virtual-hub"></a>보안 가상 허브란?

가상 허브는 다른 리소스에서 연결할 수 있도록 하는 Microsoft에서 관리하는 가상 네트워크입니다. Azure Portal의 Azure Virtual WAN에서 가상 허브를 만드는 경우, 가상 허브 VNet 및 게이트웨이(선택 사항)가 구성 요소로 만들어집니다.

*보안* 가상 허브는 Azure Firewall Manager에서 구성하는 연결된 보안 및 라우팅 정책을 포함하는 [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources)입니다. 보안 가상 허브를 사용하여 트래픽 거버넌스 및 보호를 위한 기본 보안 서비스를 통해 허브 및 스포크 및 전이적 아키텍처를 쉽게 만들 수 있습니다. 

보안 가상 허브를 사용하여 V2V(가상 네트워크), B2V (가상 네트워크 및 지점) 및 인터넷에 대한 트래픽(B2I/V2I) 간의 트래픽을 필터링할 수 있습니다. 보안 가상 허브는 자동화된 라우팅을 제공합니다. 방화벽을 통해 트래픽을 라우팅하도록 자체 UDR(사용자 정의 경로)을 구성할 필요가 없습니다.

Azure Firewall, 서비스(SECaaS) 공급자로서 제3자 보안 또는 둘 다를 포함하여 네트워크 트래픽을 보호하고 제어하는 데 필요한 보안 공급자를 선택할 수 있습니다. 현재, 보안 허브는 B2B (Branch to Branch) 필터링 및 여러 허브에 걸친 필터링을 지원하지 않습니다. 자세히 알아보려면 [Azure Firewall Manager란?](overview.md#known-issues)을 참조하세요. 

## <a name="create-a-secured-virtual-hub"></a>보안 가상 허브 만들기

Azure Portal에서 Firewall Manager를 사용하여 새 보안 가상 허브를 만들거나 이전에 Azure Virtual WAN을 사용하여 만든 기존 가상 허브를 변환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

보안 가상 허브를 만들어 허브 및 스포크 네트워크를 보호하고 제어하는 데 사용하려면, [자습서: Azure Portal을 사용하여 Azure Firewall Manager로 클라우드 네트워크 보호](secure-cloud-network.md)를 참조하세요.