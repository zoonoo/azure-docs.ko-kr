---
title: '자습서: Azure Traffic Manager를 사용하여 지리적 트래픽 라우팅 구성'
description: 이 자습서에서는 Azure Traffic Manager를 사용하여 지리적 트래픽 라우팅 방법을 구성하는 방법을 설명합니다.
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208116"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>자습서: Traffic Manager를 사용한 지리적 트래픽 라우팅 방법 구성

지리적 트래픽 라우팅 방법을 사용하면 요청이 발생하는 지리적 위치를 기반으로 특정 엔드포인트에 트래픽을 보낼 수 있습니다. 이 자습서는 이 라우팅 방법을 사용하여 Traffic Manager 프로필을 만들고, 특정 지역에서 트래픽을 받도록 엔드포인트를 구성하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 지리적 라우팅을 사용하여 Traffic Manager 프로필을 만듭니다.
> - 중첩 엔드포인트를 구성합니다.
> - Traffic Manager 프로필을 사용합니다.
> - Traffic Manager 프로필을 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽에서 **+ 리소스 만들기** 를 선택합니다. **Traffic Manager 프로필** 을 검색하고, **만들기** 를 선택합니다.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Traffic Manager 프로필 만들기":::

1. *Traffic Manager 프로필 만들기* 페이지에서 다음 설정을 정의합니다.

    | 설정         | 값                                              |
    | ---             | ---                                                |
    | Name            | 사용자의 프로필에 사용할 이름을 제공합니다. 이 이름은 trafficmanager.net 영역 내에서 고유해야 합니다. Traffic Manager 프로필에 액세스하려면 `<profilename>.trafficmanager.net` DNS 이름을 사용합니다. |    
    | 라우팅 방법  | **지리적** 을 선택합니다. |
    | Subscription    | 구독을 선택합니다. |
    | Resource group   | 기존 리소스 그룹을 사용하거나 이 프로필에서 대체할 새 리소스 그룹을 만듭니다. 새 리소스 그룹을 만드는 경우 *리소스 그룹 위치* 드롭다운을 사용하여 리소스 그룹의 위치를 지정합니다. 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포되는 Traffic Manager 프로필에는 영향을 미치지 않습니다. |

1. **만들기** 를 선택하고, Traffic Manager 프로필을 배포합니다.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Traffic Manager 프로필 만들기":::

## <a name="add-endpoints"></a>엔드포인트 추가

1. 목록에서 Traffic Manager 프로필을 선택합니다.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Manager 프로필 만들기":::

1. *설정* 아래에서 **엔드포인트** 를 선택하고, **+ 추가** 를 선택하여 새 엔드포인트를 추가합니다.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Traffic Manager 프로필 만들기":::

1. 다음 설정을 선택하거나 입력합니다. 

    | 설정                | 값                                              |
    | ---                    | ---                                                |
    | Type                   | 엔드포인트 유형을 선택합니다. 프로덕션에 사용되는 지리적 라우팅 프로필의 경우 둘 이상의 엔드포인트가 있는 자식 프로필을 포함하는 중첩 엔드포인트 형식을 사용하는 것이 좋습니다. 자세한 내용은 [지리적 트래픽 라우팅 방법 FAQ](traffic-manager-FAQs.md)를 참조하세요. |    
    | Name                   | 이 엔드포인트를 식별할 수 있는 이름을 지정합니다. |
    | 대상 리소스 종류   | 대상에 대한 리소스 종류를 선택합니다. |
    | 대상 리소스        | 목록에서 리소스를 선택합니다. |

    > [!Note]
    > 이 페이지의 특정 필드는 사용자가 추가하는 엔드포인트의 형식에 따라 달라집니다.
    > 1. Azure 엔드포인트를 추가하는 경우 트래픽을 보낼 리소스에 따라 **대상 리소스 형식** 및 **대상** 을 선택합니다.
    > 1. **외부** 엔드포인트를 추가하는 경우 사용자의 엔드포인트에 사용할 **FQDN(정규화된 도메인 이름)** 을 제공합니다.
    > 1. **중첩 엔드포인트** 를 추가하는 경우 사용할 자식 프로필에 해당하는 **대상 리소스** 를 선택하고 **최소 자식 엔드포인트 수** 를 지정합니다.

1. *지역 매핑* 섹션에서 드롭다운을 사용하여 트래픽을 이 엔드포인트로 보내려는 지역을 추가합니다. 하나 이상의 지역을 추가해야 합니다. 여러 지역을 매핑할 수 있습니다.

1. 이 프로필 아래에 추가하려는 모든 엔드포인트에 대해 마지막 단계를 반복한 다음, **저장** 을 선택합니다.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Traffic Manager 프로필 만들기":::

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager 프로필 사용

1.  포털의 검색 창에서 이전 섹션에서 만든 **Traffic Manager 프로필** 이름을 검색하고, 표시되는 결과에서 해당 Traffic Manager 프로필을 선택합니다.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager 프로필 만들기":::

1. **Traffic Manager 프로필** 에 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이 이름은 모든 클라이언트에서 라우팅 유형에 따라 결정된 올바른 엔드포인트로 라우팅(예: 웹 브라우저를 사용하여 이동)하는 데 사용할 수 있습니다. 지리적 라우팅을 사용하면 Traffic Manager에서 들어오는 요청의 원본 IP를 확인하여 해당 요청이 시작되는 지역을 결정합니다. 해당 지역이 엔드포인트에 매핑된 경우 트래픽은 그곳으로 라우팅됩니다. 이 지역이 엔드포인트에 매핑되지 않은 경우 Traffic Manager에서 NODATA 쿼리 응답을 반환합니다.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Traffic Manager 프로필 만들기":::

## <a name="clean-up-resources"></a>리소스 정리

Traffic Manager 프로필이 더 이상 필요하지 않은 경우 해당 프로필을 찾아서 **프로필 삭제** 를 선택합니다.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Traffic Manager 프로필 만들기":::

## <a name="next-steps"></a>다음 단계

지리적 라우팅 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [지리적 트래픽 라우팅 메서드](traffic-manager-routing-methods.md#geographic)
