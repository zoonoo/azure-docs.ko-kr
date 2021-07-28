---
title: Azure Firewall 강제 터널링
description: 추가 처리를 위해 인터넷 바인딩된 트래픽을 추가 방화벽 또는 네트워크 가상 어플라이언스로 라우팅하도록 강제 터널링을 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88612601"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall 강제 터널링

새 Azure Firewall을 구성하는 경우 인터넷으로 직접 이동하는 대신 인터넷에 바인딩된 모든 트래픽을 지정된 다음 홉으로 라우팅할 수 있습니다. 예를 들어 온-프레미스 에지 방화벽이나 기타 NVA(네트워크 가상 어플라이언스)를 통해 네트워크 트래픽을 인터넷에 전달하기 전에 처리할 수 있습니다. 그러나 강제 터널링을 위해 기존 방화벽을 구성할 수는 없습니다.

기본적으로 강제 터널링은 Azure Firewall에서 모든 아웃바운드 Azure 종속성이 충족되도록 허용되지 않습니다. 인터넷으로 직접 이동하지 않는 기본 경로가 있는 *AzureFirewallSubnet* 에 대한 UDR(사용자 정의 경로) 구성이 비활성화되어 있습니다.

## <a name="forced-tunneling-configuration"></a>강제 터널링 구성

강제 터널링을 지원하기 위해 서비스 관리 트래픽은 고객 트래픽과 구분됩니다. 연결된 공용 IP 주소에는 *AzureFirewallManagementSubnet*(최소 서브넷 크기/26)이라는 추가 전용 서브넷이 필요합니다. 이 서브넷에서 허용되는 유일한 경로는 인터넷에 대한 기본 경로이며, BGP 경로 전파를 사용하지 않도록 설정해야 합니다.

BGP를 통해 기본 경로를 보급하여 트래픽을 온-프레미스로 강제로 보내는 경우 방화벽을 배포하기 전에 *AzureFirewallSubnet* 및 *AzureFirewallManagementSubnet* 을 만들어야 하며, 기본 경로가 인터넷으로 설정되고 **게이트웨이 경로 전파** 가 사용하지 않도록 설정된 UDR이 있어야 합니다.

이 구성 내에서 *AzureFirewallSubnet* 은 이제 온-프레미스 방화벽 또는 NVA 경로를 포함하여 트래픽이 인터넷에 전달되기 전에 트래픽을 처리할 수 있습니다. 이 서브넷에서 **게이트웨이 경로 전파** 가 사용하도록 설정된 경우에는 BGP를 통해 해당 경로를 *AzureFirewallSubnet* 에 게시할 수도 있습니다.

예를 들어 온-프레미스 디바이스에 도달하기 위해 VPN Gateway를 다음 홉으로 설정하여 *AzureFirewallSubnet* 에서 기본 경로를 만들 수 있습니다. 또는 **게이트웨이 경로 전파** 를 사용하도록 설정하여 적절한 온-프레미스 네트워크 경로를 가져올 수 있습니다.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="가상 네트워크 게이트웨이 경로 전파":::

강제 터널링을 사용하도록 설정하면 인터넷 바인딩된 트래픽이 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나로 SNAT되어 온-프레미스 방화벽에서 원본을 숨깁니다.

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 하지만 공용 IP 주소 범위를 SNAT하지 **않도록** Azure Firewall을 구성할 수 있습니다. 자세한 내용은 [Azure Firewall SNAT 개인 IP 주소 범위](snat-private-range.md)를 참조하세요.

강제 터널링을 지원하도록 Azure Firewall을 구성하면 구성을 실행 취소할 수 없습니다. 방화벽에서 다른 모든 IP 구성을 제거하면 관리 IP 구성도 제거되고 방화벽의 할당이 취소됩니다. 관리 IP 구성에 할당된 공용 IP 주소를 제거할 수 없지만 다른 공용 IP 주소를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 하이브리드 네트워크에서 Azure Firewall 배포 및 구성](tutorial-hybrid-portal.md)
