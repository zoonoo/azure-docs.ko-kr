---
title: '자습서: Azure Traffic Manager를 사용하여 우선 순위 트래픽 라우팅 구성'
description: 이 자습서에서는 Traffic Manager에서 우선 순위 트래픽 라우팅 방법을 구성하는 방법을 설명합니다.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208059"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>자습서: Traffic Manager에서 우선 순위 트래픽 라우팅 방법 구성

이 자습서에서는 가중 라우팅 방법을 사용하여 Azure Traffic Manager에서 사용자 트래픽을 특정 엔드포인트로 라우팅하는 방법에 대해 설명합니다. 이 라우팅 방법은 Traffic Manager 프로필 구성으로 이동하는 각 엔드포인트의 순서를 정의합니다. 사용자의 트래픽은 나열된 순서대로 엔드포인트로 라우팅됩니다. 이 라우팅 방법은 서비스 장애 조치를 구성하려는 경우에 유용합니다. 기본 엔드포인트는 '1' 우선 순위 번호를 받고, 들어오는 모든 요청을 처리합니다. 우선 순위가 낮은 엔드포인트는 백업으로 작동합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> - 우선 순위 라우팅을 사용하여 Traffic Manager 프로필을 만듭니다.
> - 엔드포인트를 추가합니다.
> - 엔드포인트의 우선 순위를 구성합니다.
> - Traffic Manager 프로필을 사용합니다.
> - Traffic Manager 프로필을 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="to-configure-the-priority-traffic-routing-method"></a>우선 순위 트래픽 라우팅 방법을 구성하려면 다음을 수행합니다.
1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽에서 **+ 리소스 만들기** 를 선택합니다. **Traffic Manager 프로필** 을 검색하고, **만들기** 를 선택합니다.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

1. *Traffic Manager 프로필 만들기* 페이지에서 다음 설정을 정의합니다.

    | 설정         | 값                                              |
    | ---             | ---                                                |
    | Name            | 사용자의 프로필에 사용할 이름을 제공합니다. 이 이름은 trafficmanager.net 영역 내에서 고유해야 합니다. Traffic Manager 프로필에 액세스하려면 `<profilename>.trafficmanager.net` DNS 이름을 사용합니다. |    
    | 라우팅 방법  | **우선 순위** 를 선택합니다. |
    | Subscription    | 구독을 선택합니다. |
    | Resource group   | 기존 리소스 그룹을 사용하거나 이 프로필에서 대체할 새 리소스 그룹을 만듭니다. 새 리소스 그룹을 만드는 경우 *리소스 그룹 위치* 드롭다운을 사용하여 리소스 그룹의 위치를 지정합니다. 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포되는 Traffic Manager 프로필에는 영향을 미치지 않습니다. |

1. **만들기** 를 선택하고, Traffic Manager 프로필을 배포합니다.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

## <a name="add-endpoints"></a>엔드포인트 추가

1. 목록에서 Traffic Manager 프로필을 선택합니다.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

1. *설정* 아래에서 **엔드포인트** 를 선택하고, **+ 추가** 를 선택하여 새 엔드포인트를 추가합니다.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

1. 다음 설정을 선택하거나 입력합니다. 

    | 설정                | 값                                              |
    | ---                    | ---                                                |
    | Type                   | 엔드포인트 유형을 선택합니다. |    
    | Name                   | 이 엔드포인트를 식별할 수 있는 이름을 지정합니다. |
    | 대상 리소스 종류   | 대상에 대한 리소스 종류를 선택합니다. |
    | 대상 리소스        | 목록에서 리소스를 선택합니다. |
    | 우선순위               | 이 엔드포인트에 대한 우선 순위 번호를 지정합니다. 1이 가장 높은 우선 순위입니다. |


1. **추가** 를 선택하여 엔드포인트를 추가합니다. 2단계와 3단계를 반복하여 추가 엔드포인트를 추가합니다. 적절한 우선 순위 번호를 설정해야 합니다.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

1. **엔드포인트** 페이지에서 엔드포인트에 대한 우선 순위를 검토합니다. **우선 순위** 트래픽 라우팅 방법을 선택하는 경우 선택한 엔드포인트의 순서가 중요합니다. 엔드포인트의 우선 순위 순서를 확인합니다.  기본 엔드포인트가 맨 위에 있습니다. 표시된 순서를 다시 확인합니다. 모든 요청이 첫 번째 엔드포인트로 라우팅되고, Traffic Manager에서 이를 비정상으로 감지하면 트래픽이 자동으로 다음 엔드포인트로 장애 조치됩니다. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

1. 엔드포인트 우선 순위를 변경하려면 엔드포인트를 선택하고, 우선 순위 값을 변경하고, **저장** 을 선택하여 엔드포인트 설정을 저장합니다.

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager 프로필 사용

1.  포털의 검색 창에서 이전 섹션에서 만든 **Traffic Manager 프로필** 이름을 검색하고, 표시되는 결과에서 해당 Traffic Manager 프로필을 선택합니다.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

1.  **Traffic Manager 프로필** 개요 페이지에는 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이는 라우팅 형식에서 결정된 대로 올바른 엔드포인트로 라우팅되도록 모든 클라이언트가 사용할 수 있습니다(예를 들어 웹 브라우저를 사용하여 이동). 이 경우 모든 요청이 첫 번째 엔드포인트로 라우팅되고, Traffic Manager에서 이를 비정상으로 감지하면 트래픽이 자동으로 다음 엔드포인트로 장애 조치(fail over)됩니다.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

1. Traffic Manager 프로필이 작동하면 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다.

## <a name="clean-up-resources"></a>리소스 정리

Traffic Manager 프로필이 더 이상 필요하지 않은 경우 해당 프로필을 찾아서 **프로필 삭제** 를 선택합니다.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Traffic Manager 우선 순위 프로필 만들기":::

## <a name="next-steps"></a>다음 단계

우선 순위 라우팅 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [우선 순위 라우팅 방법](traffic-manager-routing-methods.md#priority-traffic-routing-method)
