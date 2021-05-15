---
title: 온-프레미스 환경을 Azure VMware Solution에 피어링
description: Azure VMware Solution의 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 04/27/2021
ms.openlocfilehash: fb0d8bc8391c96449cdf260c543cfed1af1125f5
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108176111"
---
# <a name="peer-on-premises-environments-to-azure-vmware-solution"></a>온-프레미스 환경을 Azure VMware Solution에 피어링

빠른 시작의 이 단계에서는 Azure VMware Solution을 온-프레미스 환경에 연결합니다. ExpressRoute Global Reach는 온-프레미스 환경을 Azure VMware Solution 프라이빗 클라우드에 연결합니다. ExpressRoute Global Reach 연결은 프라이빗 클라우드 ExpressRoute 회로와 온-프레미스 환경에 대한 기존 ExpressRoute 연결 간에 설정됩니다. 


>[!NOTE]
>VPN을 통해 연결할 수 있지만 이 빠른 시작 문서의 범위를 벗어나는 것입니다.

이 자습서에서는 다이어그램에 표시된 대로 연결됩니다.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach 온-프레미스 네트워크 연결을 보여주는 다이어그램." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::


## <a name="before-you-begin"></a>시작하기 전에

ExpressRoute Global Reach를 사용하여 두 ExpressRoute 회로 간에 연결을 설정하기 전에 [다른 Azure 구독에서 연결을 설정하는 방법](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)에 대한 설명서를 검토하세요.  

## <a name="prerequisites"></a>사전 요구 사항
- 온-프레미스 환경을 Azure에 연결하는 데 사용되는 별도의 정상 작동하는 ExpressRoute 회로로, 피어링의 _회로 1_ 입니다.
- ExpressRoute 공급자 서비스를 포함한 모든 게이트웨이가 4바이트 ASN(자율 시스템 번호)을 지원하는지 확인합니다. Azure VMware Solution은 경고를 알리는 데 4바이트 공용 ASN을 사용합니다.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>온-프레미스 ExpressRoute 회로에서 ExpressRoute 권한 부여 키를 만듭니다.

1. **ExpressRoute 회로** 블레이드의 설정에서 **권한 부여** 를 선택합니다.

1. 권한 부여 키의 이름을 입력하고 **저장** 을 선택합니다.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="권한 부여를 선택하고 권한 부여 키의 이름을 입력합니다.":::

   만들기가 완료되면 회로의 권한 부여 키 목록에 새 키가 표시됩니다.

1. 권한 부여 키와 ExpressRoute ID를 기록해 둡니다. 다음 단계에서 피어링을 완료하는 데 사용됩니다.

## <a name="peer-private-cloud-to-on-premises"></a>프라이빗 클라우드를 온-프레미스로 피어링 
프라이빗 클라우드 ExpressRoute 회로에 대한 권한 부여 키를 만들었으므로, 이제 온-프레미스 ExpressRoute 회로를 통해 피어링할 수 있습니다. **Azure Portal** 의 온-프레미스 ExpressRoute 회로에서 피어링이 수행됩니다. 프라이빗 클라우드 ExpressRoute 회로의 리소스 ID(ExpressRoute 회로 ID)와 권한 부여 키를 사용하여 피어링을 완료합니다.

1. 프라이빗 클라우드의 관리에서 **연결** > **ExpressRoute Global Reach** > **추가** 를 선택합니다.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Solution 프라이빗 클라우드의 ExpressRoute Global Reach 탭을 보여주는 스크린샷.":::

1. 이전 섹션에서 만든 ExpressRoute 경로 ID와 권한 부여 키를 입력합니다.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="연결 정보를 입력하는 대화 상자를 보여주는 스크린샷":::   

1. **만들기** 를 선택합니다. 새 연결은 온-프레미스 클라우드 연결 목록에 표시됩니다.

>[!TIP]
>**추가** 를 선택하여 목록에서 연결을 삭제하거나 연결을 끊을 수 있습니다.  
>
>:::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="온-프레미스 연결 끊기 또는 삭제":::


## <a name="verify-on-premises-network-connectivity"></a>온-프레미스 네트워크 연결 확인

이제 ExpressRoute에서 NSX 네트워크 세그먼트와 Azure VMware Solution 관리 세그먼트를 연결하는 **온-프레미스 에지 라우터** 에 표시됩니다.

>[!IMPORTANT]
>모든 사용자는 다른 환경을 가지며, 일부는 이러한 경로를 온-프레미스 네트워크에 다시 전파하도록 허용해야 합니다.  

## <a name="next-steps"></a>다음 단계
Azure VMware Solution 프라이빗 클라우드를 위한 VMware HCX 솔루션을 배포 및 구성하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [VMware HCX 배포 및 구성](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
