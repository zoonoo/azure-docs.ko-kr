---
title: 환경을 Power BI - Azure Time Series Insights에 연결합니다 | Microsoft Docs
description: Azure Time Series Insights를 Power BI에 연결하여 조직 전체에서 데이터를 공유하고, 차트로 정리하며, 표시하는 방법을 알아봅니다.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374170"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Power BI에서 Azure Time Series Insights의 데이터 시각화

Azure Time Series Insights는 클라우드의 시계열 데이터를 저장, 관리, 쿼리하고 시각화하는 데 사용할 수 있는 플랫폼입니다. [Power BI](https://powerbi.microsoft.com)는 조직 전체에서 정보 및 결과를 공유할 수 있도록 풍부한 시각화 기능을 제공하는 비즈니스 분석 도구입니다. 이제 두 서비스를 통합하여 Azure Time Series Insights의 강력한 분석 능력을 Power BI의 강력한 데이터 시각화 및 손쉬운 공유 기능으로 확장할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

* 기본 Azure Time Series Insights 커넥터를 사용하여 Azure Time Series Insights를 Power BI에 연결
* Power BI에서 시계열 데이터를 사용하여 시각적 개체 만들기
* Power BI에 보고서를 게시하고 다른 조직원들과 공유


## <a name="prerequisites"></a>사전 요구 사항

* 아직 Azure 구독이 없는 경우 [평가판 Azure 구독](https://azure.microsoft.com/free/)에 등록합니다.
* [Power BI Desktop](https://powerbi.microsoft.com/downloads/)의 최신 버전을 다운로드 및 설치합니다.
* [Azure Time Series Insights Gen2 환경](./how-to-provision-manage.md)을 보유하거나 새로 만듭니다.

[환경 액세스 정책](./concepts-access-policies.md)을 검토하고 Azure Time Series Insights Gen2 환경에 대한 직접 액세스 또는 게스트 액세스 권한이 있는지 확인하세요. 

> [!IMPORTANT]
> * [Power BI Desktop](https://powerbi.microsoft.com/downloads/)의 최신 버전을 다운로드 및 설치합니다. 이 문서의 단계를 수행하려면 Power BI Desktop의 2020년 12월(2.88.321.0) 버전이 설치되어 있는지 확인하세요. 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Azure Time Series Insights에서 Power BI 데스크톱으로 데이터 내보내기

시작하기:

1. Azure Time Series Insights Gen2 탐색기를 열고 데이터를 선택합니다. 이 데이터가 Power BI로 내보낼 데이터입니다.
1. 원하는 보기를 만들었으면 **추가 작업** 드롭다운 메뉴로 이동하여 **Power BI에 연결** 을 선택합니다.

    [![Azure Time Series Insights Gen2 탐색기 내보내기](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. 내보내기에 관한 매개 변수를 설정합니다.

   * **데이터 형식**: **집계 데이터** 또는 **원시 이벤트** 를 Power BI로 내보낼지 선택합니다. 

       > [!NOTE]
       > 원시 이벤트를 내보내는 경우 나중에 Power BI에서 해당 데이터를 집계할 수 있습니다. 그러나 집계 데이터를 내보내는 경우에는 Power BI에서 원시 데이터로 되돌릴 수 없습니다. 원시 이벤트 레벨 데이터에는 이벤트 수가 최대 25만 건으로 제한됩니다.

   * **시간 범위**: Power BI에서 **고정된** 시간 범위 또는 **최신** 데이터를 볼지 선택합니다. 고정된 시간 범위를 선택하면 차트에 사용된 검색 범위의 데이터가 Power BI로 보내집니다. 최근 시간 범위를 선택하면 Power BI에서 사용자가 선택한 검색 범위의 최신 데이터를 가져옵니다. 예를 들어 1시간 분량의 데이터를 차트로 작성하고 “최신” 설정을 선택하는 경우 Power BI 커넥터는 항상 최근 1시간 분량의 데이터를 쿼리합니다.
  
   * **저장소 유형**: 선택한 쿼리를 **웜 저장소** 또는 **콜드 저장소** 중 어떤 저장소로 실행할지 여부를 선택합니다. 콜드 및 웜 저장소 양쪽 모두에 걸쳐 있는 범위를 선택하면 웜 저장소에는 최신 데이터만 포함되기 때문에 기본적으로 쿼리는 콜드 저장소로 라우팅됩니다. 수동으로 storeType 매개 변수 변경은 가능하지만, 최상의 사용 경험을 위해서는 권장되지 않습니다. 

    > [!TIP] 
    > Azure Time Series Insights 탐색기는 내보내기 위해 선택한 데이터의 검색 범위 및 보기에 따라 권장 매개 변수를 자동으로 선택합니다. 

1. 설정을 구성했으면 **클립보드에 쿼리 복사** 를 선택합니다.

    [![Azure Time Series Insights 탐색기 내보내기](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Power BI Desktop을 시작합니다.
   
1. Power BI Desktop의 **홈** 탭에서 왼쪽 상단 구석의 **데이터 가져오기**, **자세히** 를 차례로 선택합니다.

    [![Power BI에서 데이터 가져오기](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. **Azure Time Series Insights** 를 검색하고 **Azure Time Series Insights(베타)** 를 선택한 다음 **연결** 을 선택합니다.

    [![Power BI를 Azure Time Series Insights에 연결](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    또는 **Azure** 탭으로 이동하여 **Azure Time Series Insights(베타)** 를 선택한 다음 **연결** 을 선택합니다.

1. Azure Time Series Insights 탐색기에서 복사한 쿼리를 **사용자 지정 쿼리** 필드에 붙여넣은 다음 **확인** 을 누릅니다.

    [![사용자 지정 쿼리를 붙여넣고 확인을 선택합니다.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  이제 데이터 테이블이 로드됩니다. **로드** 를 눌러 Power BI로 로드합니다. 데이터를 변환하려면 **데이터 변환** 을 클릭하여 작업할 수 있습니다. 데이터를 로드한 후에 변환할 수도 있습니다.

    [![테이블의 데이터를 검토하고 로드를 선택합니다.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>시각적 개체 관련 보고서 만들기

이제 데이터를 Power BI로 가져왔으므로, 시각적 개체를 사용하여 보고서를 빌드합니다.

1. 창의 왼쪽에서 **보고서** 보기를 선택했는지 확인합니다.

    [![보고서 보기 아이콘의 스크린샷](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. **시각화** 열에서 원하는 시각적 개체를 선택합니다. 예를 들어, 여기서는 **꺾은선형 차트** 를 선택합니다. 그러면 캔버스에 빈 꺾은선형 차트가 추가됩니다.

1.  **필드** 목록에서 **_Timestamp** 를 선택하고 **축** 필드로 드래그해 X축을 따라 시간을 표시합니다. **축** 의 값에 따라 **_Timestamp** 로 전환(기본값은 **날짜 계층 구조**)해야 합니다.

    [![_Timestamp 선택](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  다시 한 번, **필드** 목록에서 그리려는 변수를 선택하고 **값** 필드로 드래그해 Y축을 따라 값을 표시합니다. 시계열 ID 값을 선택하고 **범례** 필드로 드래그해 차트에서 시계열 ID당 하나씩 여러 줄을 만듭니다. 그러면 Azure Time Series Insights 탐색기에서 제공되는 것과 비슷한 뷰가 렌더링됩니다. 

    [![기본 꺾은선형 차트 만들기](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. 캔버스에 다른 차트를 추가하려면 캔버스에서 꺾은선형 차트 외부의 아무 곳이나 선택하여 이 프로세스를 반복합니다.

    [![공유할 추가 차트 만들기](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

보고서를 만든 후에는 Power BI Reporting Services에 게시하여 조직의 다른 사용자와 공유할 수 있습니다.

## <a name="advanced-editing"></a>고급 편집
Power BI에서 데이터 세트를 이미 로드했지만 쿼리를 수정하려는 경우(예: 날짜/시간 또는 환경 ID 매개 변수) Power BI의 고급 편집기 기능을 통해 이 작업을 할 수 있습니다. **Power Query 편집기** 를 사용하여 변경하는 방법에 대한 자세한 내용은 [Power BI 설명서](/power-bi/desktop-query-overview)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

* [Power BI 데스크톱](/power-bi/desktop-query-overview)에 대한 자세한 정보를 알아봅니다.

* Azure Time Series Insights Gen2에서 [데이터를 쿼리](concepts-query-overview.md)하는 방법에 대해 알아봅니다.