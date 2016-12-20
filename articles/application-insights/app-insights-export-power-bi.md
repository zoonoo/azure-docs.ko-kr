---
title: "Application Insights에서 Power BI로 내보내기 | Microsoft Docs"
description: "분석 쿼리를 Power BI에서 표시할 수 있습니다."
services: application-insights
documentationcenter: 
author: noamben
manager: douge
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7a9c40081f52b2ffe918f4612f790f7fd08acc5a
ms.openlocfilehash: dba7f1ac2257246f14acf79de6e9fc714689b276


---
# <a name="feed-power-bi-from-application-insights"></a>Application Insights에서 Power BI 공급
[Power BI](http://www.powerbi.com/)는 데이터를 분석하는 데 도움을 주고 통찰력을 공유하는 비즈니스 분석 도구 제품군입니다. 모든 장치에서 풍부한 대시보드를 사용할 수 있습니다. [Azure Application Insights](app-insights-overview.md)의 Analytics 쿼리를 포함하여 다양한 원본의 데이터를 포함할 수 있습니다.

Power BI에 Application Insights 데이터를 내보내는 세 가지 권장 방법이 있습니다. 개별적으로 또는 함께 사용할 수 있습니다.

* [**Power BI 어댑터**](#power-pi-adapter) - 앱에서 원격 분석의 전체 대시보드를 설정 합니다. 일련의 차트가 미리 정의되어 있으나, 다른 원본에서 직접 쿼리를 추가할 수 있습니다.
* [**Analytics 쿼리 내보내기**](#export-analytics-queries) - Analytics를 사용하여 원하는 쿼리를 작성하고 Power BI로 내보냅니다. 이 쿼리를 다른 데이터와 함께 대시보드에 배치할 수 있습니다.
* [**연속 내보내기 및 Stream Analytics**](app-insights-export-stream-analytics.md) - 여기에서는 추가 작업을 설정해야 합니다. 데이터를 오랜 기간 유지하려는 경우에 유용합니다. 그렇지 않은 경우에는 다른 방법이 권장됩니다.

## <a name="power-bi-adapter"></a>Power BI 어댑터
이 방법은 원격 분석의 전체 대시보드를 만듭니다. 초기 데이터 집합은 미리 정의되어 있으나, 더 많은 데이터를 추가할 수 있습니다.

### <a name="get-the-adapter"></a>어댑터 가져오기
1. [Power BI](https://app.powerbi.com/)에 로그인합니다.
2. **데이터 가져오기**, **Services**, **Application Insights**를 엽니다.
   
    ![Application Insights 데이터 원본에서 가져오기](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Application Insights 리소스의 세부 정보를 제공합니다.
   
    ![Application Insights 데이터 원본에서 가져오기](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. 데이터를 가져오는 데 1, 2분 정도 기다립니다.
   
    ![Power BI 어댑터](./media/app-insights-export-power-bi/010.png)

Application Insights 차트를 다른 원본의 차트 및 Analytics 쿼리와 결합하여 대시보드를 편집할 수 있습니다. 추가 차트를 가져올 수 있는 시각화 갤러리가 있으며, 각 차트에는 설정할 수 있는 매개 변수가 있습니다.

초기 가져오기 후에는 대시보드와 보고서가 지속적으로 매일 업데이트됩니다. 데이터 집합에 대한 새로 고침 일정을 제어할 수 있습니다.

## <a name="export-analytics-queries"></a>Analytics 쿼리 내보내기
이 경로를 사용하면 원하는 모든 Analytics 쿼리를 작성한 다음, Power BI 대시보드로 내보낼 수 있습니다. (어댑터에서 만든 대시보드를 추가할 수 있습니다.)

### <a name="one-time-install-power-bi-desktop"></a>한 번: Power BI Desktop을 설치합니다.
Application Insights 쿼리를 가져오려면 데스크톱 버전의 Power BI를 사용합니다. 하지만 또 웹이나 사용자의 Power BI 클라우드 작업 영역으로 게시할 수 있습니다. 

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)을 설치합니다.

### <a name="export-an-analytics-query"></a>Analytics 쿼리 내보내기
1. [Analytics 열기 및 쿼리 작성](app-insights-analytics-tour.md).
2. 결과에 만족할 때까지 쿼리를 테스트하고 수정합니다.

   **내보내기 전에 Analytics에서 쿼리가 제대로 실행되는지 확인합니다.**
3. **내보내기** 메뉴에서 **Power BI(M)**를 선택합니다. 텍스트 파일을 저장합니다.
   
    ![Power BI 쿼리 내보내기](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Power BI Desktop에서 **데이터 가져오기, 빈 쿼리**를 선택한 다음, 쿼리 편집기에서 **보기** 아래에 있는 **고급 쿼리 편집기**를 선택합니다.

    내보낸 M 언어 스크립트를 고급 쿼리 편집기에 복사합니다.

    ![고급 쿼리 편집기](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Power BI가 Azure에 액세스할 수 있도록 자격 증명을 제공해야 할 수 있습니다. '조직 계정'을 사용하여 Microsoft 계정에 로그인합니다.
   
    ![Application Insights 쿼리를 실행하려면 Power BI를 사용하도록 설정하는 Azure 자격 증명을 제공합니다.](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (자격 증명을 확인해야 하는 경우 쿼리 편집기에서 데이터 원본 설정 메뉴 명령을 사용합니다. Azure에 사용할 자격 증명은 Power BI용 자격 증명과 다를 수 있습니다.)
2. 쿼리에 대한 시각화를 선택하고 x축, y축 및 차원 분할에 대한 필드를 선택합니다.
   
    ![시각화 선택](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Power BI 클라우드 작업 영역에 보고서를 게시합니다. 여기에서 다른 웹 페이지에 동기화된 버전을 포함할 수 있습니다.
   
    ![시각화 선택](./media/app-insights-export-power-bi/publish-power-bi.png)
4. 간격을 두고 보고서를 수동으로 새로 고치거나 옵션 페이지에서 예약된 새로 고침을 설정합니다.

## <a name="about-sampling"></a>샘플링 정보
응용 프로그램에서 많은 양의 데이터를 전송하는 경우 적응 샘플링 기능이 작동하여 원격 분석의 백분율만 보낼 수도 있습니다. 이는 SDK 또는 수집에서 샘플링을 수동으로 설정한 경우에도 마찬가지입니다. [샘플링에 대해 자세히 알아봅니다.](app-insights-sampling.md)

## <a name="next-steps"></a>다음 단계
* [Power BI - 알아보기](http://www.powerbi.com/learning/)
* [Analytics 자습서](app-insights-analytics-tour.md)




<!--HONumber=Nov16_HO3-->


