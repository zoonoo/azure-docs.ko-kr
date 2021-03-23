---
title: 자습서 - 온-프레미스 환경을 프라이빗 클라우드로 피어링
description: Azure VMware Solution의 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: ae92bf89a08c5fade8757e3ee596c4ed4a5e6389
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494173"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>자습서: 온-프레미스 환경을 프라이빗 클라우드로 피어링

ExpressRoute Global Reach는 온-프레미스 환경을 Azure VMware Solution 프라이빗 클라우드에 연결합니다. ExpressRoute Global Reach 연결은 프라이빗 클라우드 ExpressRoute 회로와 온-프레미스 환경에 대한 기존 ExpressRoute 연결 간에 설정됩니다. 

[Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹을 구성](tutorial-configure-networking.md)할 때 사용하는 ExpressRoute 회로를 사용하려면 권한 부여 키를 만들고 사용해야 합니다.  이미 ExpressRoute 회로에서 권한 부여 키 하나를 사용했으며 이 자습서에서는 온-프레미스 ExpressRoute 회로를 통해 피어링하는 두 번째 권한 부여 키를 만듭니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 프라이빗 클라우드 ExpressRoute 회로인 _회로 2_ 에 대한 두 번째 권한 부여 키를 만듭니다.
> * _회로 1_ 구독의 Cloud Shell 메서드에서 Azure Portal 또는 Azure CLI를 사용하여 온-프레미스-프라이빗 클라우드 ExpressRoute Global Reach 피어링을 사용하도록 설정합니다.


## <a name="before-you-begin"></a>시작하기 전에

ExpressRoute Global Reach를 사용하여 두 ExpressRoute 회로 간에 연결을 설정하기 전에 [다른 Azure 구독에서 연결을 설정하는 방법](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)에 대한 설명서를 검토하세요.  

## <a name="prerequisites"></a>사전 요구 사항

- ExpressRoute 회로가 Azure VNet(가상 네트워크)에서 ExpressRoute 게이트웨이를 통해 피어링된 Azure VMware Solution 프라이빗 클라우드에 대해 설정된 연결 - 피어링 프로시저의 관점에서 보자면 회로 2입니다.
- 온-프레미스 환경을 Azure에 연결하는 데 사용되는 별도의 정상 작동하는 ExpressRoute 회로 - 피어링 프로시저의 관점에서 보자면 회로 1입니다.
- ExpressRoute Global Reach 피어링에 대한 /29 겹치지 않는 [네트워크 주소 블록](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)
- ExpressRoute 공급자의 서비스를 포함한 모든 게이트웨이가 4바이트 ASN(자율 시스템 번호)을 지원하는지 확인합니다. Azure VMware Solution은 경고를 알리는 데 4바이트 공용 ASN을 사용합니다.

>[!IMPORTANT]
>이러한 필수 구성 요소와 관련하여 온-프레미스 ExpressRoute 회로는 _회로 1_ 이고, 프라이빗 클라우드 ExpressRoute 회로는 다른 구독에 있으며 _회로 2_ 라는 레이블이 지정됩니다.

## <a name="create-an-expressroute-authorization-key-in-the-on-premises-circuit"></a>온-프레미스 회로에서 ExpressRoute 권한 부여 키 만들기

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>권한 부여 키를 사용하여 프라이빗 클라우드를 온-프레미스에 피어링
프라이빗 클라우드 ExpressRoute 회로에 대한 권한 부여 키를 만들었으므로, 이제 온-프레미스 ExpressRoute 회로를 통해 피어링할 수 있습니다. 피어링은 **Azure Portal** 에서 온-프레미스 ExpressRoute 회로의 관점에서 수행되거나 **Cloud Shell의 Azure CLI** 를 사용하여 수행됩니다. 두 방법 모두는 프라이빗 클라우드 ExpressRoute 회로의 리소스 ID와 권한 부여 키를 사용하여 피어링을 완료합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)
 
1. 온-프레미스 ExpressRoute 회로와 동일한 구독을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 프라이빗 클라우드 **개요** 의 관리에서 **연결** > **ExpressRoute Global Reach** > **추가** 를 선택합니다.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Solution 프라이빗 클라우드의 ExpressRoute Global Reach 탭을 보여주는 스크린샷.":::

1. 온-프레미스 클라우드 연결을 만듭니다. 다음 중 하나를 수행한 다음, **만들기** 를 선택합니다.

   - 목록에서 **ExpressRoute** 회로를 선택하거나
   - 회로 ID가 있는 경우 필드에 붙여넣고 권한 부여 키를 제공합니다.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="ExpressRoute 경로 ID와 권한 부여 키를 입력한 다음, 만들기를 선택합니다.":::   
   
   새 연결은 온-프레미스 클라우드 연결 목록에 표시됩니다.

>[!TIP]
>**추가** 를 선택하여 목록에서 연결을 삭제하거나 연결을 끊을 수 있습니다.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="온-프레미스 연결 끊기 또는 삭제":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[CLI 명령](../expressroute/expressroute-howto-set-global-reach-cli.md)은 온-프레미스 환경 간에 Azure VMware Solution 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 구성하는 데 도움이 되는 세부 정보 및 예제가 추가되었습니다.

>[!TIP]
>Azure CLI 명령 출력을 간소화하기 위해 이러한 지침에서 [`–query` 인수](https://docs.microsoft.com/cli/azure/query-azure-cli)를 사용하여 JMESPath 쿼리를 실행하여 필요한 결과만 표시할 수 있습니다.

1. 온-프레미스 ExpressRoute 회로와 동일한 구독을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. Cloud Shell을 열고 셸을 bash로 유지합니다.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Azure Portal Cloud Shell을 보여주는 스크린샷.":::

1. 회로 1에 대한 피어링을 만들고 회로 2의 리소스 ID와 권한 부여 키를 전달합니다. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="회로 1과 회로 2 간의 성공적인 피어링 결과와 명령을 보여주는 스크린샷.":::

ExpressRoute Global Reach 피어링을 통해 온-프레미스 환경에서 프라이빗 클라우드로 연결할 수 있습니다.

>[!TIP]
>[온-프레미스 네트워크 간 연결 사용 안 함](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) 지침에 따라 피어링을 삭제할 수 있습니다.


---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 온-프레미스 및 프라이빗 클라우드 간 ExpressRoute Global Reach 피어링을 사용하도록 설정하는 방법을 알아보았습니다. 

Azure VMware Solution 프라이빗 클라우드를 위한 VMware HCX 솔루션을 배포 및 구성하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [VMware HCX 배포 및 구성](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
