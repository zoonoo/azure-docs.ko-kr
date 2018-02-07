---
title: "Azure Log Analytics 데이터를 Power BI로 가져오기 | Microsoft Docs"
description: "Power BI는 서로 다른 데이터 집합의 분석을 위해 다양한 시각화 및 보고서를 제공하는 Microsoft의 클라우드 기반 비즈니스 분석 서비스입니다.  이 문서에서는 Log Analytics 데이터를 Power BI로 가져오도록 구성하고 자동으로 새로 고침하도록 구성하는 방법을 설명합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: e687a1ee8ac4f565062e57b07cdfa9ac5e6bbf4f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Azure Log Analytics 데이터를 Power BI로 가져오기


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)는 서로 다른 데이터 집합의 분석을 위해 다양한 시각화 및 보고서를 제공하는 Microsoft의 클라우드 기반 비즈니스 분석 서비스입니다.  Log Analytics 로그 검색 결과를 Power BI 데이터 집합으로 가져오면 여러 소스의 데이터를 결합하고 웹 및 모바일 장치에서 보고서를 공유하는 등의 기능을 활용할 수 있습니다.

이 문서에서는 Log Analytics 데이터를 Power BI로 가져오고 자동으로 새로 고침하도록 예약하는 방법을 자세히 설명합니다.  [업그레이드된](#upgraded-workspace) 작업 영역과 [레거시](#legacy-workspace) 작업 영역에 대한 여러 프로세스가 포함되어 있습니다.

## <a name="upgraded-workspace"></a>업그레이드된 작업 영역


[업그레이드된 Log Analytics 작업 영역](log-analytics-log-search-upgrade.md)에서 Power BI로 데이터를 가져오려면 Log Analytics의 로그 검색 쿼리를 기반으로 Power BI에 데이터 집합을 만듭니다.  쿼리는 데이터 집합이 새로 고침될 때마다 실행됩니다.  데이터 집합의 데이터를 사용하는 Power BI 보고서를 작성할 수 있습니다.  Power BI에서 데이터 집합을 만들려면 Log Analytics에서 [파워 쿼리(M) 언어](https://msdn.microsoft.com/library/mt807488.aspx)로 쿼리를 내보냅니다.  그런 다음 Power BI Desktop에 쿼리를 만들고 Power BI에 데이터 집합으로 게시합니다.  이 프로세스에 대한 세부 정보는 아래에 설명되어 있습니다.

![Log Analytics에서 Power BI로](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>내보내기 쿼리
Log Analytics에서 Power BI 데이터 집합을 채우고 싶은 데이터를 반환하는 [로그 검색](log-analytics-log-search-new.md)부터 만듭니다.  그런 다음 해당 쿼리를 [파워 쿼리(M) 언어](https://msdn.microsoft.com/library/mt807488.aspx)로 내보내면 Power BI Desktop에서 사용할 수 있습니다.

1. Log Analytics에서 데이터 집합에 대한 데이터를 추출하는 로그 검색을 만듭니다.
2. 로그 검색 포털을 사용하는 경우 **Power BI**를 클릭합니다.  Analytics 포털을 사용하는 경우 **내보내기** > **Power BI 쿼리(M)**를 선택합니다.  두 옵션 모두 **PowerBIQuery.txt**라고 하는 텍스트 파일로 쿼리를 내보냅니다. 

    ![로그 검색 내보내기](media/log-analytics-powerbi/export-logsearch.png) ![로그 검색 내보내기](media/log-analytics-powerbi/export-analytics.png)

3. 텍스트 파일을 열고 내용을 복사합니다.

### <a name="import-query-into-power-bi-desktop"></a>Power BI Desktop으로 쿼리 가져오기
Power BI Desktop은 Power BI에 게시할 수 있는 데이터 집합 및 보고서를 만들 수 있는 데스크톱 응용 프로그램입니다.  Log Analytics에서 내보낸 파워 쿼리 언어를 사용하여 쿼리를 만드는 데도 사용할 수 있습니다. 

1. 아직 설치하지 않았으면 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)을 설치하고 응용 프로그램을 엽니다.
2. **데이터 가져오기** > **빈 쿼리**를 선택하여 새 쿼리를 엽니다.  그런 다음 **고급 편집기**를 선택하고 내보낸 파일의 내용을 쿼리에 붙여넣습니다. **Done**을 클릭합니다.

    ![Power BI 데스크톱 쿼리](media/log-analytics-powerbi/desktop-new-query.png)

5. 쿼리가 실행되고 결과가 표시됩니다.  Azure에 연결하려면 자격 증명이 필요할 수도 있습니다.  
6. 쿼리를 설명하는 이름을 입력합니다.  기본값은 **Query1**입니다. **닫고 적용**을 클릭하여 보고서에 데이터 집합을 추가합니다.

    ![Power BI Desktop 이름](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Power BI에 게시
Power BI에 게시할 때 데이터 집합 및 보고서가 생성됩니다.  Power BI Desktop에서 보고서를 만드는 경우 사용자의 데이터를 사용하여 다음 항목이 게시됩니다.  그렇지 않은 경우 빈 보고서가 생성됩니다.  Power BI에서 보고서를 수정할 수도 있고 데이터 집합에 따라 새로 만들 수도 있습니다.

8. 데이터를 기반으로 보고서를 만듭니다.  아직 익숙하지 않은 경우 [Power BI Desktop 설명서](https://docs.microsoft.com/power-bi/desktop-report-view)를 사용합니다.  Power BI로 보낼 준비가 완료되면 **게시**를 클릭합니다.  메시지가 표시되면 Power BI 계정에서 대상을 선택합니다.  염두에 둔 특정 대상이 없으면 **My workspace**를 사용합니다.

    ![Power BI Desktop 게시](media/log-analytics-powerbi/desktop-publish.png)

3. 게시가 완료되면 **Power BI에서 열기**를 클릭하여 새 데이터 집합으로 Power BI를 엽니다.


### <a name="configure-scheduled-refresh"></a>예약된 새로 고침 구성
Power BI에서 생성된 데이터 집합은 이전에 Power BI Desktop에서 본 것과 동일한 데이터를 갖습니다.  데이터 집합을 주기적으로 새로 고침하여 쿼리를 다시 실행하고 Log Analytics의 최신 데이터로 채워야 합니다.  

1. 보고서를 업로드한 작업 영역을 클릭하고 **데이터 집합** 메뉴를 선택합니다. 새 데이터 집합 옆에 있는 상황에 맞는 메뉴를 선택하고 **설정**을 선택합니다. **데이터 원본 자격 증명** 아래에 자격 증명이 유효하지 않다는 내용의 메시지가 표시될 것입니다.  데이터 집합이 데이터를 새로 고칠 때 사용할 자격 증명을 아직 제공하지 않았기 때문입니다.  **자격 증명 편집**을 클릭하고 Log Analytics에 액세스할 수 있는 자격 증명을 지정합니다.

    ![Power BI 일정](media/log-analytics-powerbi/powerbi-schedule.png)

5. **예약된 새로 고침** 아래에서 **데이터를 최신 상태로 유지** 옵션을 선택합니다.  필요에 따라 **새로 고침 빈도** 및 새로 고침을 실행할 하나 이상의 특정 시간을 변경할 수 있습니다.

    ![Power BI 새로 고침](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>기존 작업 영역
[레거시 Log Analytics 작업 영역](log-analytics-powerbi.md)을 사용하여 Power BI를 구성하는 경우 결과를 Power BI의 해당 데이터 집합으로 내보내는 로그 쿼리를 만듭니다.  쿼리 및 내보내기는 Log Analytics에 의해 수집된 최신 데이터로 데이터 집합을 최신 상태로 유지하도록 정의한 일정에 따라 계속해서 자동으로 실행합니다.

![Log Analytics에서 Power BI로](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Power BI 일정
*Power BI 일정*에는 Log Analytics에서 Power BI의 해당 데이터 집합으로 데이터 집합을 내보내는 로그 검색 및 데이터 집합을 최신으로 유지하기 위해 이 검색의 실행 빈도를 정의하는 일정이 포함됩니다.

데이터 집합의 필드는 로그 검색에서 반환되는 레코드의 속성과 일치합니다.  검색이 다양한 종류의 레코드를 반환하는 경우 데이터 집합은 포함된 각 레코드 형식의 모든 속성을 포함합니다.  

### <a name="connecting-log-analytics-workspace-to-power-bi"></a>Log Analytics 작업 영역을 Power BI에 연결
Log Analytics에서 Power BI로 내보내려면 다음 절차를 사용하여 Power BI 계정에 사용자 작업 영역을 연결해야 합니다.  

1. OMS 콘솔에서 **설정** 타일을 클릭합니다.
2. **계정**을 선택합니다.
3. **작업 영역 정보** 섹션에서 **Connect to Power BI Account**을 클릭합니다.
4. Power BI 계정에 대한 자격 증명을 입력합니다.

### <a name="create-a-power-bi-schedule"></a>Power BI 일정 만들기
다음 절차를 사용하여 각 데이터 집합에 대한 Power BI 일정을 만듭니다.

1. OMS 콘솔에서 **로그 검색** 타일을 클릭합니다.
2. 새 쿼리를 입력하거나 **Power BI**로 내보내려는 데이터를 반환하는 저장된 검색을 선택합니다.  
3. 페이지 맨 위에 있는 **Power BI** 단추를 클릭하여 **Power BI** 대화 상자를 엽니다.
4. 다음 테이블에 정보를 제공하고 **저장**을 클릭합니다.

| 자산 | 설명 |
|:--- |:--- |
| Name |Power BI 일정의 목록을 볼 때 일정을 식별하는 이름입니다. |
| 저장된 검색 |실행할 로그 검색입니다.  현재 쿼리를 선택하거나 드롭다운 상자에서 기존 저장된 검색을 선택합니다. |
| 일정 |저장된 검색을 실행하고 Power BI 데이터 집합으로 내보내는 빈도입니다.  값은 15분에서 24시간 사이여야 합니다. |
| 데이터 집합 이름 |Power BI의 데이터 집합 이름입니다.  존재하지 않는 경우 생성되고 존재하는 경우 업데이트됩니다. |

### <a name="viewing-and-removing-power-bi-schedules"></a>Power BI 일정 보기 및 제거
다음 절차를 사용하여 기존 Power BI 일정의 목록을 봅니다.

1. OMS 콘솔에서 **설정** 타일을 클릭합니다.
2. **Power BI**를 선택합니다.

일정의 세부 정보 외에도 지난 주에 일정이 실행된 횟수와 마지막 동기화의 상태가 표시됩니다.  동기화에 오류가 발견되면 링크를 클릭하여 오류의 세부 정보로 레코드에 대한 로그 검색을 실행할 수 있습니다.

**열 제거**의 **X**를 클릭하여 일정을 제거할 수 있습니다.  **끄기**를 선택하여 일정을 비활성화할 수 있습니다.  일정을 수정하려면 제거하고 새 설정으로 다시 만들어야 합니다.

![Power BI 일정](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>샘플 연습
다음 섹션은 Power BI 일정을 만들고 해당 데이터 집합을 사용하여 간단한 보고서를 만드는 예제를 안내합니다.  이 예제에서는 컴퓨터 집합에 대한 모든 성능 데이터가 Power BI로 내보내진 다음 프로세서 사용률을 표시하는 꺾은선형 그래프가 작성됩니다.

#### <a name="create-log-search"></a>로그 검색 만들기
데이터 집합에 보낼 데이터에 대한 로그 검색을 만들어 보겠습니다.  이 예제에서는 *srv*로 시작하는 이름의 컴퓨터에 대한 모든 성능 데이터를 반환하는 쿼리를 사용합니다.  

![Power BI 일정](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Power BI Search 만들기
**Power BI** 단추를 클릭하여 Power BI 대화 상자를 열고 필요한 정보를 제공합니다.  이 검색이 시간당 한 번씩 실행되길 원하고 *Contoso Perf*라는 데이터 집합을 만듭니다.  원하는 데이터를 만드는 검색 열기가 이미 있으므로 *저장된 검색* 에 대한 **현재 검색 쿼리 사용**의 기본값을 유지합니다.

![Power BI 검색](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Power BI Search 확인
일정을 올바르게 만들었는지 확인하려면 OMS 대시보드의 **설정** 타일 아래에서 Power BI Search의 목록을 봅니다.  동기화가 실행되었음을 보고할 때까지 몇 분 정도 기다렸다가 이 보기를 새로 고칩니다.  데이터 집합을 자동으로 새로 고치도록 예약하는 것이 일반적입니다.

![Power BI 검색](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Power BI의 데이터 집합 확인
[powerbi.microsoft.com](http://powerbi.microsoft.com/) 에 계정으로 로그인하고 왼쪽 창 맨 아래의 **데이터 집합** 으로 스크롤합니다.  내보내기가 성공적으로 실행되었음을 나타내는 *Contoso Perf* 가 나열된 것을 볼 수 있습니다.

![Power BI 데이터 집합](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>데이터 집합 기반 보고서 만들기
이 데이터 집합에 포함된 필드를 보려면 **Contoso Perf** 데이터 집합을 선택한 다음 오른쪽 **필드** 창에서 **결과**를 클릭합니다.  각 컴퓨터에 대한 프로세서 사용률을 보여 주는 꺾은선형 그래프를 만들려면 다음 작업을 수행합니다.

1. 선 차트 시각화를 선택합니다.
2. **ObjectName**을 **Report level filter**로 끌어 놓고 **Processor**를 선택합니다.
3. **CounterName**을 **Report level filter**로 끌어 놓고 **% Processor Time**을 선택합니다.
4. **CounterValue**를 **Values**로 끌어 놓습니다.
5. **Computer**를 **Legend**로 끌어 놓습니다.
6. **TimeGenerated**를 **Axis**로 끌어 놓습니다.

데이터 집합의 데이터로 결과 꺾은선형 그래프가 표시된 것을 확인할 수 있습니다.

![Power BI 꺾은선형 그래프](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>보고서 저장
화면 맨 위에 있는 저장 단추를 클릭하여 보고서를 저장하고 왼쪽 창의 보고서 섹션에 나열된 것을 검사합니다.

![Power BI 보고서](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>다음 단계
* Power BI로 내보낼 수 있는 쿼리를 작성하려면 [로그 검색](log-analytics-log-searches.md) 에 대해 알아봅니다.
* Log Analytics 내보내기를 기준으로 시각화를 작성하려면 [Power BI](http://powerbi.microsoft.com) 에 대해 자세히 알아보세요.
