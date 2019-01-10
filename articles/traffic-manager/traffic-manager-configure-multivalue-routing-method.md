---
title: Azure Traffic Manager에서 다중값 트래픽 라우팅 방법 구성 | Microsoft Docs
description: 이 문서에서는 A/AAAA 엔드포인트로 트래픽을 라우팅하도록 Traffic Manager를 구성하는 방법을 설명합니다.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: d4a51551fb0f9067929df1fdf074e7286938004b
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187708"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Traffic Manager에서 다중값 라우팅 방법 구성

이 문서에서는 다중값 트래픽 라우팅 방법을 구성하는 방법을 설명합니다. **다중값** 트래픽 라우팅 방법을 사용하면 여러 정상 엔드포인트를 반환할 수 있으며, 다른 DNS 조회하지 않고도 다시 시도할 수 있는 옵션이 클라이언트에게 있으므로 애플리케이션의 안정성을 높이는 데 도움이 됩니다. 다중값 라우팅은 IPv4 또는 IPv6 주소를 사용하여 지정된 모든 엔드포인트가 있는 프로필에서만 활성화할 수 있습니다. 쿼리가 이 프로필에 대해 수신되면 지정된 구성 가능한 최대 반환 수를 기반으로 모든 정상 엔드포인트가 반환됩니다. 

>[!NOTE]
> 이 시점에 IPv4 또는 IPv6 주소를 사용하여 엔드포인트를 추가하는 것은 **외부** 유형의 엔드포인트에만 지원됩니다. 따라서 다중값 라우팅도 그러한 엔드포인트에 대해서만 지원됩니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인 

https://portal.azure.com에서 Azure Portal에 로그인합니다.
## <a name="create-a-resource-group"></a>리소스 그룹 만들기
Traffic Manager 프로필에 대한 리소스 그룹을 만듭니다.
1. Azure Portal의 왼쪽 창에서 **리소스 그룹**을 선택합니다.
2. 페이지 맨 위에 있는 **리소스 그룹**에서 **추가**를 선택합니다.
3. **리소스 그룹 이름**에서 이름 *myResourceGroupTM1*을 입력합니다. **리소스 그룹 위치**에 대해서는 **미국 동부**를 선택한 다음, **확인**을 선택합니다.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기
대기 시간이 가장 짧은 엔드포인트로 사용자 트래픽을 보내서 트래픽을 향하게 하는 Traffic Manager 프로필을 만듭니다.

1. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기**를 선택합니다.
2. **Traffic Manager 프로필 만들기**에서 다음 정보를 입력하거나 선택하고, 나머지 설정은 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.
    
    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 이름                   | 이 이름은 trafficmanager.net 영역 내에서 고유해야 하며 DNS 이름, trafficmanager.net 형식으로 나타나고, Traffic Manager 프로필에 액세스하는 데 사용됩니다.                                   |
    | 라우팅 방법          | **다중값** 라우팅 방법을 선택합니다.                                       |
    | 구독            | 구독을 선택합니다.                          |
    | 리소스 그룹          | *myResourceGroupTM1*을 선택합니다. |
    | 위치                | 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포되는 Traffic Manager 프로필에는 영향을 미치지 않습니다.                              |
   |        |           | 
  
  ![Traffic Manager 프로필 만들기](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager 엔드포인트 추가

이전 단계에서 만든 다중값 Traffic Manager 프로필에 외부 엔드포인트로 두 개의 IP 주소를 추가합니다.

1. 포털의 검색 창에서 이전 섹션에서 만든 Traffic Manager 프로필 이름을 검색하고, 표시되는 결과에서 해당 프로필을 선택합니다.
2. **Traffic Manager 프로필**의 **설정** 섹션에서 **엔드포인트**를 클릭한 다음, **추가**를 클릭합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | type                    | 외부 엔드포인트                                   |
    | 이름           | myEndpoint1                                        |
    | FQDN(정규화된 도메인 이름) 또는 IP           | 이 Traffic Manager 프로필에 추가하려는 엔드포인트의 공용 IP 주소를 입력합니다.                         |
    |        |           |

4. **FQDN(정규화된 도메인 이름) 또는 IP**에 *myEndpoint2*라는 다른 엔드포인트를 추가하려면 2-3단계를 반복하고, 두 번째 엔드포인트의 공용 IP 주소를 입력합니다.
5.  두 엔드포인트 추가가 완료되면 **온라인**인 모니터링 상태와 함께 **Traffic Manager 프로필**에 표시됩니다.

   ![Traffic Manager 엔드포인트 추가](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>다음 단계

- [가중치 적용 트래픽 라우팅 방법](traffic-manager-configure-weighted-routing-method.md)에 대해 알아보세요.
- [우선 순위 라우팅 방법](traffic-manager-configure-priority-routing-method.md)에 대해 알아보세요.
- [성능 라우팅 방법](traffic-manager-configure-performance-routing-method.md)에 대해 알아보세요.
- [지리적 라우팅 방법](traffic-manager-configure-geographic-routing-method.md)에 대해 알아보세요.


