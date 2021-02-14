---
title: Power BI-Azure Time Series Insights에 환경 연결 Microsoft Docs
description: Azure Time Series Insights를 Power BI에 연결 하 여 조직 전체에서 데이터를 공유 하 고, 차트를 표시 하 고, 표시 하는 방법을 알아봅니다.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374170"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Power BI의 Azure Time Series Insights에서 데이터 시각화

Azure Time Series Insights는 클라우드에서 시계열 데이터를 저장, 관리, 쿼리 및 시각화 하는 데 사용할 수 있는 플랫폼입니다. [Power BI](https://powerbi.microsoft.com) 는 조직 전체에서 정보 및 결과를 공유할 수 있도록 하는 풍부한 시각화 기능을 제공 하는 비즈니스 분석 도구입니다. 이제 두 서비스를 통합 하 여 강력한 데이터 시각화 및 Power BI의 손쉬운 공유 기능으로 Azure Time Series Insights의 강력한 분석을 강화할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

* 기본 Azure Time Series Insights 커넥터를 사용 하 여 Power BI에 Azure Time Series Insights 연결
* Power BI에서 시계열 데이터를 사용 하 여 시각적 개체 만들기
* Power BI에 보고서를 게시 하 고 나머지 조직과 공유


## <a name="prerequisites"></a>사전 요구 사항

* 아직 Azure 구독이 없는 경우 [평가판 Azure 구독](https://azure.microsoft.com/free/)에 등록합니다.
* 최신 버전의 [Power BI Desktop](https://powerbi.microsoft.com/downloads/) 다운로드 및 설치
* [Azure Time Series Insights Gen2 환경을](./how-to-provision-manage.md) 만들거나 만듭니다.

[환경 액세스 정책을](./concepts-access-policies.md) 검토 하 고 Azure Time Series Insights Gen2 환경에 대 한 직접 액세스 또는 게스트 액세스 권한이 있는지 확인 하세요. 

> [!IMPORTANT]
> * 최신 버전의 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)를 다운로드 하 여 설치 합니다. 이 문서의 단계를 수행 하려면 Power BI Desktop의 12 월 2020 (2.88.321.0) 버전이 설치 되어 있는지 확인 하세요. 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Azure Time Series Insights에서 Power BI 데스크톱으로 데이터 내보내기

시작하기:

1. Azure Time Series Insights Gen2 탐색기를 열고 데이터를 만들어 둡니다. Power BI로 내보낼 데이터입니다.
1. 만족 하는 보기를 만들었으면 **추가 작업** 드롭다운 메뉴로 이동 하 여 **Power BI에 연결** 을 선택 합니다.

    [![Azure Time Series Insights Gen2 Explorer 내보내기](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. 내보내기에 대 한 매개 변수를 설정 합니다.

   * **데이터 형식**: **집계 데이터** 또는 **원시 이벤트** 를 Power BI로 내보낼지 여부를 선택 합니다. 

       > [!NOTE]
       > 원시 이벤트를 내보내는 경우 나중에 Power BI에서 해당 데이터를 집계할 수 있습니다. 그러나 집계 데이터를 내보내는 경우에는 Power BI 원시 데이터로 되돌릴 수 없습니다. 원시 이벤트 수준 데이터에는 25만 이벤트 수 제한이 있습니다.

   * **시간 범위**: **고정** 된 시간 범위 또는 Power BI의 **최신** 데이터를 볼 지 여부를 선택 합니다. 고정 된 시간 범위를 선택 하면 차트에 사용 되는 검색 범위의 데이터가 Power BI로 내보내집니다. 최근 시간 범위를 선택 하면 Power BI에서 사용자가 선택한 검색 범위의 최신 데이터를 가져옵니다. 예를 들어 1 시간 분량의 데이터를 차트로 작성 하 고 "최신" 설정을 선택 하는 경우 커넥터 Power BI는 항상 최근 1 시간 분량의 데이터를 쿼리 합니다.)
  
   * **저장소 유형**: **웜 스토어** 또는 **콜드 스토어** 에 대해 선택한 쿼리를 실행할지 여부를 선택 합니다. 콜드 및 웜 저장소 둘 다에 걸쳐 있는 범위를 선택 하면 웜 저장소에 최신 데이터만 포함 되기 때문에 기본적으로 쿼리는 콜드 저장소로 라우팅됩니다. 수동으로 storeType 매개 변수를 변경 하는 것은 허용 되지만 최상의 환경에서는 권장 되지 않습니다. 

    > [!TIP] 
    > Azure Time Series Insights 탐색기는 내보내기 위해 선택한 데이터의 검색 범위 및 뷰에 따라 권장 매개 변수를 자동으로 선택 합니다. 

1. 설정을 구성 했으면 **클립보드에 쿼리 복사** 를 선택 합니다.

    [![Azure Time Series Insights 탐색기 내보내기 모달](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Power BI Desktop을 시작합니다.
   
1. **홈** 탭의 Power BI Desktop에서 왼쪽 위 모서리에 있는 **데이터 가져오기** 를 선택 하 고 **추가** 를 선택 합니다.

    [![Power BI에서 데이터 가져오기](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. **Azure Time Series Insights** 를 검색 하 고 **Azure Time Series Insights (베타)** 를 선택한 다음 **연결** 을 선택 합니다.

    [![Azure Time Series Insights에 Power BI 연결](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    또는 **Azure** 탭으로 이동 하 **Azure Time Series Insights (베타)** 를 선택한 다음 **연결** 을 선택 합니다.

1. Azure Time Series Insights 탐색기에서 복사한 쿼리를 **사용자 지정 쿼리** 필드에 붙여넣은 다음 **확인** 을 누릅니다.

    [![사용자 지정 쿼리를 붙여넣고 확인을 선택 합니다.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  이제 데이터 테이블이 로드 됩니다. **Load** 를 눌러 Power BI으로 로드 합니다. 데이터에 대 한 변환을 수행 하려는 경우 **데이터 변환** 을 클릭 하 여 지금 작업을 수행할 수 있습니다. 데이터를 로드 한 후 변형할 수도 있습니다.

    [![테이블의 데이터를 검토 하 고 로드를 선택 합니다.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>시각적 개체 관련 보고서 만들기

이제 데이터를 Power BI로 가져왔지만 시각적 개체를 사용 하 여 보고서를 작성 합니다.

1. 창의 왼쪽에서 **보고서** 뷰를 선택 했는지 확인 합니다.

    [![보고서 뷰 아이콘을 보여 주는 스크린샷](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. **시각화** 열에서 원하는 시각적 개체를 선택 합니다. 예를 들어 **꺾은선형 차트** 를 선택 합니다. 그러면 캔버스에 빈 꺾은선형 차트가 추가 됩니다.

1.  **필드** 목록에서 **_Timestamp** 을 선택 하 고 **축** 필드로 끌어 X 축을 따라 시간을 표시 합니다. **축** 에 대 한 값으로 **_Timestamp** 로 전환 (기본값은 **날짜 계층 구조**) 해야 합니다.

    [![_Timestamp 선택](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  그런 다음, **필드** 목록에서 그리려는 변수를 선택 하 고 **값** 필드로 끌어 Y 축을 따라 값을 표시 합니다. 시계열 ID 값을 선택 하 고 **범례** 필드로 끌어 차트에서 시계열 id 당 하나씩 여러 줄을 만듭니다. 그러면 Azure Time Series Insights 탐색기에서 제공 되는 것과 비슷한 뷰가 렌더링 됩니다. 

    [![기본 꺾은선형 차트 만들기](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. 캔버스에 다른 차트를 추가 하려면 캔버스에서 꺾은선형 차트 외부의 아무 곳 이나 선택 하 고이 프로세스를 반복 합니다.

    [![공유할 추가 차트 만들기](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

보고서를 만든 후에는 Power BI Reporting Services에 게시 하 여 조직의 다른 사용자와 공유할 수 있습니다.

## <a name="advanced-editing"></a>고급 편집
Power BI에서 데이터 집합을 이미 로드 했지만 쿼리를 수정 하려는 경우 (예: 날짜/시간 또는 환경 ID 매개 변수) Power BI의 고급 편집기 기능을 통해이 작업을 수행할 수 있습니다. **파워 쿼리 편집기** 를 사용 하 여 변경 하는 방법에 대 한 자세한 내용은 [Power BI 설명서](/power-bi/desktop-query-overview) 를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* [Power BI desktop](/power-bi/desktop-query-overview)에 대해 자세히 알아보세요.

* Azure Time Series Insights Gen2에서 [데이터를 쿼리](concepts-query-overview.md) 하는 방법에 대해 알아봅니다.