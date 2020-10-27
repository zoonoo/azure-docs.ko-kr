---
title: '자습서: Azure Traffic Manager를 사용하여 가중 라운드 로빈 트래픽 라우팅 구성'
description: 이 자습서에서는 Traffic Manager에서 라운드 로빈 방법을 사용하여 트래픽 부하를 분산하는 방법을 설명합니다
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207842"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>자습서: Traffic Manager에서 가중치 적용 트래픽 라우팅 방법 구성

일반적인 트래픽 라우팅 방법 패턴은 클라우드 서비스와 웹 사이트를 포함하는 일련의 동일한 엔드포인트를 제공하고 각 엔드포인트에 똑같이 트래픽을 전송하는 것입니다. 다음 단계에서는 이 유형의 트래픽 라우팅 방법을 구성하는 방법을 간략하게 설명합니다.

> [!NOTE]
> Azure 웹앱은 Azure 지역 내 웹 사이트에 대해 이미 라운드 로빈 부하 분산 기능을 제공합니다(여러 데이터 센터로 구성될 수 있음). Traffic Manager를 사용하면 여러 데이터 센터의 웹 사이트에 트래픽을 분산할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 가중 라우팅을 사용하여 Traffic Manager 프로필을 만듭니다.
> - Traffic Manager 프로필을 사용합니다.
> - Traffic Manager 프로필을 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>가중치 적용 트래픽 라우팅 방법 구성

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 포털의 검색 창에서 이전 섹션에서 만든 **Traffic Manager 프로필** 이름을 검색하고, 표시되는 결과에서 해당 Traffic Manager 프로필을 선택합니다.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Traffic Manager 프로필 검색":::

1. **구성** 을 선택하고, 다음 설정을 선택하거나 입력합니다.

    | 설정         | 값                                              |
    | ---             | ---                                                |
    | 라우팅 방법            | **가중** 을 선택합니다. |    
    | DNS TTL(Time to Live) | 이 값은 클라이언트의 로컬 캐싱 이름 서버에서 업데이트된 DNS 항목을 Traffic Manager 시스템에 쿼리하는 빈도를 제어합니다. 트래픽 라우팅 방법 변경 또는 추가된 엔드포인트의 가용성 변경과 같이 Traffic Manager에서 발생하는 모든 변경은 이 기간이 지난 후에 전체 DNS 서버 시스템에서 새로 고쳐집니다. |
    | 프로토콜    | 엔드포인트 모니터링에 대한 프로토콜을 선택합니다. *옵션: HTTP, HTTPS 및 TCP* |
    | 포트 | 포트 번호를 지정합니다. |
    | 경로 | 엔드포인트를 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다. |
    | 사용자 지정 헤더 설정 | 사용자 지정 헤더를 host:contoso.com,newheader:newvalue 형식으로 구성합니다. 지원되는 최대 쌍은 8입니다. Http 및 Https 프로토콜에 적용됩니다. 프로필의 모든 엔드포인트에 적용됩니다. |
    | 예상 상태 코드 범위(기본값: 200) | 상태 코드 범위를 200-299, 301-301 형식으로 구성합니다. 지원되는 최대 범위는 8입니다. Http 및 Https 프로토콜에 적용됩니다. 프로필의 모든 엔드포인트에 적용됩니다. |
    | 프로브 간격 | 엔드포인트 상태 프로브 사이의 시간 간격을 구성합니다. 10초 또는 30초를 선택할 수 있습니다. |
    | 허용되는 오류 수 | 엔드포인트 오류가 트리거될 때까지 허용되는 상태 프로브 오류 수를 구성합니다. 0에서 9 사이의 숫자를 입력할 수 있습니다. | 
    | 프로브 시간 제한 | 엔드포인트 상태 프로브 시간이 초과되기 전에 필요한 시간을 구성합니다. 이 값은 5 이상이어야 하며, 프로브 간격 값보다 작아야 합니다. |

1. **저장** 을 선택하여 구성을 완료합니다.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Traffic Manager 프로필 검색"::: 

1. **엔드포인트** 를 선택하고 각 엔드포인트의 가중치를 구성합니다. 가중치는 1~1000일 수 있습니다. 가중치가 높을수록 우선 순위가 높아집니다.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Traffic Manager 프로필 검색"::: 

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager 프로필 사용

**Traffic Manager 프로필** 에 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이 이름은 모든 클라이언트에서 라우팅 유형에 따라 결정된 올바른 엔드포인트로 라우팅(예: 웹 브라우저를 사용하여 이동)하는 데 사용할 수 있습니다. 이 경우 모든 요청은 라운드 로빈 방식으로 각 엔드포인트로 라우팅됩니다.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Traffic Manager 프로필 검색"::: 

## <a name="clean-up-resources"></a>리소스 정리

Traffic Manager 프로필이 더 이상 필요하지 않은 경우 해당 프로필을 찾아서 **프로필 삭제** 를 선택합니다.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Traffic Manager 프로필 검색":::

## <a name="next-steps"></a>다음 단계

가중 라우팅 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [가중 트래픽 라우팅 방법](traffic-manager-routing-methods.md#weighted)