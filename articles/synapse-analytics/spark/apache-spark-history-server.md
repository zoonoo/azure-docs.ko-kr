---
title: 확장된 스파크 기록 서버를 사용하여 앱을 디버깅 - Azure 시냅스의 아파치 스파크
description: 확장된 스파크 기록 서버를 사용하여 Azure Synapse Analytics에서 스파크 응용 프로그램을 디버깅하고 진단합니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429214"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>확장된 아파치 스파크 기록 서버를 사용하여 아파치 스파크 애플리케이션을 디버깅하고 진단합니다.

이 문서에서는 확장된 아파치 스파크 기록 서버를 사용하여 완료및 실행 중인 Spark 응용 프로그램을 디버깅하고 진단하는 방법에 대한 지침을 제공합니다.

확장에는 데이터 탭, 그래프 탭 및 진단 **Data** 탭이 포함됩니다. **그래프** 탭에는 작업 그래프의 데이터 흐름과 재생이 표시됩니다. **진단** 탭에는 **데이터 기울이기,** **시간 기울이기**및 **실행기 사용 분석이**표시됩니다.

## <a name="access-the-apache-spark-history-server"></a>아파치 스파크 히스토리 서버에 액세스

아파치 스파크 히스토리 서버는 Spark 응용 프로그램을 완료하고 실행하기 위한 웹 사용자 인터페이스입니다. Azure 시냅스 분석에서 아파치 스파크 기록 서버 웹 인터페이스를 열 수 있습니다.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>아파치 스파크 애플리케이션 노드에서 스파크 히스토리 서버 웹 UI 열기

1. [Azure 시냅스 분석을 엽니다.](https://web.azuresynapse.net/)

2. **모니터를**클릭한 다음 **아파치 스파크 응용 프로그램을 선택합니다.**

    ![모니터를 클릭한 다음 스파크 응용 프로그램을 선택합니다.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. 응용 프로그램을 선택한 다음 클릭하여 **로그 쿼리를** 엽니다.

    ![로그 쿼리 창을 엽니다.](./media/apache-spark-history-server/open-application-window.png)

4. **스파크 기록 서버를**선택하면 스파크 히스토리 서버 웹 UI가 표시됩니다.

    ![스파크 기록 서버를 엽니다.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>데이터 노드에서 스파크 히스토리 서버 웹 UI 열기

1. Azure Synapse Studio 전자 필기장에서 작업 실행 출력 셀 또는 전자 필기장 문서 하단의 상태 패널에서 **Spark 기록 서버를** 선택합니다. **세션 세부 정보**를 선택합니다.

   ![스파크 히스토리 서버 출시](./media/apache-spark-history-server/launch-history-server2.png "스파크 히스토리 서버 출시")

2. 슬라이드 아웃 패널에서 **스파크 기록 서버를** 선택합니다.

   ![스파크 히스토리 서버 출시](./media/apache-spark-history-server/launch-history-server.png "스파크 히스토리 서버 출시")

## <a name="explore-the-data-tab-in-spark-history-server"></a>스파크 기록 서버의 데이터 탭 살펴보기

보려는 작업에 대한 작업 ID를 선택합니다. 그런 다음 도구 메뉴에서 **데이터를** 선택하여 데이터 보기를 가져옵니다. 이 섹션에서는 데이터 탭에서 다양한 작업을 수행하는 방법을 보여 주며 있습니다.

* 각 탭을 선택하여 **입력**, **출력** 및 **테이블 작업**을 확인합니다.

    ![스파크 응용 프로그램 탭용 데이터](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* 복사 를 선택하여 모든 **행을**복사합니다.

    ![스파크 응용 프로그램 복사본용 데이터](./media/apache-spark-history-server/apache-spark-data-copy.png)

* **csv를**선택하여 모든 데이터를 CSV 파일로 저장합니다.

    ![스파크 애플리케이션 저장용 데이터](./media/apache-spark-history-server/apache-spark-data-save.png)

* 필드 **검색에**키워드를 입력하여 검색합니다. 검색 결과가 즉시 표시됩니다.

    ![스파크 애플리케이션 검색을 위한 데이터](./media/apache-spark-history-server/apache-spark-data-search.png)

* 테이블을 정렬할 열 헤더를 선택하거나, 더하기 기호를 선택하여 행을 확장하여 자세한 내용을 표시하거나, 행을 축소할 빼기 기호를 선택합니다.

    ![스파크 애플리케이션 테이블용 데이터](./media/apache-spark-history-server/apache-spark-data-table.png)

* 부분 다운로드를 선택하여 단일 파일을 **다운로드합니다.** 선택한 파일이 로컬로 다운로드됩니다. 파일이 더 이상 존재하지 않으면 오류 메시지와 함께 새 탭이 나타납니다.

    ![스파크 응용 프로그램 다운로드 행에 대한 데이터](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 전체 경로 또는 상대 경로를 복사하려면 드롭다운 메뉴에서 확장되는 **전체 경로 복사** 또는 상대 경로 **복사** 옵션을 선택합니다. Azure Data Lake Storage 파일의 경우 **Azure 저장소 탐색기에서 열기는** Azure 저장소 탐색기를 시작하고 로그인할 때 폴더를 찾습니다.

    ![스파크 응용 프로그램 복사 경로에 대한 데이터](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 한 페이지에 표시할 행이 너무 많은 경우 테이블 아래의 페이지 번호를 선택하여 페이지를 탐색합니다.

    ![스파크 응용 프로그램 용 데이터 페이지](./media/apache-spark-history-server/apache-spark-data-page.png)

* **데이터** 옆의 물음표에 마우스를 가져가서 도구 설명이 표시하거나 물음표를 선택하여 자세한 정보를 얻을 수 있습니다.

    ![스파크 애플리케이션에 대한 데이터 추가 정보](./media/apache-spark-history-server/sparkui-data-more-info.png)

* **피드백 제공을**선택하여 문제에 대한 피드백을 보냅니다.

    ![스파크 그래프는 우리에게 다시 피드백을 제공합니다](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>아파치 스파크 히스토리 서버의 그래프 탭

보려는 작업에 대한 작업 ID를 선택합니다. 그런 다음 도구 메뉴에서 **그래프를** 선택하여 작업 그래프 보기를 가져옵니다.

### <a name="overview"></a>개요

생성된 작업 그래프에서 작업의 개요를 볼 수 있습니다. 기본적으로 그래프에는 모든 작업이 표시됩니다. **작업 ID로**이 보기를 필터링할 수 있습니다.

![스파크 애플리케이션 및 직무 번호](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>디스플레이

기본적으로 **진행률** 표시가 선택됩니다. **표시** 드롭다운 목록에서 **읽기** 또는 **기록을** 선택하여 데이터 흐름을 확인할 수 있습니다.

![스파크 애플리케이션 및 작업 그래프 디스플레이](./media/apache-spark-history-server/sparkui-graph-display.png)

그래프 노드는 히트맵 범례에 표시된 색상을 표시합니다.

![스파크 애플리케이션 및 작업 그래프 히트맵](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>재생

작업을 재생하려면 **재생을 선택합니다.** 중지하려면 언제든지 **중지를** 선택할 수 있습니다. 작업 색상은 재생할 때 다른 상태를 표시합니다.

|색|의미|
|-|-|
|녹색|성공: 작업이 성공적으로 완료되었습니다.|
|주황색|다시 시도: 실패했지만 작업의 최종 결과에 영향을 주지 않는 작업의 인스턴스입니다. 해당 작업에는 중복 또는 다시 시도 인스턴스가 있었으며, 나중에 성공할 수 있습니다.|
|파랑|실행 중: 작업이 실행 중입니다.|
|흰색|대기 또는 건너뛰기: 작업이 실행되기를 기다리거나 스테이지가 건너왔습니다.|
|빨강|실패: 작업이 실패했습니다.|

다음 이미지는 녹색, 주황색 및 파란색 상태 색상을 보여 주며 있습니다.

![스파크 응용 프로그램 및 작업 그래프 색상 샘플, 실행](./media/apache-spark-history-server/sparkui-graph-color-running.png)

다음 이미지는 녹색 및 흰색 상태 색상을 보여 주며,

![스파크 응용 프로그램 및 작업 그래프 색상 샘플, 건너 뛰기](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

다음 이미지는 빨간색과 녹색 상태 색상을 보여 주며, 빨간색과 녹색상태 색상이 표시됩니다.

![스파크 응용 프로그램 및 작업 그래프 색상 샘플, 실패](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 각 작업을 재생할 수 있습니다. 불완전한 작업의 경우 재생이 지원되지 않습니다.

### <a name="zoom"></a>Zoom

마우스 스크롤을 사용하여 작업 그래프를 확대 및 축소하거나 **확대/축소를 선택하여** 화면에 맞출 수 있습니다.

![스파크 응용 프로그램 및 작업 그래프 줌에 맞게](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>도구 설명

그래프 노드를 마우스로 가리키면 실패한 작업이 있을 때 도구 설명이 표시되고 스테이지 페이지를 여는 단계를 선택합니다.

![스파크 애플리케이션 및 작업 그래프 툴팁](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

작업 그래프 탭에는 다음 조건을 충족하는 작업이 있는 경우 단계에 도구 설명과 작은 아이콘이 표시됩니다.

|조건|Description|
|-|-|
|데이터 기울이기|데이터 읽기 크기 > 이 단계 내의 모든 작업의 평균 데이터 읽기 크기 * 2 및 데이터 읽기 크기 > 10 MB|
|시간 왜곡|실행 시간 > 이 단계 내의 모든 작업의 평균 실행 시간 * 2 및 실행 시간 > 2 분|
   
![스파크 응용 프로그램 및 작업 그래프 기울이기 아이콘](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>그래프 노드 설명

작업 그래프 노드에는 각 단계의 다음 정보가 표시됩니다.

  * ID를 삭제합니다.
  * 이름 또는 설명
  * 총 작업 수
  * 읽은 데이터: 입력 크기 및 순서 섞기 읽기 크기의 합계입니다.
  * 데이터 쓰기: 출력 크기와 셔플의 합계는 크기를 씁니다.
  * 실행 시간: 첫 번째 시도 시작 시간과 마지막 시도 완료 시간 사이의 시간
  * 행 개수: 입력 레코드, 출력 레코드, 무작위 읽기 레코드 및 무작위 쓰기 레코드의 합계
  * 진행률

    > [!NOTE]  
    > 기본적으로 작업 그래프 노드는 각 단계의 마지막 시도의 정보를 표시합니다(스테이지 실행 시간 제외). 그러나 재생 중에 그래프 노드에는 각 시도의 정보가 표시됩니다.
    >  
    > 읽기 및 쓰기의 데이터 크기는 1MB = 1000KB = 1000 * 1000 바이트입니다.

### <a name="provide-feedback"></a>피드백 제공

**피드백 제공을**선택하여 문제에 대한 피드백을 보냅니다.

![스파크 애플리케이션 및 채용 그래프 피드백](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>아파치 스파크 히스토리 서버의 진단 탭 살펴보기

진단 탭에 액세스하려면 작업 ID를 선택합니다. 그런 다음 도구 메뉴에서 **진단을** 선택하여 작업 진단 보기를 가져옵니다. 진단 탭에는 **데이터 기울이기**, **시간 기울이기** 및 **실행기 사용량 분석**이 포함되어 있습니다.

각 탭을 선택하여 **데이터 기울이기**, **시간 기울이기** 및 **실행기 사용량 분석**을 확인합니다.

![SparkUI 진단 데이터 스큐 탭 다시](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>데이터 기울이기

**데이터 기울이기** 탭을 선택하면 지정된 매개 변수에 따라 해당 기울어진 작업이 표시됩니다.

* **매개 변수 지정** - 첫 번째 섹션에는 데이터 기울이기를 검색하는 데 사용되는 매개 변수가 표시됩니다. 기본 규칙은 작업 데이터 읽기가 평균 작업 데이터 읽기의 3배보다 크고 작업 데이터 읽기가 10MB보다 큽입니다. 비뚤어진 작업에 대한 사용자 고유의 규칙을 정의하려는 경우 매개 변수를 선택할 수 **있으며, 기울이기 단계** 및 **기울이기 Char** 섹션은 그에 따라 새로 고쳐집니다.

* **기울어진 단계** - 두 번째 섹션에는 위에 지정된 조건을 충족하는 기울어진 작업이 있는 단계가 표시됩니다. 단계에 기울어진 작업이 두 개 이상 있는 경우 기울어진 단계 테이블에는 가장 많이 기울어진 작업(예: 데이터 기울이기 데이터가 가장 큰 작업)만 표시됩니다.

    ![스파크이 진단 데이터 스큐 탭](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **기울이기 차트** - 기울이기 스테이지 테이블의 행을 선택하면 데이터 읽기 및 실행 시간을 기반으로 더 많은 작업 분포 세부 정보가 스큐 차트에 표시됩니다. 기울어진 작업은 빨간색으로 표시되고 일반 작업은 파란색으로 표시됩니다. 차트에는 최대 100개의 샘플 작업이 표시되고 작업 세부 정보가 오른쪽 하단 패널에 표시됩니다.

    ![스테이지 10의 스파크이 스큐 이스큐 차트](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>시간 기울이기

**시간 기울이기** 탭에는 작업 실행 시간을 기준으로 기울어진 작업이 표시됩니다.

* **매개변수 지정** - 첫 번째 섹션에는 시간 기울이기를 감지하는 데 사용되는 매개 변수가 표시됩니다. 시간 기울이기를 검색하는 기본 기준은 작업 실행 시간이 평균 실행 시간의 3배보다 크고 작업 실행 시간이 30초를 초과하는 것입니다. 필요에 따라 매개 변수를 변경할 수 있습니다. **기울어진 작업** 및 **기울이기 차트**에는 위의 **데이터 기울이기** 탭과 마찬가지로 해당 단계 및 작업 정보가 표시됩니다.

* **시간 기울이기를**선택한 다음 필터링된 결과가 **매개변수 지정**섹션에 설정된 매개변수에 따라 **기울어진 단계** 섹션에 표시됩니다. **기울어진 단계** 섹션에서 한 항목을 선택한 다음 해당 차트가 섹션3에 구도되고 작업 세부 정보가 오른쪽 하단 패널에 표시됩니다.

    ![스파크기 진단 시간 기울이기 섹션](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>실행기 사용량 분석

실행기 사용 그래프는 Spark 작업 실행기의 할당 및 실행 상태를 시각화합니다.  

1. **실행기 사용 분석을**선택하면 **할당된 실행기,** **실행 실행기,** **유휴 실행기**및 **최대 실행기 인스턴스를**포함하여 실행기 사용에 대한 네 가지 유형 곡선이 구배됩니다. 할당된 실행기와 관련하여 각 "실행기 추가" 또는 "실행기 제거" 이벤트는 할당된 실행자를 늘리거나 줄입니다. 자세한 비교를 위해 “작업” 탭에서 “이벤트 타임라인”을 확인할 수 있습니다.

   ![스파크이 진단 실행기 탭](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 모든 구문에서 해당 콘텐츠를 선택하거나 선택 취소하려면 색상 아이콘을 선택합니다.

    ![스파쿠이 가 선택 차트를 진단](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>알려진 문제

복원력 있는 분산 데이터 집합(RDD)을 사용하는 입력/출력 데이터는 데이터 탭에 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../overview-what-is.md)
- [아파치 스파크 문서용 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

