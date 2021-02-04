---
title: 'Azure Express 경로: Azure Portal를 사용 하 여 Global Reach 구성'
description: 이 문서는 Express 경로 회로를 함께 연결 하 여 온-프레미스 네트워크 간에 개인 네트워크를 만들고 Azure Portal를 사용 하 Global Reach을 사용 하도록 설정 하는 데 도움이 됩니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539352"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Express 경로 Global Reach 구성

이 문서는 PowerShell을 사용하여 ExpressRoute Global Reach를 구성하는 데 도움이 됩니다. 자세한 내용은 [ExpressRoute Global Reach](expressroute-global-reach.md)를 참조하세요.

 ## <a name="before-you-begin"></a>시작하기 전에

구성을 시작 하기 전에 다음 조건을 확인 합니다.

* Express 경로 회로 프로 비전 [워크플로](expressroute-workflows.md)를 이해 합니다.
* Express 경로 회로는 프로 비전 된 상태입니다.
* Azure 개인 피어 링은 Express 경로 회로에서 구성 됩니다.
* PowerShell을 로컬로 실행 하려면 최신 버전의 Azure PowerShell이 컴퓨터에 설치 되어 있는지 확인 합니다.

## <a name="identify-circuits"></a>회로 식별

1. 브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

2. 사용할 Express 경로 회로를 식별 합니다. 지원 되는 국가/지역에 있는 경우 두 Express 경로 회로의 개인 피어 링 간에 Express 경로 Global Reach를 사용 하도록 설정할 수 있습니다. 회로는 서로 다른 피어 링 위치에 만들어야 합니다. 

   * 사용자 구독이 두 회로 모두를 소유하는 경우 다음 섹션에서 구성을 실행할 회로를 하나 선택할 수 있습니다.
   * 두 회로가 다른 Azure 구독에 있는 경우 하나의 Azure 구독에서 권한을 부여해야 합니다. 그런 후 다른 Azure 구독에서 구성 명령을 실행할 때 권한 부여 키를 제공합니다.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Express 경로 회로 목록":::

## <a name="enable-connectivity"></a>연결 설정

온-프레미스 네트워크 간의 연결을 사용 하도록 설정 합니다. 동일한 Azure 구독에 있는 회로와 다른 구독의 회로에 대 한 별도의 지침 집합이 있습니다.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>동일한 Azure 구독의 Express 경로 회로

1. **Azure 개인** 피어 링 구성을 선택 합니다. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Express 경로 피어 링 개요":::

1. **Global Reach 사용** 확인란을 선택 하 고 **Global Reach 추가** 를 선택 하 여 *Global Reach 구성 추가* 페이지를 엽니다.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="개인 피어 링에서 전역 접근 사용":::

1. Global Reach 구성 *추가* 페이지에서이 구성의 이름을 지정 합니다. 이 회로에 연결 하려는 *express 경로 회로* 를 선택 하 고 *Global Reach 서브넷* 에 대해 **/29 IPv4** 를 입력 합니다. 두 ExpressRoute 회로 간의 연결을 설정하는 데 이 서브넷의 IP 주소가 사용됩니다. Azure 가상 네트워크 또는 온-프레미스 네트워크에서이 서브넷의 주소를 사용 하지 마세요. **추가** 를 선택 하 여 회로를 개인 피어 링 구성에 추가 합니다.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Global Reach 구성 페이지":::

1. **저장** 을 선택 하 Global Reach 구성을 완료 합니다. 작업이 완료 되 면 두 Express 경로 회로를 통해 두 개의 온-프레미스 네트워크 간에 연결을 갖게 됩니다.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="개인 피어 링 구성 저장 중":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>서로 다른 Azure 구독의 ExpressRoute 회로

두 회로가 동일한 Azure 구독에 없는 경우 권한 부여가 필요 합니다. 다음 구성에서 권한 부여는 회로 2의 구독에서 생성 됩니다. 그런 다음 인증 키가 회로 1로 전달 됩니다.

1. 권한 부여 키를 생성합니다.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="권한 부여 키 생성"::: 

   회로 2의 회로 리소스 ID와 권한 부여 키를 적어 둡니다.

1. **Azure 개인** 피어 링 구성을 선택 합니다. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="회로 1 피어 링 개요":::

1. **Global Reach 사용** 확인란을 선택 하 고 **Global Reach 추가** 를 선택 하 여 *Global Reach 구성 추가* 페이지를 엽니다.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="회로 1에서 전역 접근 사용":::

1. Global Reach 구성 *추가* 페이지에서이 구성의 이름을 지정 합니다. **권한 부여** 확인란을 선택 합니다. 1 단계에서 생성 및 얻은 **인증 키** 와 **EXPRESS 경로 회로 ID** 를 입력 합니다. 그런 다음 *Global Reach 서브넷* 에 **/29 IPv4** 를 제공 합니다. 두 ExpressRoute 회로 간의 연결을 설정하는 데 이 서브넷의 IP 주소가 사용됩니다. Azure 가상 네트워크 또는 온-프레미스 네트워크에서이 서브넷의 주소를 사용 하지 마세요. **추가** 를 선택 하 여 회로를 개인 피어 링 구성에 추가 합니다.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="권한 부여 키를 사용 하 여 Global Reach 추가":::

1. **저장** 을 선택 하 Global Reach 구성을 완료 합니다. 작업이 완료 되 면 두 Express 경로 회로를 통해 두 개의 온-프레미스 네트워크 간에 연결을 갖게 됩니다.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="회로 1에 개인 피어 링 구성을 저장 하는 중":::

## <a name="verify-the-configuration"></a>구성 확인

Express 경로 회로 구성에서 *개인 피어 링* 을 선택 하 여 Global Reach 구성을 확인 합니다. 올바르게 구성 된 경우 구성이 다음과 같이 표시 됩니다.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Global Reach 구성 확인":::

## <a name="disable-connectivity"></a>연결 사용 안 함

Global Reach를 사용 하지 않도록 설정 하는 경우 두 가지 옵션이 있습니다. 모든 회로 간에 연결을 사용 하지 않도록 설정 하려면 **Global Reach 사용** 안 함을 선택 취소 하 여 모든 회로 간의 연결을 해제 합니다. 개별 회로 간에 연결을 사용 하지 않도록 설정 하려면 *Global Reach 이름* 옆에 있는 삭제 단추를 선택 하 여 연결을 제거 합니다. 그런 다음, **저장** 을 선택 하 여 작업을 완료 합니다.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Global Reach 구성 사용 안 함":::

작업이 완료 되 면 Express 경로 회로를 통해 온-프레미스 네트워크 간에 더 이상 연결 되지 않습니다.

## <a name="next-steps"></a>다음 단계
1. [ExpressRoute Global Reach에 대해 자세히 알아봅니다.](expressroute-global-reach.md)
2. [Express 경로 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
3. [Azure Virtual Network에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)
