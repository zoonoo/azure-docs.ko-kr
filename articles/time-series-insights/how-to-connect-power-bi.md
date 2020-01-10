---
title: Power BI-Azure Time Series Insights에 환경 연결 Microsoft Docs
description: Azure Time Series Insights를 Power BI에 연결 하 여 조직 전체에서 데이터를 공유 하 고, 차트를 표시 하 고, 표시 하는 방법을 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 1f6b753052b882b75884503f8c05f34b8cead821
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749350"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Power BI의 Time Series Insights에서 데이터 시각화

Azure Time Series Insights는 클라우드에서 시계열 데이터를 저장, 관리, 쿼리 및 시각화 하는 데 사용할 수 있는 플랫폼입니다. [Power BI](https://powerbi.microsoft.com) 는 조직 전체에서 정보 및 결과를 공유할 수 있도록 하는 풍부한 시각화 기능을 제공 하는 비즈니스 분석 도구입니다. 이제 두 서비스를 통합 하 여 Time Series Insights의 고유 시각화 기능과 Power BI를 모두 활용할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

* 클라우드 커넥터를 사용 하 여 Power BI에 Time Series Insights 연결
* Power BI 데이터를 사용 하 여 시각적 개체 만들기
* Power BI에 보고서를 게시 하 고 나머지 조직과 공유

이 과정을 완료 하면 Azure Time Series Insights를 통해 시계열 데이터를 시각화 하 고 Power BI의 강력한 데이터 시각화 및 손쉬운 공유 기능을 사용 하 여이 데이터를 개선 하는 방법을 배우게 됩니다.

아직 없는 경우 [무료 Azure 구독](https://azure.microsoft.com/free/) 에 등록 해야 합니다.

## <a name="prerequisites"></a>필수 조건

* 최신 버전의 [Power BI Desktop](https://powerbi.microsoft.com/downloads/) 다운로드 및 설치
* [Azure Time Series Insights 미리 보기 인스턴스](time-series-insights-update-how-to-manage.md) 를 만들거나 만듭니다.

> [!IMPORTANT]
> Power BI 커넥터는 현재 **웜 스토어**용으로 구성 된 Time Series Insights 미리 보기 *종 량* 제 환경에서 지원 됩니다.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Time Series Insights의 데이터를 Power BI에 연결

Time Series Insights 환경을 Power BI에 연결 하려면 다음 단계를 수행 합니다.

1. Time Series Insights 탐색기 열기
1. 데이터를 쿼리 또는 원시 데이터로 내보내기
1. Power BI Desktop 열기
1. 사용자 지정 쿼리에서 로드

### <a name="export-data-into-power-bi-desktop"></a>Power BI desktop으로 데이터 내보내기

시작하려면:

1. Time Series Insights Preview 탐색기를 열고 데이터를 만들어 둡니다.
1. 만족 하는 보기를 만들었으면 **추가 작업** 드롭다운 메뉴로 이동 하 여 **Power BI에 연결**을 클릭 합니다.

    [![Time Series Insights Preview 탐색기 내보내기](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. 이 탭 내에서 매개 변수를 설정 합니다.

   1. 보려는 상대 기간을 지정 합니다. 기존 보기에 만족 하는 경우 **기존 기간**으로 그대로 둡니다.
   
   1. **집계** **이벤트와 원시 이벤트**중에서 선택 합니다. 
   
       > [!NOTE]
       > Power BI 나중에 언제 든 지 데이터를 집계할 수 있지만 집계 후에는 원시 데이터로 되돌릴 수 없습니다. 
       
       > [!NOTE]
       > 원시 이벤트 수준 데이터에는 100-K 이벤트 수 제한이 있습니다.

       [연결 ![](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. **웜 저장소**에 대 한 Time Series Insights 인스턴스를 구성 하지 않은 경우 경고가 표시 됩니다.

       [![웜 저장소 경고](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Azure Portal에서 **웜 저장소** 에 대 한 기존 인스턴스를 구성할 수 있습니다.

1. **클립보드에 쿼리 복사를**선택 합니다.
1. 이제 Power BI Desktop를 시작 합니다.
1. **홈** 탭의 Power BI Desktop에서 왼쪽 위 모서리에 있는 **데이터 가져오기** 를 선택 하 고 **추가**를 선택 합니다.

    [![홈 드롭다운](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. **Time Series Insights**를 검색 하 고 **Azure Time Series Insights (베타)** 를 선택한 다음 **연결**을 선택 합니다.

    [Time Series Insights에 Power BI 연결 ![](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    또는 **Azure** 탭으로 이동 하 **Azure Time Series Insights (베타)** 를 선택한 다음 **연결**을 선택 합니다.
    
1. 타사 리소스에 연결할 수 있는 권한을 요청 하는 메시지 대화 상자가 표시 됩니다. **계속**을 선택 합니다.

    [![사용자 지정 쿼리 만들기를 선택 합니다.](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. 드롭다운 메뉴의 **데이터 원본**에서 **사용자 지정 쿼리 만들기**를 선택 합니다. 클립보드의 선택적 **사용자 지정 쿼리 (선택 사항)** 필드에 붙여넣은 다음 **확인**을 누릅니다.

    [사용자 지정 쿼리를 전달 ![고 확인을 선택 합니다.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. 이제 데이터 테이블이 로드 됩니다. **Load** 를 눌러 Power BI으로 로드 합니다.

    [테이블에서 로드 된 데이터를 검토 하 고 로드를 선택 ![](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

이러한 단계를 완료 한 경우 다음 섹션으로 건너뜁니다.

## <a name="create-a-report-with-visuals"></a>시각적 개체 관련 보고서 만들기

이제 데이터를 Power BI로 가져왔지만 시각적 개체를 사용 하 여 보고서를 작성 합니다.

1. 창의 왼쪽에서 **보고서** 뷰를 선택 했는지 확인 합니다.

    [보고서 뷰를 선택 ![](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  **시각화** 열에서 원하는 시각적 개체를 선택 합니다. 예를 들어 **꺾은선형 차트**를 선택 합니다. 그러면 캔버스에 빈 꺾은선형 차트가 추가 됩니다.
 
1.  **필드** 목록에서 **타임 스탬프** 를 선택 하 고 **축** 필드로 끌어 X 축에 항목을 표시 합니다.

1.  다시 **필드** 목록에서 **Timeseriesid** 를 선택 하 고 **값** 필드로 끌어 Y 축을 따라 항목을 표시 합니다.

    [꺾은선형 차트를 만드는 ![](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  캔버스에 다른 차트를 추가 하려면 캔버스의 꺾은선형 차트 외부에서 아무 곳 이나 클릭 하 고이 프로세스를 반복 합니다.

    [공유할 추가 차트를 만들 ![](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

보고서를 만든 후에는 Power BI Reporting Services에 게시할 수 있습니다.

## <a name="advanced-editing"></a>고급 편집

Power BI에서 데이터 집합을 이미 로드 했지만 쿼리를 수정 하려는 경우 (예: 날짜/시간 또는 환경 ID 매개 변수) Power BI의 고급 편집기 기능을 통해이 작업을 수행할 수 있습니다. 자세한 내용은 [Power BI 설명서](https://docs.microsoft.com/power-bi/desktop-query-overview) 를 참조 하세요.

개요:

1. Power BI Desktop에서 **쿼리 편집**을 선택 합니다.
1. **고급 편집기**를 누릅니다.

    [고급 편집기에서 ![편집 쿼리](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 필요에 따라 JSON 페이로드를 수정 합니다.
1. **완료** 를 선택한 다음 **닫기 &** **파워 쿼리 편집기 창**에서 적용 합니다.

원하는 변경 내용이 적용 된 것을 볼 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights에 대 한 [Power BI 커넥터 개념](https://docs.microsoft.com/power-bi/desktop-query-overview) 을 참조 하세요.

* [Power BI desktop](https://docs.microsoft.com/power-bi/desktop-query-overview)에 대해 자세히 알아보세요.

* [TIME SERIES INSIGHTS GA 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) 및 [Time Series Insights Preview 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)를 참조 하세요.
