---
title: Azure 센티널 watchlists 사용
description: 이 문서에서는 Azure 센티널 watchlists에서 위협 조사, 비즈니스 데이터 가져오기, 허용 목록 만들기 및 이벤트 데이터 보강을 사용 하는 방법을 설명 합니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 1267f040b13184f50c9d98fe0fb13fb24db0f4f7
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026839"
---
# <a name="use-azure-sentinel-watchlists"></a>Azure 센티널 watchlists 사용

Azure 센티널 watchlists는 Azure 센티널 환경의 이벤트와 상관 관계를 위해 외부 데이터 원본에서 데이터를 수집할 수 있도록 합니다. 일단 만들어지면 검색, 검색 규칙, 위협 검색 및 응답 플레이 북에서 watchlists을 사용할 수 있습니다. Watchlists는 Azure 센티널 작업 영역에 이름-값 쌍으로 저장 되며 최적의 쿼리 성능 및 짧은 대기 시간을 위해 캐시 됩니다.

Watchlists 사용에 대 한 일반적인 시나리오는 다음과 같습니다.

- IP 주소, 파일 해시 및 CSV 파일의 기타 데이터를 신속 하 게 가져오는 방법으로 **위협을 조사** 하 고 신속 하 게 문제에 대응 합니다. 가져온 후에는 경고 규칙, 위협 구하기, 통합 문서, 전자 필기장 및 일반 쿼리에서 조인 및 필터에 대 한 관심 목록 이름-값 쌍을 사용할 수 있습니다.

- **비즈니스 데이터** 를 Watchlists로 가져옵니다. 예를 들어 권한이 있는 시스템 액세스 또는 종료 된 직원의 사용자 목록을 가져온 다음 관심 목록를 사용 하 여 해당 사용자가 네트워크에 로그인 하지 못하도록 하는 허용 및 거부 목록을 만듭니다.

- **경고 피로을 줄입니다** . 일반적으로 경고를 트리거하는 작업을 수행 하는 권한이 부여 된 IP 주소의 사용자와 같은 사용자 그룹의 경고를 표시 하지 않고 무해 한 이벤트가 경고 되지 않도록 허용 목록을 만듭니다.

- **보강 이벤트 데이터** 입니다. Watchlists를 사용 하 여 외부 데이터 원본에서 파생 된 이름-값 조합을 사용 하 여 이벤트 데이터를 보강 합니다.

## <a name="create-a-new-watchlist"></a>새 관심 목록 만들기

1. Azure Portal에서 **Azure 센티널**  >  **Configuration**  >  **관심 목록** 로 이동한 후 **새로 추가** 를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![새 관심 목록](./media/watchlists/sentinel-watchlist-new.png)

1. **일반** 페이지에서 관심 목록에 대 한 이름, 설명 및 별칭을 입력 하 고 **다음** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![관심 목록 일반 페이지](./media/watchlists/sentinel-watchlist-general.png)

1. **원본** 페이지에서 데이터 집합 유형을 선택 하 고 파일을 업로드 한 후 **다음** 을 선택 합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="관심 목록 원본 페이지" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > 파일 업로드는 현재 최대 3.8 메가바이트의 파일로 제한 됩니다.

1. 정보를 검토 하 고 올바른지 확인 한 다음 **만들기** 를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![관심 목록 검토 페이지](./media/watchlists/sentinel-watchlist-review.png)

    관심 목록 만들어지면 알림이 표시 됩니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="관심 목록 원본 페이지" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>쿼리에서 watchlists 사용

1. Azure Portal에서 **Azure 센티널**  >  **Configuration**  >  **관심 목록** 로 이동 하 고 사용 하려는 관심 목록을 선택한 다음 **Log Analytics에서 보기** 를 선택 합니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="관심 목록 원본 페이지" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. 관심 목록의 항목은 쿼리에 대해 자동으로 추출 되 고 **결과** 탭에 표시 됩니다. 아래 예제에서는 **ServerName** 및 **IpAddress** 필드의 추출 결과를 보여 줍니다.

    > [!NOTE]
    > 쿼리 타임 스탬프는 쿼리 UI와 예약 된 경고에서 무시 됩니다.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="관심 목록 원본 페이지" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
## <a name="use-watchlists-in-analytics-rules"></a>분석 규칙에서 watchlists 사용

분석 규칙에서 watchlists를 사용 하려면 Azure Portal에서 **Azure 센티널** Configuration analytics로 이동 하 여  >  **Configuration**  >  **Analytics** 쿼리에 함수를 사용 하 여 규칙을 만듭니다 `_GetWatchlist('<watchlist>')` .

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="관심 목록 원본 페이지" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::

## <a name="view-list-of-watchlists-aliases"></a>Watchlists 별칭 목록 보기

관심 목록 별칭 목록을 가져오려면 Azure Portal에서 **Azure 센티널**  >  **일반**  >  **로그** 로 이동 하 고 다음 쿼리를 실행 `_GetWatchlistAlias` 합니다. **결과** 탭에서 별칭 목록을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![watchlists 나열](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널에서 watchlists를 사용 하 여 데이터를 보강 하 고 조사를 개선 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.

