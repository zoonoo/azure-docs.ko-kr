---
title: Azure Virtual WAN 개요 | Microsoft Docs
description: Virtual WAN 자동화된 확장성 있는 분기 간 연결에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/18/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 67dd6ba9b94ed9d58d91fb644ce9ee9e44ae9e45
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159172"
---
# <a name="what-is-azure-virtual-wan-preview"></a>Azure Virtual WAN이란? (미리 보기)

Azure Virtual WAN은 Azure를 통해 최적화 및 자동화된 분기 간 연결을 제공하는 네트워킹 서비스입니다. Virtual WAN을 사용하여 Azure와 통신하도록 분기 장치를 연결 및 구성할 수 있습니다. 수동으로 또는 Virtual WAN 파트너를 통해 선호하는 공급자 장치를 사용하여 수행할 수 있습니다. 선호하는 공급자 장치를 사용하여 사용 편의성, 연결 및 구성 관리의 간소화를 얻을 수 있습니다. Azure WAN 기본 제공 대시보드는 시간을 절약할 수 있도록 도울 수 있는 즉각적인 문제 해결 인사이트를 제공하고, 대규모 사이트 간 연결을 확인하는 간편한 방법을 제공합니다.

> [!IMPORTANT]
> Azure Virtual WAN은 현재 관리되는 공개 미리 보기입니다. Virtual WAN을 사용하려면 [미리 보기에 등록](#enroll)해야 합니다.
>
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure 및 비 Azure 워크로드의 네트워크 연결에 대한 빠른 보기를 제공합니다. Virtual WAN은 다음과 같은 이점을 제공합니다.

* **허브 및 스포크에서 통합된 연결 솔루션:** 사이트 간 연결 및 Virtual WAN 파트너 솔루션을 포함한 다양한 원본에서 온-프레미스 사이트와 Azure 허브 간 연결을 자동화합니다.
* **자동화된 스포크 설치 및 구성:** Azure 허브로 가상 네트워크 및 워크로드를 원활하게 연결합니다.
* **직관적인 문제 해결:** Azure 내에서 종단 간 흐름을 보고 이 정보를 사용하여 필요한 작업을 수행할 수 있습니다.

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Virtual WAN 파트너와 작업

1. 분기 장치(VPN/SDWAN) 컨트롤러는 Azure 서비스 사용자를 사용하여 Azure로 사이트 중심 정보를 내보내도록 인증됩니다.
2. 분기 장치(VPN/SDWAN) 컨트롤러는 Azure 연결 구성을 가져오고 로컬 장치를 업데이트합니다. 구성 다운로드, 온-프레미스 VPN 장치의 편집 및 업데이트를 자동화합니다.
3. 장치가 올바른 Azure 구성을 가지면 Azure WAN으로 사이트 간 연결(두 개의 활성 터널)이 설정됩니다. Azure는 IKEv2를 지원하는 분기(VPN/SDWAN) 컨트롤러가 필요합니다. BGP는 선택 사항입니다.

## <a name="resources"></a>Virtual WAN 리소스

종단 간 가상 WAN을 구성하려면 다음 리소스를 만듭니다.

* **virtualWAN:** virtualWAN 리소스는 Azure 네트워크의 가상 오버레이를 나타내며 여러 리소스의 컬렉션입니다. 가상 WAN 내에서 가지려는 모든 가상 허브에 대한 링크를 포함합니다. Virtual WAN 리소스는 서로 격리되며 공통의 허브를 포함할 수 없습니다. Virtual WAN에서 가상 허브는 서로 통신하지 않습니다.

* **사이트:** vpnsite로 알려진 사이트 리소스는 온-프레미스 VPN 장치 및 해당 설정을 나타냅니다. Virtual WAN 파트너와 작업하여 이 정보를 Azure로 자동으로 내보내는 기본 제공 솔루션을 갖습니다.

* **허브:** 가상 허브는 Microsoft에서 관리하는 가상 네트워크입니다. 허브는 온-프레미스 네트워크(vpnsite)에서 연결을 활성화하는 다양한 서비스 엔드포인트를 포함합니다. 허브는 지역에서 네트워크의 핵심입니다. Azure 지역당 하나의 허브만 있을 수 있습니다. Azure Portal을 사용하여 허브를 만들 때 가상 허브 VNet 및 가상 허브 vpngateway를 자동으로 만듭니다.

  허브 게이트웨이는 ExpressRoute 및 VPN Gateway에 사용하는 가상 네트워크 게이트웨이와 동일하지 않습니다. 예를 들어 Virtual WAN을 사용할 때 온-프레미스 사이트에서 VNet으로 직접 사이트 간 연결을 만들지 않습니다. 대신 허브로 사이트 간 연결을 만듭니다. 트래픽은 항상 허브 게이트웨이를 통해 이동합니다. VNet에 자체 가상 네트워크 게이트웨이가 필요하지 않다는 것을 의미합니다. Virtual WAN을 사용하면 VNet은 가상 허브와 가상 허브 게이트웨이를 통해 쉽게 크기 조정을 활용할 수 있습니다. 

* **허브 가상 네트워크 연결:** 허브 가상 네트워크 연결 리소스는 가상 네트워크에 허브를 원활하게 연결하는 데 사용됩니다. 이 때 동일한 허브 지역 내에 있는 가상 네트워크에 연결할 수 있습니다.

##<a name="enroll"></a>미리 보기에 등록

Virtual WAN을 구성하려면 그 전에 먼저 미리 보기에서 구독을 등록해야 합니다. 그렇지 않으면 포털에서 Virtual WAN을 사용할 수 없습니다. 등록하려면 구독 ID로 <azurevirtualwan@microsoft.com>에 이메일을 전송합니다. 구독이 등록되면 이메일 회신을 받게 됩니다.

## <a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>미리 보기 피드백

여러분의 의견에 감사드립니다. 문제를 보고하거나 Virtual WAN에 대한 피드백(긍정적이거나 부정적인)을 제공하려면 <azurevirtualwan@microsoft.com>에 메일을 보내세요. 제목 줄에서 “[]”에 회사 이름을 적어주세요. 문제를 보고하려는 경우 구독 ID도 보내주세요.

## <a name="next-steps"></a>다음 단계

Virtual WAN을 사용하여 사이트 간 연결을 만들려면 [Virtual WAN 파트너](https://aka.ms/virtualwan)를 통해 이동하거나 수동으로 연결을 만들 수 있습니다. 연결을 수동으로 만들려면 [Virtual WAN을 사용하여 사이트 간 연결 만들기](virtual-wan-site-to-site-portal.md)를 참조하세요.
