---
title: '회로의 피어링 구성 - ExpressRoute: Azure | Microsoft Docs'
description: 이 문서를 만들고 ExpressRoute 개인 및 Microsoft 피어 링을 프로 비전 하는 단계에 설명 합니다. 이 문서에서는 상태를 확인 하는 방법을 보여 줍니다 업데이트 또는 회로의 피어 링을 삭제 합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: f6061710fb15d4183bd42a82c4bd269a69fc9be2
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964447"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>ExpressRoute 회로의 피어링 만들기 및 수정

이 문서에서는 Azure portal을 사용 하 여 Azure 리소스 관리자 (ARM) ExpressRoute 회로 대 한 라우팅 구성을 만들고 관리 합니다. ExpressRoute 회로에 대한 피어링의 상태를 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행할 수도 있습니다. 회로를 사용하는 다른 메서드를 사용하려는 경우 다음 목록에서 문서를 선택합니다.

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [비디오 - 개인 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [비디오 - 공용 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [비디오 - Microsoft 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-routing-classic.md)
> 

Azure 개인 및 ExpressRoute 회로에 Microsoft 피어 링을 구성할 수 있습니다 (Azure 공용 피어 링은 사용 되지 않는 새 회로 대 한). 선택한 순서로 피어링을 구성할 수 있습니다. 그러나 각 피어링의 구성을 한 번에 하나 씩 완료하도록 해야 합니다. 라우팅 도메인 및 피어링에 대한 자세한 내용은 [회로 및 피어링 정보](expressroute-circuit-peerings.md)를 참조하세요.

## <a name="configuration-prerequisites"></a>필수 구성 요소

* 구성을 시작하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 페이지, [라우팅 요구 사항](expressroute-routing.md) 페이지 및 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.
* 활성화된 ExpressRoute 회로가 있어야 합니다. 지침을 수행하여 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-portal-resource-manager.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. 피어 링을 구성 하려면 ExpressRoute 회로 프로 비전 하 고 활성화 된 상태에서 여야 합니다. 
* 공유 키 /md5 해시를 사용 하려는 경우에 터널의 양쪽 모두에이 사용 하 여 최대 25 영숫자 문자의 수를 제한 해야 합니다. 특수 문자는 지원 되지 않습니다. 

이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IPVPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다. 

> [!IMPORTANT]
> 현재는 서비스 관리 포털을 통해 서비스 공급자가 구성한 피어링을 보급하지 않습니다. 이 기능을 곧 사용할 수 있도록 개발 중입니다. BGP 피어링을 구성하기 전에 서비스 공급자에게 확인하세요.
> 
> 

## <a name="msft"></a>Microsoft 피어링

이 섹션은 ExpressRoute 회로에 Microsoft 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

> [!IMPORTANT]
> 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다. 2017년 8월 1일 이후에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터가 회로에 연결될 때까지 접두사가 보급되지 않습니다. 자세한 내용은 [Microsoft 피어링에 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft 피어링을 만들려면

1. ExpressRoute 회로를 구성합니다. 계속하기 전에 연결 공급자에 의해 회로가 완전이 프로비전되었는지 확인합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Microsoft 피어링을 사용하도록 할 수 있습니다. 이 경우 다음 섹션에 나열 된 지침에 따라 필요가 없습니다. 그러나 연결 공급자에서 회로 만든 후 라우팅을 관리 하지 않는 경우 다음 단계를 진행 합니다.

   ![Microsoft 피어링 나열](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 회로에 Microsoft 피어링을 구성합니다. 진행하기 전에 다음 정보가 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.
   * 보조 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.
   * 이 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다. 기본 링크와 보조 링크에 동일한 VLAN ID를 사용해야 합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사 목록을 제공해야 합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우 쉼표로 구분된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다.
   * **선택 사항 -** 고객 ASN: 피어링 AS 숫자에 등록되지 않은 광고 접두사인 경우 등록된 AS 번호를 지정할 수 있습니다.
   * 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/IRR을 지정할 수 있습니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.
3. 다음 예제와 같이 구성하려는 피어링을 선택할 수 있습니다. Microsoft 피어링 행을 선택합니다.

   ![Microsoft 피어링 행 선택](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Microsoft 피어링을 구성합니다. 다음 이미지는 구성 예를 보여줍니다.

   ![Microsoft 피어링 구성](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. 모든 매개 변수를 지정한 후에 구성을 저장합니다.

   회로가 ‘유효성 검사가 필요’한 상태가 되면(이미지에 표시됨), 지원 팀에 접두사에 대한 소유권의 증거를 보여주는 지원 티켓을 열어야 합니다.

   ![Microsoft 피어링 구성 저장](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

   다음 예제와 같이 포털에서 바로 지원 티켓을 열 수 있습니다.

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. 구성을 성공적으로 수락 된 후 다음 이미지와 비슷한 내용이 표시 됩니다.

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>Microsoft 피어링 세부 정보를 보려면

Microsoft 피어 링을 선택 하 여 피어 링의 속성을 볼 수 있습니다.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>Microsoft 피어링 구성을 업데이트하려면

피어링의 행을 선택하고 피어링 속성을 수정할 수 있습니다.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>Microsoft 피어링을 삭제하려면

다음 이미지처럼 삭제 아이콘을 선택하면 피어링 구성을 제거할 수 있습니다.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Azure 개인 피어링

이 섹션은 ExpressRoute 회로에 Azure 프라이빗 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

### <a name="to-create-azure-private-peering"></a>Azure 프라이빗 피어링을 만들려면

1. ExpressRoute 회로를 구성합니다. 계속하기 전에 연결 공급자에 의해 회로가 완전이 프로비전되었는지 확인합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 프라이빗 피어링을 사용하도록 할 수 있습니다. 이 경우 다음 섹션에 나열 된 지침에 따라 필요가 없습니다. 그러나 연결 공급자에서 회로 만든 후 라우팅을 관리 하지 않는 경우 다음 단계를 진행 합니다.

   ![list](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 회로에 Azure 프라이빗 피어링을 구성합니다. 다음 단계를 계속 진행하기 전에 다음 항목이 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 서브넷은 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.
   * 보조 링크에 대한 /30 서브넷입니다. 서브넷은 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.
   * 이 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다. 기본 링크와 보조 링크에 동일한 VLAN ID를 사용해야 합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다. 이 피어링에는 65515에서 65520까지의 숫자를 제외한 프라이빗 AS 숫자를 사용할 수 있습니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.
3. 다음 예제와 같이 Azure 프라이빗 피어링 행을 선택합니다.

   ![프라이빗](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. 프라이빗 피어링을 구성합니다. 다음 이미지는 구성 예를 보여줍니다.

   ![프라이빗 피어링 구성](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. 모든 매개 변수를 지정한 후에 구성을 저장합니다. 구성이 성공적으로 수락되고 나면 다음 예와 유사한 내용을 볼 수 있습니다.

   ![프라이빗 피어링 저장](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Azure 개인 피어링 세부 정보를 보려면

피어링을 선택하면 Azure 프라이빗 피어링의 속성을 볼 수 있습니다.

![프라이빗 피어링 보기](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Azure 개인 피어링 구성을 업데이트하려면

피어링의 행을 선택하고 피어링 속성을 수정할 수 있습니다.

![프라이빗 피어링 업데이트](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Azure 개인 피어링을 삭제하려면

다음 이미지처럼 삭제 아이콘을 선택하면 피어링 구성을 제거할 수 있습니다.

> [!WARNING]
> 이 예제를 실행하기 전에 모든 가상 네트워크 및 ExpressRoute Global Reach 연결을 제거해야 합니다. 
> 
> 

![프라이빗 피어링 삭제](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Azure 공용 피어링

이 섹션은 ExpressRoute 회로에 Azure 공용 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

> [!Note]
> 새 회로 대 한 azure 공용 피어 링 하는 사용 되지 않습니다. 자세한 내용은 [ExpressRoute 피어 링](expressroute-circuit-peerings.md)합니다.
>

### <a name="to-create-azure-public-peering"></a>Azure 공용 피어링을 만들려면

1. ExpressRoute 회로를 구성합니다. 계속하기 전에 연결 공급자에 의해 회로가 완전이 프로비전되었는지 확인합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 공용 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 다음 단계를 사용하여 구성을 계속합니다.

   ![공용 피어링 나열](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. 회로에 Azure 공용 피어링을 구성합니다. 다음 단계를 계속 진행하기 전에 다음 항목이 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다. 
   * 보조 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.
   * 이 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다. 기본 링크와 보조 링크에 동일한 VLAN ID를 사용해야 합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.
3. 다음 이미지와 같이 Azure 공용 피어링 행을 선택합니다.

   ![공용 피어링 행 선택](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. 공용 피어링을 구성합니다. 다음 이미지는 구성 예를 보여줍니다.

   ![공용 피어링 구성](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. 모든 매개 변수를 지정한 후에 구성을 저장합니다. 구성이 성공적으로 수락되고 나면 다음 예와 유사한 내용을 볼 수 있습니다.

   ![공용 피어링 구성 저장](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Azure 공용 피어링 세부 정보를 보려면

피어링을 선택하면 Azure 공용 피어링의 속성을 볼 수 있습니다.

![공용 피어 링 속성 보기](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Azure 공용 피어링 구성을 업데이트하려면

피어링의 행을 선택하고 피어링 속성을 수정할 수 있습니다.

![공용 피어링 행 선택](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Azure 공용 피어링을 삭제하려면

다음 예제와 같이 삭제 아이콘을 선택하면 피어링 구성을 제거할 수 있습니다.

![공용 피어링 삭제](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>다음 단계

다음 단계로 [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)합니다.
* ExpressRoute 워크플로에 대한 자세한 내용은 [ExpressRoute 워크플로](expressroute-workflows.md)를 참조하세요.
* 회로 피어링에 대한 자세한 내용은 [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
* 가상 네트워크 작업에 대한 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.
