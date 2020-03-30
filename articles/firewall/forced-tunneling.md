---
title: Azure 방화벽 강제 터널링
description: 추가 처리를 위해 인터넷 바인딩 트래픽을 추가 방화벽 또는 네트워크 가상 어플라이언스로 라우팅하도록 강제 터널링을 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597282"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure 방화벽 강제 터널링(미리 보기)

Azure 방화벽을 구성하여 모든 인터넷 바인딩 트래픽을 인터넷으로 직접 이동하지 않고 지정된 다음 홉으로 라우팅할 수 있습니다. 예를 들어 온-프레미스 에지 방화벽 또는 기타 NVA(네트워크 가상 어플라이언스)가 인터넷으로 전달되기 전에 네트워크 트래픽을 처리할 수 있습니다.

> [!IMPORTANT]
> Azure 방화벽 강제 터널링은 현재 공개 미리 보기 상태입니다.
>
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능이 지원되지 않거나, 기능이 제한되거나, 일부 Azure 위치에 제공되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

기본적으로 강제 터널링은 Azure 방화벽에서 모든 아웃바운드 Azure 종속성을 충족하도록 허용되지 않습니다. 인터넷에 직접 이동 하지 않는 기본 경로 *AzureFirewallSubnet에* 사용자 정의 된 경로 (UDR) 구성을 사용할 수 없습니다.

## <a name="forced-tunneling-configuration"></a>강제 터널링 구성

강제 터널링을 지원하기 위해 서비스 관리 트래픽은 고객 트래픽과 분리됩니다. *AzureFirewallManagementSubnet* (최소 서브넷 크기 /26)라는 추가 전용 서브넷자체 관련 공용 IP 주소가 필요합니다. 이 서브넷에서 허용되는 유일한 경로는 인터넷에 대한 기본 경로이며 BGP 경로 전파를 사용하지 않도록 설정해야 합니다.

온-프레미스로 트래픽을 강제로 BGP를 통해 보급된 기본 경로가 있는 경우 방화벽을 배포하기 전에 *AzureFirewallSubnet* 및 *AzureFirewallManagementSubnet을* 만들고 인터넷에 대한 기본 경로가 있는 UDR이 있어야 하며 **가상 네트워크 게이트웨이 경로 전파가** 비활성화되어 있습니다.

이 구성 내에서 *AzureFirewallSubnet은* 이제 인터넷에 전달되기 전에 트래픽을 처리하기 위해 온-프레미스 방화벽 또는 NVA에 대한 경로를 포함할 수 있습니다. 이 서브넷에서 **가상 네트워크 게이트웨이 경로 전파가** 활성화된 경우 BGP를 통해 이러한 경로를 *AzureFirewallSubnet에* 게시할 수도 있습니다.

예를 들어 VPN 게이트웨이를 사용하여 *AzureFirewallSubnet에서* 기본 경로를 다음 홉으로 만들어 온-프레미스 장치에 가져올 수 있습니다. 또는 가상 **네트워크 게이트웨이 경로 전파를** 활성화하여 온-프레미스 네트워크에 대한 적절한 경로를 얻을 수 있습니다.

![가상 네트워크 게이트웨이 경로 전파](media/forced-tunneling/route-propagation.png)

강제 터널링을 지원하도록 Azure 방화벽을 구성한 후에는 구성을 취소할 수 없습니다. 방화벽에서 다른 모든 IP 구성을 제거하면 관리 IP 구성도 제거되고 방화벽이 할당 해제됩니다. 관리 IP 구성에 할당된 공용 IP 주소는 제거할 수 없지만 다른 공용 IP 주소를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure 포털을 사용하여 하이브리드 네트워크에서 Azure 방화벽 배포 및 구성](tutorial-hybrid-portal.md)