---
title: Azure Sentinel 관심 목록 사용
description: 이 문서에서는 Azure Sentinel 관심 목록에서 위협 조사, 비즈니스 데이터 가져오기, 허용 목록 만들기 및 이벤트 데이터 보강을 사용하는 방법을 설명합니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99555640"
---
# <a name="use-azure-sentinel-watchlists"></a>Azure Sentinel 관심 목록 사용

> [!IMPORTANT]
> 관심 목록 기능은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel 관심 목록을 사용하면 보유 중인 Azure Sentinel 환경에서 일어나는 이벤트와의 연결을 위해 외부 데이터에서 데이터를 수집할 수 있게 됩니다. 생성한 다음에는 검색, 검색 규칙, 위협 헌팅, 대응 플레이북에 관심 목록을 활용할 수 있습니다. 관심 목록은 Azure Sentinel 작업 영역에 이름 값 쌍으로 저장되며 쿼리 성능 최적화와 대기 시간 최소화를 위해 캐시됩니다.

관심 목록을 사용하는 일반적인 시나리오는 다음을 포함합니다.

- IP 주소, 파일 해시, 기타 데이터를 CSV 파일에서 신속하게 가져와 **위협을 조사** 하소 인시던트에 신속하게 대응합니다. 한 번 가져온 뒤부터는 관심 목록을 참가용 이름 값 쌍으로 사용하는 한편, 경고 규칙, 위협 헌팅, 통합 문서, 노트북, 일반 쿼리 관련 필터로 활용할 수 있습니다.

- 관심 목록으로 **비즈니스 데이터 가져오기**. 예를 들어 시스템 액세스 권한이 높은 사용자 목록이나 권한이 종료된 직원 사용자 목록을 가져온 뒤 관심 목록을 사용해 허가 목록과 불허 목록을 만들고 이를 통해 해당 사용자들이 네트워크에 로그인하는 것을 검색하거나 막습니다.

- **경고 피로 감소**. 일반적으로 경고를 트리거할 수 있는 작업을 수행하는 허가된 IP 주소로 들어온 사용자 그룹이 유발하는 경고를 억제하고 이러한 경고로 인한 무해한 이벤트 발생을 예방할 수 있게 허가 목록을 작성합니다.

- **이벤트 데이터 보강**. 관심 목록을 사용해 외부 데이터 소스에서 파생된 이름 값 조합으로 이벤트 데이터를 보강합니다.

## <a name="create-a-new-watchlist"></a>새 관심 목록 만들기

1. Azure Portal에서 **Azure Sentinel** > **구성** > **관심 목록** 으로 이동한 다음 **새 항목 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![새 관심 목록](./media/watchlists/sentinel-watchlist-new.png)

1. **일반** 페이지에서, 관심 목록에서의 이름, 설명, 별칭을 입력한 뒤 **다음** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![관심 목록 일반 페이지](./media/watchlists/sentinel-watchlist-general.png)

1. **원본** 페이지에서 데이터 세트 유형을 선택하고 파일을 업로드한 후 **다음** 을 선택합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="관심 목록 원본 페이지" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 현재 업로드 가능한 파일의 최대 크기는 3.8MB로 제한됩니다.

1. 정보를 검토하고 오류가 없는지 확인한 뒤 **만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![관심 목록 검토 페이지](./media/watchlists/sentinel-watchlist-review.png)

    관심 목록이 만들어지면 알림이 표시됩니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="관심 목록 생성 성공 알림" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>쿼리에서 관심 목록 사용

1. Azure Portal에서 **Azure Sentinel** > **구성** > **관심 목록** 으로 이동하여 사용할 관심 목록을 선택한 다음 **Log Analytics에서 보기** 를 선택합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="쿼리에서 관심 목록 사용" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. 관심 목록의 항목은 쿼리에 대해 자동으로 추출되고 **결과** 탭에 표시됩니다. 아래 예는 **ServerName** 및 **IpAddress** 필드의 추출 결과를 보여 줍니다.

    > [!NOTE]
    > 쿼리의 타임스탬프는 쿼리 UI와 예약된 경고에서 무시됩니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="관심 목록 필드가 있는 쿼리" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. 관심 목록을 조인 및 조회에 대한 테이블로 처리하여 관심 목록 데이터에 대한 테이블의 데이터를 쿼리할 수 있습니다.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="조회로 관심 목록에 대한 쿼리":::

## <a name="use-watchlists-in-analytics-rules"></a>분석 규칙에서 관심 목록 사용

분석 규칙에서 관심 목록을 사용하려면 Azure Portal에서 **Azure Sentinel** > **구성** > **분석** 로 이동하고 쿼리에서 `_GetWatchlist('<watchlist>')` 함수를 사용하여 규칙을 만듭니다.

1. 이 예에서는 다음 값을 사용하여 “ipwatchlist”라는 관심 목록을 만듭니다.

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="관심 목록에 대한 4가지 항목 목록":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="4가지 항목으로 관심 목록 만들기":::

1. 다음으로 분석 규칙을 만듭니다.  이 예에서는 관심 목록의 IP 주소에서 발생하는 이벤트만 포함합니다.

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="분석 규칙에서 관심 목록 사용":::

## <a name="view-list-of-watchlists-aliases"></a>관심 목록 별칭 목록 보기

관심 목록 별칭 목록을 가져오려면 Azure Portal에서 **Azure Sentinel** > **일반** > **로그** 로 이동하고 다음 쿼리를 실행합니다. `_GetWatchlistAlias`. **결과** 탭에서 별칭 목록을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![관심 목록](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel 관심 목록을 사용하여 데이터를 보강하고 조사를 개선하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
