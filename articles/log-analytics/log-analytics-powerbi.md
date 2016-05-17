<properties
   pageTitle="Log Analytics 데이터를 Power BI로 내보내기 | Microsoft Azure"
   description="Power BI는 서로 다른 데이터 집합의 분석을 위해 다양한 시각화 및 보고서를 제공하는 Microsoft의 클라우드 기반 비즈니스 분석 서비스입니다. Log Analytics는 시각화 및 분석 도구를 활용할 수 있도록 OMS 리포지토리에서 Power BI로 데이터를 지속적으로 내보낼 수 있습니다. 이 문서에서는 Power BI에 일정한 간격으로 자동으로 내보내는 Log Analytics에서 쿼리를 구성하는 방법을 설명합니다."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# Log Analytics 데이터를 Power BI로 내보내기

[Power BI](https://powerbi.microsoft.com/ko-KR/documentation/powerbi-service-get-started/)는 서로 다른 데이터 집합의 분석을 위해 다양한 시각화 및 보고서를 제공하는 Microsoft의 클라우드 기반 비즈니스 분석 서비스입니다. Log Analytics는 시각화 및 분석 도구를 활용할 수 있도록 OMS 리포지토리에서 Power BI로 데이터를 자동으로 내보낼 수 있습니다.

Log Analytics로 Power BI를 구성할 때 해당 결과를 Power BI의 해당 데이터 집합으로 내보내는 로그 쿼리를 만듭니다. 쿼리 및 내보내기는 Log Analytics에 의해 수집된 최신 데이터로 데이터 집합을 최신 상태로 유지하도록 정의한 일정에 따라 계속해서 자동으로 실행합니다.

![Log Analytics에서 Power BI로](media/log-analytics-power-bi/overview.png)

## Power BI 일정

*Power BI 일정*에는 OMS 리포지토리에서 Power BI의 해당 데이터 집합으로 데이터 집합을 내보내는 로그 검색 및 데이터 집합을 최신으로 유지하는 이 검색 실행 빈도를 정의하는 일정이 포함됩니다.

데이터 집합의 필드는 로그 검색에서 반환되는 레코드의 속성과 일치합니다. 검색이 다양한 종류의 레코드를 반환하는 경우 데이터 집합은 포함된 각 레코드 형식의 모든 속성을 포함합니다.

[Measure](log-analytics-search-reference.md#measure)와 같은 명령을 사용하여 모든 통합을 수행하는 것과 달리 원시 데이터를 반환하는 로그 검색 쿼리를 사용하는 것이 가장 좋은 방법입니다. 원시 데이터에서 Power BI의 모든 집계와 계산을 수행할 수 있습니다.

## OMS 작업 영역을 Power BI에 연결

Log Analytics에서 Power BI로 내보내려면 다음 절차를 사용하여 Power BI 계정에 OMS 작업 영역을 연결해야 합니다.

1. OMS 콘솔에서 **설정** 타일을 클릭합니다.
2. **계정**을 선택합니다.
3. **작업 영역 정보** 섹션에서 **Power BI 계정에 연결**을 클릭합니다.
4. Power BI 계정에 대한 자격 증명을 입력합니다.

## Power BI 일정 만들기

다음 절차를 사용하여 각 데이터 집합에 대한 Power BI 일정을 만듭니다.

1. OMS 콘솔에서 **로그 검색** 타일을 클릭합니다.
2. 새 쿼리를 입력하거나 **Power BI**로 내보내려는 데이터를 반환하는 저장된 검색을 선택합니다.  
3. 페이지 맨 위에 있는 **Power BI** 단추를 클릭하여 **Power BI** 대화 상자를 엽니다.
4. 다음 테이블에 정보를 제공하고 **저장**을 클릭합니다.

| 속성 | 설명 |
|:--|:--|
| 이름 | Power BI 일정의 목록을 볼 때 일정을 식별하는 이름입니다. |
| 저장된 검색 | 실행할 로그 검색입니다. 현재 쿼리를 선택하거나 드롭다운 상자에서 기존 저장된 검색을 선택합니다. |
| 일정 | 저장된 검색을 실행하고 Power BI 데이터 집합으로 내보내는 빈도입니다. 값은 15분에서 24시간 사이여야 합니다. |
| 데이터 집합 이름 | Power BI의 데이터 집합 이름입니다. 존재하지 않는 경우 생성되고 존재하는 경우 업데이트됩니다. |

## Power BI 일정 보기 및 제거

다음 절차를 사용하여 기존 Power BI 일정의 목록을 봅니다.

1. OMS 콘솔에서 **설정** 타일을 클릭합니다.
2. **Power BI**를 선택합니다.

일정의 세부 정보 외에도 지난 주에 일정이 실행된 횟수와 마지막 동기화의 상태가 표시됩니다. 동기화에 오류가 발견되면 링크를 클릭하여 오류의 세부 정보로 레코드에 대한 로그 검색을 실행할 수 있습니다.

**열 제거**의 **X**를 클릭하여 일정을 제거할 수 있습니다. **끄기**를 선택하여 일정을 비활성화할 수 있습니다. 일정을 수정하려면 제거하고 새 설정으로 다시 만들어야 합니다.

![Power BI 일정](media/log-analytics-power-bi/schedules.png)

## 샘플 연습
다음 섹션은 Power BI 일정을 만들고 해당 데이터 집합을 사용하여 간단한 보고서를 만드는 예제를 안내합니다. 이 예제에서는 컴퓨터 집합에 대한 모든 성능 데이터가 Power BI로 내보내진 다음 프로세서 사용률을 표시하는 꺾은선형 그래프가 작성됩니다.

### 로그 검색 만들기
데이터 집합에 보낼 데이터에 대한 로그 검색을 만들어 보겠습니다. 이 예제에서는 *srv*로 시작하는 이름의 컴퓨터에 대한 모든 성능 데이터를 반환하는 쿼리를 사용합니다.

![Power BI 일정](media/log-analytics-power-bi/walkthrough-query.png)

### Power BI 검색 만들기
**Power BI** 단추를 클릭하여 Power BI 대화 상자를 열고 필요한 정보를 제공합니다. 이 검색이 시간당 한 번씩 실행되길 원하고 *Contoso Perf*라는 데이터 집합을 만듭니다. 원하는 데이터를 만드는 검색 열기가 이미 있으므로 **저장된 검색**에 대한 *현재 검색 쿼리 사용*의 기본값을 유지합니다.

![Power BI 검색](media/log-analytics-power-bi/walkthrough-schedule.png)

### Power BI 검색 확인
일정을 올바르게 만들었는지 확인하려면 OMS 대시보드의 **설정** 타일 아래에서 Power BI 검색의 목록을 봅니다. 동기화가 실행되었음을 보고할 때까지 몇 분 정도 기다렸다가 이 보기를 새로 고칩니다.

![Power BI 검색](media/log-analytics-power-bi/walkthrough-schedules.png)

### Power BI의 데이터 집합 확인
[powerbi.microsoft.com](http://powerbi.microsoft.com/)에 계정으로 로그인하고 왼쪽 창 맨 아래의 **데이터 집합**으로 스크롤합니다. 내보내기가 성공적으로 실행되었음을 나타내는 *Contoso Perf*가 나열된 것을 볼 수 있습니다.

![Power BI 데이터 집합](media/log-analytics-power-bi/walkthrough-datasets.png)

### 데이터 집합 기반 보고서 만들기
**Contoso Perf** 데이터 집합을 선택한 다음 오른쪽 **필드** 창의 **결과**를 클릭하여 이 데이터 집합에 포함된 필드를 봅니다. 각 컴퓨터에 대한 프로세서 사용률을 보여 주는 꺾은선형 그래프를 만들려면 다음 작업을 수행합니다.

1. 선 차트 시각화를 선택합니다.
2. **ObjectName**을 **보고서 수준 필터**로 끌어서 놓고 **프로세서**를 확인합니다.
3. **CounterName**을 **보고서 수준 필터**로 끌어서 놓고 **% 프로세서 시간**을 확인합니다.
4. **CounterValue**를 **값**으로 끌어서 놓습니다.
5. **컴퓨터**를 **범례**로 끌어서 놓습니다.
6. **TimeGenerated**를 **축**으로 끌어서 놓습니다.

데이터 집합의 데이터로 결과 꺾은선형 그래프가 표시된 것을 확인할 수 있습니다.

![Power BI 꺾은선형 그래프](media/log-analytics-power-bi/walkthrough-linegraph.png)

### 보고서 저장
화면 맨 위에 있는 저장 단추를 클릭하여 보고서를 저장하고 왼쪽 창의 보고서 섹션에 나열된 것을 검사합니다.

![Power BI 보고서](media/log-analytics-power-bi/walkthrough-report.png)

## 다음 단계

- Power BI로 내보낼 수 있는 쿼리를 작성하려면 [로그 검색](log-analytics-log-searches.md)에 대해 알아봅니다.
- Log Analytics 내보내기 기반 시각화를 작성하려면 [Power BI](powerbi.microsoft.com)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0504_2016-->