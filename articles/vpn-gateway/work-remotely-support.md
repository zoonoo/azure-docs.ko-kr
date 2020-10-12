---
title: 'P2S를 사용 하 여 원격 작업: Azure VPN Gateway'
description: 이 페이지에서는 VPN Gateway를 활용 하 여 COVID-19 전염병로 인해 원격 작업을 사용 하도록 설정 하는 방법을 설명 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 028250e25f4d67628c08970e25b4621e78526b6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440833"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Azure VPN Gateway 지점 및 사이트 간 원격 작업

>[!NOTE]
>이 문서에서는 Azure VPN Gateway, Azure, Microsoft 네트워크 및 Azure 파트너 에코 시스템을 활용 하 여 원격으로 작업 하 고 COVID-19 위기 때문에 직면 하 고 있는 네트워크 문제를 완화 하는 방법을 설명 합니다.
>

이 문서에서는 조직에서 사용자에 대 한 원격 액세스를 설정 하거나 COVID-19 epidemic 동안 추가 용량으로 기존 솔루션을 보완 하는 데 사용할 수 있는 옵션을 설명 합니다.

Azure 지점 및 사이트 간 솔루션은 클라우드 기반 솔루션으로, 집에서 작업 하는 사용자의 수요 증가를 제공 하기 위해 신속 하 게 프로 비전 할 수 있습니다. 더 이상 용량이 더 이상 필요 하지 않은 경우 쉽게 확장 하 고 쉽고 빠르게 해제할 수 있습니다.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>지점 및 사이트 간 VPN 연결 정보

P2S(지점 및 사이트 간) VPN 게이트웨이 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 솔루션은 집 또는 회의와 같은 원격 위치에서 Azure Vnet 또는 온-프레미스 데이터 센터에 연결 하려는 재택 근무자에 게 유용 합니다. 이 문서에서는 사용자가 다양 한 시나리오에 따라 원격으로 작업할 수 있도록 하는 방법을 설명 합니다.

다음 표에서는 클라이언트 운영 체제와 해당 운영 체제에서 사용할 수 있는 인증 옵션을 보여 줍니다. 이미 사용 중인 클라이언트 OS에 따라 인증 방법을 선택 하는 것이 좋습니다. 예를 들어, 연결 해야 하는 클라이언트 운영 체제가 혼합 되어 있는 경우 인증서 기반 인증을 사용 하 여 OpenVPN을 선택 합니다. 또한 지점 및 사이트 간 VPN은 경로 기반 VPN 게이트웨이에서만 지원 됩니다.

![클라이언트 운영 체제 및 사용 가능한 인증 옵션을 보여 주는 스크린샷](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>시나리오 1-사용자가 Azure의 리소스에만 액세스 해야 하는 경우

이 시나리오에서 원격 사용자는 Azure에 있는 리소스에만 액세스 하면 됩니다.

![Azure의 리소스에만 액세스 해야 하는 사용자를 위한 지점 및 사이트 간 시나리오를 보여 주는 다이어그램입니다.](./media/working-remotely-support/scenario1.png "시나리오 1")

높은 수준에서 사용자가 Azure 리소스에 안전 하 게 연결할 수 있도록 하려면 다음 단계를 수행 해야 합니다.

1. 가상 네트워크 게이트웨이 (있는 경우)를 만듭니다.
2. 게이트웨이에서 지점 및 사이트 간 VPN을 구성 합니다.

   * 인증서 인증의 경우 [이 링크](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)를 따릅니다.
   * OpenVPN의 경우 [이 링크](vpn-gateway-howto-openvpn.md)를 따릅니다.
   * Azure AD 인증의 경우 [이 링크](openvpn-azure-ad-tenant.md)를 따릅니다.
   * 지점 및 사이트 간 연결 문제를 해결 하려면 [이 링크](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)를 따릅니다.
3. VPN 클라이언트 구성을 다운로드 하 고 배포 합니다.
4. 인증서 (인증서 인증을 선택한 경우)를 클라이언트에 배포 합니다.
5. Azure VPN에 연결 합니다.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>시나리오 2-사용자가 Azure 및/또는 온-프레미스 리소스의 리소스에 액세스 해야 하는 경우

이 시나리오에서 원격 사용자는 Azure 및 온-프레미스 데이터 센터에 있는 리소스에 액세스 해야 합니다.

![Azure의 리소스에 액세스 해야 하는 사용자를 위한 지점 및 사이트 간 시나리오를 보여 주는 다이어그램입니다.](./media/working-remotely-support/scenario2.png "시나리오 2")

높은 수준에서 사용자가 Azure 리소스에 안전 하 게 연결할 수 있도록 하려면 다음 단계를 수행 해야 합니다.

1. 가상 네트워크 게이트웨이 (있는 경우)를 만듭니다.
2. 게이트웨이에서 지점 및 사이트 간 VPN을 구성 합니다 ( [시나리오 1](#scenario1)참조).
3. BGP를 사용 하도록 설정 된 Azure 가상 네트워크 게이트웨이에서 사이트 간 터널을 구성 합니다.
4. 온-프레미스 장치를 구성 하 여 Azure 가상 네트워크 게이트웨이에 연결 합니다.
5. Azure Portal에서 지점 및 사이트 간 프로필을 다운로드 하 고 클라이언트에 배포

사이트 간 VPN 터널을 설정 하는 방법에 대 한 자세한 내용은 [이 링크](vpn-gateway-howto-site-to-site-resource-manager-portal.md)를 참조 하세요.

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>네이티브 Azure 인증서 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 인증에 대한 FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>다음 단계

* [P2S 연결 구성-Azure AD 인증](openvpn-azure-ad-tenant.md)

* [P2S 연결 구성 - RADIUS 인증](point-to-site-how-to-radius-ps.md)

* [P2S 연결 구성 - Azure 네이티브 인증서 인증](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN"은 OpenVPN i n c .의 상표입니다.**