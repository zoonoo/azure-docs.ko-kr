---
title: 'P2S를 통한 원격 작업: Azure VPN 게이트웨이'
description: 이 페이지에서는 COVID-19 전염병으로 인해 Azure Bastion을 활용하여 원격으로 작업을 활성화하는 방법에 대해 설명합니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337114"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Azure VPN 게이트웨이 포인트 투 사이트를 사용하는 원격 작업

>[!NOTE]
>이 문서에서는 Azure VPN 게이트웨이, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 COVID-19 위기로 인해 직면하고 있는 네트워크 문제를 완화하는 방법에 대해 설명합니다.
>

이 문서에서는 조직에서 사용자에 대한 원격 액세스를 설정하거나 COVID-19 전염병 동안 추가 용량으로 기존 솔루션을 보완하는 데 사용할 수 있는 옵션에 대해 설명합니다.

Azure 지점 간 솔루션은 클라우드 기반이며 집에서 작업하는 사용자의 증가하는 수요를 수용하기 위해 신속하게 프로비저닝할 수 있습니다. 더 이상 용량을 늘릴 필요가 없을 때 쉽게 확장하고 쉽게 꺼낼 수 있습니다.

## <a name="about-point-to-site-vpn"></a>지점 및 사이트 간 VPN 연결 정보

P2S(지점 및 사이트 간) VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 솔루션은 집이나 회의와 같은 원격 위치에서 Azure VNet 또는 온-프레미스 데이터 센터에 연결하려는 재택 근무자에게 유용합니다. 이 문서에서는 사용자가 다양한 시나리오를 기반으로 원격으로 작업할 수 있도록 하는 방법에 대해 설명합니다.

아래 표는 클라이언트 운영 체제와 사용할 수 있는 인증 옵션을 보여 주며 있습니다. 이미 사용 중이던 클라이언트 OS를 기반으로 인증 방법을 선택하는 것이 좋습니다. 예를 들어 연결해야 하는 클라이언트 운영 체제가 혼합되어 있는 경우 인증서 기반 인증을 사용하여 OpenVPN을 선택합니다. 또한 지점 간 VPN은 경로 기반 VPN 게이트웨이에서만 지원됩니다.

![지점 간](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>시나리오 1 - 사용자가 Azure에서만 리소스에 액세스해야 합니다.

이 시나리오에서는 원격 사용자가 Azure에 있는 리소스에만 액세스하면 됩니다.

![지점 간](./media/working-remotely-support/scenario1.png "스카나리오 1")

높은 수준에서 사용자가 Azure 리소스에 안전하게 연결할 수 있도록 하려면 다음 단계가 필요합니다.

1. 가상 네트워크 게이트웨이 만들기(존재하지 않는 경우)
2. 게이트웨이에서 지점 간 VPN 구성
    3. [인증서 인증의 경우 이 링크를 따르십시오.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw)
    2.  [OpenVPN의 경우 이 링크를 따르십시오.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn)
    3.  [Azure AD 인증의 경우 이 링크를 따르십시오.](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant)
    4.  [지점 간 연결 문제를 해결하려면 이 링크를 따르십시오.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)
3. VPN 클라이언트 구성 다운로드 및 배포
4. 인증서(인증서 인증을 선택한 경우)를 클라이언트에 배포합니다.
5. Azure VPN에 연결

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>시나리오 2 - 사용자가 Azure 및/또는 온프레미 리소스의 리소스에 액세스해야 합니다.

이 시나리오에서 원격 사용자는 Azure 및 온-프레미스 데이터 센터에 있는 리소스에 액세스해야 합니다.

![지점 간](./media/working-remotely-support/scenario2.png "시나리오 2")

높은 수준에서 사용자가 Azure 리소스에 안전하게 연결할 수 있도록 하려면 다음 단계가 필요합니다.

1. 가상 네트워크 게이트웨이 만들기(존재하지 않는 경우)
2. 게이트웨이에서 지점 간 VPN 구성(위의 시나리오 1 참조)
3. BGP를 사용하도록 설정한 Azure 가상 네트워크 게이트웨이에서 사이트 간 터널 구성
4. Azure 가상 네트워크 게이트웨이에 연결하도록 온-프레미스 디바이스 구성
5. Azure 포털에서 지점 간 프로필을 다운로드하여 클라이언트에 배포

[사이트 간 VPN 터널을 설정하는 방법을 알아보려면 이 링크를 따르십시오.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>네이티브 Azure 인증서 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>다음 단계

* [P2S 연결 구성 - Azure AD 인증](openvpn-azure-ad-tenant.md)

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

**"오픈VPN"은 오픈VPN 의 상표입니다.**