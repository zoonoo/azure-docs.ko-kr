---
title: '자습서: ExpressRoute 회로에 VNet 연결 - Azure Portal'
description: 이 자습서에서는 Azure Portal을 사용하여 Azure ExpressRoute 회로에 가상 네트워크를 연결하는 방법을 보여줍니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107286"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>자습서: 포털을 사용하여 ExpressRoute 회로에 가상 네트워크 연결

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-linkvnet-classic.md)
> 

이 자습서는 Azure Portal을 사용하여 Azure ExpressRoute 회로에 가상 네트워크를 연결하는 데 도움이 됩니다. Azure ExpressRoute 회로에 연결한 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다.

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> - 동일한 구독에 있는 회로에 가상 네트워크를 연결합니다.
> - 다른 구독에 있는 회로에 가상 네트워크를 연결합니다.
> - 가상 네트워크와 ExpressRoute 회로 간의 연결을 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

* 활성화된 ExpressRoute 회로가 있어야 합니다.
  * 지침을 수행하여 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-portal-resource-manager.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다.
  * 회로에 구성된 Azure 프라이빗 피어링이 있는지 확인합니다. 피어링 및 라우팅은 [ExpressRoute 회로에 대한 피어링 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md) 문서를 참조하세요.
  * Azure 개인 피어링이 구성되고 엔드투엔드 연결을 위해 네트워크와 Microsoft 간에 BGP 피어링이 설정되어 있는지 확인합니다.
  * 가상 네트워크 및 가상 네트워크 게이트웨이를 만들어서 완전히 프로비전해야 합니다. 지침에 따라 [ExpressRoute에 대한 가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-resource-manager.md). ExpressRoute의 가상 네트워크 게이트웨이는 GatewayType으로 VPN이 아닌 'ExpressRoute'를 사용합니다.

* 최대 10개의 가상 네트워크를 표준 ExpressRoute 회로에 연결할 수 있습니다. 표준 ExpressRoute 회로를 사용하는 경우 모든 가상 네트워크는 동일한 지역에 있어야 합니다.

* 단일 VNet을 최대 4개의 ExpressRoute 회로에 연결할 수 있습니다. 다음 프로세스를 사용하여 연결하려는 각 ExpressRoute 회로에 대한 새 연결 개체를 만듭니다. ExpressRoute 회로는 동일한 구독, 서로 다른 구독 또는 두 가지가 혼합된 상태로 존재할 수 있습니다.

* ExpressRoute 프리미엄 추가 기능을 사용하도록 설정하면 ExpressRoute 회로의 지정학적 지역 외부에서 가상 네트워크를 연결할 수 있습니다. 또한 프리미엄 추가 항목을 사용하면 선택한 대역폭에 따라 10개가 넘는 가상 네트워크를 ExpressRoute 회로에 연결할 수도 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md) 에서 확인하세요.

* 시작 전에 [비디오 보기](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)로 단계를 더 잘 이해할 수 있습니다.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>회로에 VNet 연결 - 동일한 구독

> [!NOTE]
> 3계층 공급자가 피어링을 구성한 경우 BGP 구성 정보가 표시되지 않습니다. 회로가 프로비전된 상태인 경우 연결을 만들 수 있어야 합니다.
>

### <a name="to-create-a-connection"></a>연결을 만들려면

1. ExpressRoute 회로 및 Azure 프라이빗 피어링이 성공적으로 구성되었는지 확인합니다. [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md) 및 [ExpressRoute 회로에 대한 피어링을 만들기 및 수정](expressroute-howto-routing-arm.md)의 지침을 따릅니다. ExpressRoute 회로가 다음 이미지와 같이 표시됩니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="ExpressRoute 회로 스크린샷":::

1. 이제 연결 프로비전을 시작하여 가상 네트워크 게이트웨이를 ExpressRoute 회로에 연결할 수 있습니다. **연결** > **추가**를 선택하여 **연결 추가** 페이지를 엽니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="ExpressRoute 회로 스크린샷":::

1. 연결 이름을 입력하고 **다음: 설정 >** 을 선택합니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="ExpressRoute 회로 스크린샷":::

1. 회로에 연결할 가상 네트워크에 속하는 게이트웨이를 선택하고 **검토 + 만들기**를 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택합니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="ExpressRoute 회로 스크린샷":::

1. 연결이 성공적으로 구성되면 연결 개체가 연결에 대한 정보를 표시합니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="ExpressRoute 회로 스크린샷":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>회로에 VNet 연결 - 다른 구독

여러 구독에서 ExpressRoute 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독에서 ExpressRoute 회로에 대한 작업을 공유하는 방법의 간단한 계통도가 나와 있습니다.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="ExpressRoute 회로 스크린샷":::

큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서에서 자체 구독을 사용하여 서비스를 배포하지만, 단일 ExpressRoute 회로를 공유하여 온-프레미스 네트워크에 다시 연결할 수 있습니다. 단일 부서(이 예제에서는 IT)가 ExpressRoute 회로를 소유할 수 있습니다. 조직 내의 다른 구독은 ExpressRoute 회로를 사용할 수 있습니다.

  > [!NOTE]
  > 전용 회로에 대한 연결 및 대역폭 요금은 ExpressRoute 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>관리 - 회로 소유자 및 회로 사용자 정보

’회로 소유자’는 ExpressRoute 회로 리소스의 인증된 고급 사용자입니다. 회로 소유자는 '회로 사용자'가 사용할 수 있는 권한 부여를 만들 수 있습니다. 회로 사용자는 ExpressRoute 회로와 동일한 구독 내에 있지 않은 가상 네트워크 게이트웨이의 소유자입니다. 회로 사용자는 가상 네트워크당 하나의 권한 부여를 사용할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크 연결이 삭제됩니다.

### <a name="circuit-owner-operations"></a>회로 소유자 작업

**연결 권한 부여를 만들려면**

회로 소유자는 가상 네트워크 게이트웨이를 ExpressRoute 회로에 연결하기 위해 회로 사용자가 사용할 권한 부여 키를 만드는 권한 부여를 만듭니다. 권한 부여는 하나의 연결에만 유효합니다.

> [!NOTE]
> 각 연결에는 별도의 권한이 필요합니다.
>

1. ExpressRoute 페이지에서 **권한 부여**를 선택한 다음, 권한 부여에 대한 **이름**을 입력하고 **저장**을 선택합니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="ExpressRoute 회로 스크린샷":::

2. 구성이 저장되면 **리소스 ID** 및 **권한 부여 키**를 복사합니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="ExpressRoute 회로 스크린샷":::

**연결 권한 부여를 삭제하려면**

연결에 대한 권한 부여 키에서 **삭제** 아이콘을 선택하여 연결을 삭제할 수 있습니다.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="ExpressRoute 회로 스크린샷":::

연결을 삭제하지만 권한 부여 키를 유지하려는 경우 회로의 연결 페이지에서 연결을 삭제할 수 있습니다.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="ExpressRoute 회로 스크린샷":::

### <a name="circuit-user-operations"></a>회로 사용자 작업

회로 사용자는 회로 소유자로부터 권한 부여 키를 받아야 하며 리소스 ID가 필요합니다.

**연결 권한 부여를 사용하려면**

1. **+ 리소스 만들기** 단추를 선택합니다. **연결**을 검색한 다음, **만들기**를 선택합니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="ExpressRoute 회로 스크린샷":::

1. *연결 유형*이 **ExpressRoute**로 설정되어 있는지 확인합니다. *리소스 그룹* 및 *위치*를 선택한 다음, 기본 사항 페이지에서 **확인**을 선택합니다.

    > [!NOTE]
    > 위치는 연결을 만들 가상 네트워크 게이트웨이 위치와 일치*해야 합니다*.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="ExpressRoute 회로 스크린샷":::

1. **설정** 페이지에서 *가상 네트워크 게이트웨이*를 선택하고 **권한 부여 사용** 확인란을 선택합니다. *권한 부여 키* 및 *피어 회로 URI*를 입력한 후 연결 이름을 지정합니다. **확인**을 선택합니다. 
 
    > [!NOTE]
    > *피어 회로 URI*는 ExpressRoute 회로의 리소스 ID입니다(ExpressRoute 회로의 속성 설정 창에서 찾을 수 있습니다).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="ExpressRoute 회로 스크린샷":::

1. **요약** 페이지에서 정보를 검토하고 **확인**을 선택합니다.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="ExpressRoute 회로 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

연결에 대한 페이지에서 **삭제** 아이콘을 선택하여 연결을 삭제하고 ExpressRoute 회로에 대한 VNet 연결을 해제할 수 있습니다.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="ExpressRoute 회로 스크린샷":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 네트워크를 동일한 구독 및 다른 구독의 회로에 연결하는 방법을 알아보았습니다. ExpressRoute 게이트웨이에 대한 자세한 내용은 다음을 참조하세요. 

> [!div class="nextstepaction"]
> [ExpressRoute 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md)
