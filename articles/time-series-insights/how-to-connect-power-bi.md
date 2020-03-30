---
title: 환경을 Power BI - Azure 타임시리즈 인사이트로 연결 | 마이크로 소프트 문서
description: Azure Time Series Insights를 Power BI에 연결하여 조직 전체에서 데이터를 공유, 차트 및 표시하는 방법을 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863845"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Power BI에서 타임시리즈 인사이트로 데이터 시각화

Azure Time Series Insights는 클라우드에서 타임시리즈 데이터를 저장, 관리, 쿼리 및 시각화하기 위한 플랫폼입니다. [Power BI는](https://powerbi.microsoft.com) 조직 전체에서 통찰력과 결과를 공유할 수 있는 풍부한 시각화 기능을 갖춘 비즈니스 분석 도구입니다. 이제 두 서비스를 통합하여 타임시리즈 인사이트 고유의 시각화 기능과 Power BI의 기능을 모두 최대한 사용할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

* 클라우드 커넥터를 사용하여 타임시리즈 인사이트를 Power BI에 연결
* Power BI에서 데이터로 시각적 개체 만들기
* Power BI에 보고서를 게시하고 조직의 나머지 부분과 공유

마지막으로 Azure Time Series Insights를 통해 타임시리즈 데이터를 시각화하고 Power BI의 강력한 데이터 시각화 및 간편한 공유 기능을 통해 이를 향상시키는 방법을 배웁니다.

아직 없는 경우 [무료 Azure 구독에](https://azure.microsoft.com/free/) 등록해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 최신 버전의 Power [BI 데스크톱](https://powerbi.microsoft.com/downloads/) 다운로드 및 설치
* [Azure 서열 인사이트 미리 보기 인스턴스](time-series-insights-update-how-to-manage.md) 를 갖거나 만들기

> [!IMPORTANT]
> Power BI 커넥터는 현재 **웜 스토어용으로**구성된 Time Series Insights 미리 보기 *종량제* 환경에서 지원됩니다.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>타임시리즈 인사이트부터 파워 BI로 데이터 연결

타임 시리즈 인사이트 환경을 Power BI에 연결하려면 다음 단계를 따르십시오.

1. 오픈 타임 시리즈 인사이트 탐색기
1. 데이터를 쿼리 또는 원시 데이터로 내보내기
1. 개방형 Power BI 데스크톱
1. 사용자 지정 쿼리에서 로드

### <a name="export-data-into-power-bi-desktop"></a>데이터를 Power BI 데스크톱으로 내보내기

시작하기:

1. 타임시리즈 인사이트 미리보기 탐색기를 열고 데이터를 큐레이트합니다.
1. 만족하는 뷰를 만든 후에는 **더 많은 작업** 드롭다운 메뉴로 이동하여 Power **BI에 연결됨을**선택합니다.

    [![타임시리즈 인사이트 미리 보기 탐색기 내보내기](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. 이 탭 에서 매개 변수를 설정합니다.

   1. 볼 상대 기간을 지정합니다. 기존 보기에 만족하는 경우 **기존 기간으로**둡니다.
   
   1. 집계된 **이벤트와 원시 이벤트**중에서 **선택합니다.** 
   
       > [!NOTE]
       > Power BI에서 나중에 데이터를 언제든지 집계할 수 있지만 집계 후 원시 데이터로 되돌릴 수는 없습니다. 
       
       > [!NOTE]
       > Raw 이벤트 수준 데이터에 대한 100K 이벤트 수 제한이 있습니다.

       [![연결](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. **웜 스토어에**대한 타임시리즈 인사이트 인스턴스를 구성하지 않은 경우 경고가 표시됩니다.

       [![따뜻한 매장 경고](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Azure 포털에서 웜 **스토어에** 대한 기존 인스턴스를 구성할 수 있습니다.

1. **클립보드에 쿼리 복사를**선택합니다.
1. 이제 Power BI 데스크톱을 시작합니다.
1. **홈** 탭의 Power BI 데스크톱에서 왼쪽 상단 모서리에서 **데이터 가져옵니다를** 선택한 다음 **자세히 보기**를 선택합니다.

    [![홈 드롭다운](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. **열렬 인사이트를**검색하고 **Azure 열렬 인사이트(베타)를**선택한 다음 **연결합니다.**

    [![파워 BI를 타임시리즈 인사이트에 연결](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    또는 **Azure** 탭으로 이동하여 **Azure Time Series Insights(베타)를**선택한 다음 **연결**합니다.
    
1. 타사 리소스에 연결할 수 있는 권한을 요청하는 메시지 대화 상자가 표시됩니다. **계속을 선택합니다.**

    [![사용자 지정 쿼리 만들기 선택](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. **데이터 원본**아래의 드롭다운 메뉴에서 사용자 지정 **쿼리 만들기를**선택합니다. 아래의 선택적 사용자 지정 **쿼리(선택 사항)** 필드에 클립보드에서 붙여넣은 다음 **확인을**누릅니다.

    [![사용자 지정 쿼리를 전달하고 확인을 선택합니다.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. 이제 데이터 테이블이 로드됩니다. **부하를** 눌러 Power BI에 로드합니다.

    [![테이블에 로드된 데이터를 검토하고 로드를 선택합니다.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

이 단계를 완료한 경우 다음 섹션으로 건너뜁니다.

## <a name="create-a-report-with-visuals"></a>시각적 개체 관련 보고서 만들기

이제 데이터를 Power BI로 가져왔으니 이제 시각적 개체가 있는 보고서를 작성할 차례입니다.

1. 창 의 왼쪽에 보고서 **보기를** 선택 했는지 확인 합니다.

    [![보고서 보기 선택](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  **시각화** 열에서 선택한 시각적 개체를 선택합니다. 예를 들어 **선도 차트를**선택합니다. 이렇게 하면 캔버스에 빈 줄 차트가 추가됩니다.
 
1.  **필드** 목록에서 **타임스탬프를** 선택하고 **축** 필드로 드래그하여 X축을 따라 항목을 표시합니다.

1.  필드 **목록에서** **TimeSeriesId를** 선택하고 **값** 필드로 드래그하여 Y축을 따라 항목을 표시합니다.

    [![꺾은선형 차트 만들기](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  캔버스에 다른 차트를 추가하려면 표선 차트 외부의 캔버스의 아무 곳이나 선택하고 이 프로세스를 반복합니다.

    [![공유할 추가 차트 만들기](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

보고서를 만든 후에는 Power BI 보고 서비스에 게시할 수 있습니다.

## <a name="advanced-editing"></a>고급 편집

Power BI에서 데이터 집합을 이미 로드했지만 쿼리를 수정하려는 경우(예: 날짜/시간 또는 환경 ID 매개 변수) Power BI의 고급 편집기 기능을 통해 이 작업을 수행할 수 있습니다. 자세한 내용은 [Power BI 설명서를](https://docs.microsoft.com/power-bi/desktop-query-overview) 참조하십시오.

개요:

1. Power BI 데스크톱에서 **쿼리 편집을 선택합니다.**
1. **고급 편집기**를 누릅니다.

    [![고급 편집기에서 쿼리 편집](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 원하는 대로 JSON 페이로드를 수정합니다.
1. **완료를** 선택한 다음 전원 **쿼리 편집기 창**내에서 **적용& 닫기.**

이제 인터페이스에 적용한 원하는 변경 내용이 반영됩니다.  

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights에 대한 [Power BI 커넥터 개념에](https://docs.microsoft.com/power-bi/desktop-query-overview) 대해 읽어보십시오.

* [Power BI 데스크톱에](https://docs.microsoft.com/power-bi/desktop-query-overview)대해 자세히 알아보십시오.

* [읽기 타임 시리즈 인사이트 GA 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) 및 타임 시리즈 [인사이트 미리 보기 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
