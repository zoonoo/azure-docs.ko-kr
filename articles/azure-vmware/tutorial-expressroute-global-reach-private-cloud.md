---
title: 자습서 - 온-프레미스 환경을 프라이빗 클라우드로 피어링
description: Azure VMware Solution의 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558813"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>자습서: 온-프레미스 환경을 프라이빗 클라우드로 피어링

ExpressRoute Global Reach는 온-프레미스 환경을 Azure VMware Solution 프라이빗 클라우드에 연결합니다. ExpressRoute Global Reach 연결은 프라이빗 클라우드 ExpressRoute 회로와 온-프레미스 환경에 대한 기존 ExpressRoute 연결 간에 설정됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 온-프레미스 및 프라이빗 클라우드 간 ExpressRoute Global Reach 피어링을 사용하도록 설정합니다.


## <a name="before-you-begin"></a>시작하기 전에

ExpressRoute Global Reach를 사용하여 두 ExpressRoute 회로 간에 연결을 설정하기 전에 [다른 Azure 구독에서 연결을 설정하는 방법](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)에 대한 설명서를 검토하세요.  


## <a name="prerequisites"></a>사전 요구 사항

- 온-프레미스 환경을 Azure에 연결하는 데 사용되는 별도의 정상 작동하는 ExpressRoute 회로입니다.
- ExpressRoute 공급자의 서비스를 포함한 모든 게이트웨이가 4바이트 ASN(자율 시스템 번호)을 지원하는지 확인합니다. Azure VMware Solution은 경고를 알리는 데 4바이트 공용 ASN을 사용합니다.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>온-프레미스 ExpressRoute 회로에서 ExpressRoute 인증 키 만들기

1. **ExpressRoute 회로** 블레이드의 설정에서 **권한 부여** 를 선택합니다.

2. 권한 부여 키의 이름을 입력하고 **저장** 을 선택합니다.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="권한 부여를 선택하고 권한 부여 키의 이름을 입력합니다.":::
  
     만들기가 완료되면 회로의 권한 부여 키 목록에 새 키가 표시됩니다.
 
 4. 권한 부여 키와 ExpressRoute ID를 기록해 둡니다. 다음 단계에서 피어링을 완료하는 데 사용됩니다.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>프라이빗 클라우드를 온-프레미스로 피어링

1. 프라이빗 클라우드 **개요** 의 관리에서 **연결 > ExpressRoute Global Reach > 추가** 를 선택합니다.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="메뉴에서 연결, ExpressRoute Global Reach 탭, 추가를 차례로 선택합니다.":::

2. 이전 섹션에서 만든 ExpressRoute 경로 ID와 권한 부여 키를 입력합니다.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="ExpressRoute 경로 ID와 권한 부여 키를 입력한 다음, 만들기를 선택합니다.":::

3. **만들기** 를 선택합니다. 새 연결은 온-프레미스 클라우드 연결 목록에 표시됩니다.  

>[!TIP]
>**추가** 를 선택하여 목록에서 연결을 삭제하거나 연결을 끊을 수 있습니다.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="온-프레미스 연결 끊기 또는 삭제":::


## <a name="next-steps"></a>다음 단계

이 자습서에서는 온-프레미스 및 프라이빗 클라우드 간 ExpressRoute Global Reach 피어링을 사용하도록 설정하는 방법을 알아보았습니다. 

Azure VMware Solution 프라이빗 클라우드를 위한 VMware HCX 솔루션을 배포 및 구성하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [VMware HCX 배포 및 구성](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
