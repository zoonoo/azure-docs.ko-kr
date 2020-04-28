---
title: '가상 허브의 유효 경로 보기: Azure 가상 WAN | Microsoft Docs'
description: Azure 가상 WAN의 가상 허브에 대 한 유효 경로 뷰
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515851"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>가상 허브의 유효 경로 보기

Azure Portal에서 가상 WAN 허브의 모든 경로를 볼 수 있습니다. 경로를 보려면 가상 허브로 이동한 다음 **라우팅-> 유효 경로 보기**를 선택 합니다.

## <a name="understanding-routes"></a><a name="understand"></a>경로 이해

다음 예제는 가상 WAN 라우팅이 표시 되는 방식을 더 잘 이해 하는 데 도움이 될 수 있습니다.

이 예제에서는 3 개의 허브가 있는 가상 WAN을 사용 합니다. 첫 번째 허브는 미국 동부 지역에 있고, 두 번째 허브는 유럽 서부 지역에 있고, 세 번째 허브는 미국 서 부 지역에 있습니다. 가상 WAN에서는 모든 허브가 상호 연결 되어 있습니다. 이 예제에서는 미국 동부 및 유럽 서부 허브가 온-프레미스 분기 (스포크)와 Azure 가상 네트워크 (스포크)에서 연결 된 것으로 가정 합니다.

10.4.0.6 (네트워크 가상 어플라이언스)를 사용 하는 Azure VNet 스포크 (10.4.0.0/16)는 VNet (10.5.0.0/16)에 추가 피어 링. 허브 경로 테이블에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 [추가 정보](#abouthubroute) 를 참조 하세요.

이 예제에서는 유럽 서부 분기 1이 미국 동부 허브 및 유럽 서부 허브에도 연결 되어 있다고 가정 합니다. 미국 동부 지역의 Express 경로 회로는 분기 2를 미국 동부 허브에 연결 합니다.

![다이어그램](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>유효 경로 보기

포털에서 ' 유효 경로 보기 '를 선택 하면 미국 동부 허브에 대 한 [허브 경로 테이블](#routetable) 에 표시 된 출력이 생성 됩니다.

이를 원근에 배치 하기 위해 첫 번째 줄은 VPN *다음 홉 유형* 연결 (' next 홉 ' VPN GATEWAY Instance0 ip 10.1.0.6, Instance1 ip 10.1.0.7)으로 인해 미국 동부 허브가 10.20.1.0/24 (분기 1)의 경로를 배웠습니다. *경로 원본이* 리소스 ID를 가리킵니다. *As path* 는 분기 1의 as 경로를 나타냅니다.

### <a name="hub-route-table"></a><a name="routetable"></a>허브 경로 테이블

테이블의 맨 아래에 있는 스크롤 막대를 사용 하 여 "AS Path"를 볼 수 있습니다.

| **접두사** |  **다음 홉 유형** | **다음 홉** |  **경로 원점** |**AS Path** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/Ssourceg/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/Ssourceg/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/Ssourceg/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|연결 Virtual Network| 링크 |  |  |
|10.5.0.0/16| IP 주소| 10.4.0.6|/subscriptions/`<sub>`/Ssourceg/`<rg>`/providers/microsoft.network/virtualhubs/easthub_1/routetables/table_1| |
|0.0.0.0/0| IP 주소| `<Azure Firewall IP>` |/subscriptions/`<sub>`/Ssourceg/`<rg>`/providers/microsoft.network/virtualhubs/easthub_1/routetables/table_1| |
|10.22.1.0/16| 원격 허브|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/Ssourceg/`<rg>`/providers/microsoft.network/virtualhubs/westhub_| 4848-22000 |
|10.9.0.0/16| 원격 허브|  링크 |/subscriptions/`<sub>`/Ssourceg/`<rg>`/providers/microsoft.network/virtualhubs/westhub_1| |

>[!NOTE]
> 미국 동부와 유럽 서부 허브가 예제 토폴로지에서 서로 통신 하지 않는 경우 학습 된 경로 (10.9.0.0/16)가 존재 하지 않습니다. 허브는 직접 연결 된 네트워크만을 보급 합니다.
>

## <a name="additional-information"></a><a name="additional"></a>추가 정보

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>허브 경로 테이블 정보

가상 허브 경로를 만들어 가상 허브 경로 테이블에 적용할 수 있습니다. 가상 허브 경로 테이블에 여러 경로를 적용할 수 있습니다. 이렇게 하면 IP 주소 (일반적으로 스포크 VNet의 NVA (네트워크 가상 어플라이언스))를 통해 대상 VNet의 경로를 설정할 수 있습니다. Nva에 대 한 자세한 내용은 [가상 허브에서 NVA로 트래픽 라우팅](virtual-wan-route-table-portal.md)을 참조 하세요.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>기본 경로 정보 (0.0.0.0/0)

가상 허브는 연결에서 플래그가 ' 사용 ' 인 경우 학습 된 기본 경로를 가상 네트워크, 사이트 간 VPN 및 Express 경로 연결에 전파 하는 기능을 제공 합니다. 이 플래그는 가상 네트워크 연결, VPN 연결 또는 Express 경로 연결을 편집할 때 표시 됩니다. ' EnableInternetSecurity '는 항상 허브 VNet, Express 경로 및 VPN 연결에 대해 기본적으로 false입니다.

기본 경로는 가상 WAN 허브에서 시작 되지 않습니다. 허브에 방화벽을 배포 하는 경우 또는 다른 연결 된 사이트에서 강제 터널링을 사용 하는 경우 기본 경로가 가상 WAN 허브에서 이미 학습 된 경우 전파 됩니다.

## <a name="next-steps"></a>다음 단계

가상 WAN에 대 한 자세한 내용은 [가상 Wan 개요](virtual-wan-about.md)를 참조 하세요.