---
title: 확장 Spark 기록 서버를 사용 하 여 앱 디버그-Azure Synapse에서 Apache Spark
description: 확장 Spark 기록 서버를 사용 하 여 Azure Synapse Analytics에서 Spark 응용 프로그램을 디버그 하 고 진단 합니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429214"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>확장 Apache Spark 기록 서버를 사용 하 여 Apache Spark 응용 프로그램 디버그 및 진단

이 문서에서는 확장 Apache Spark 기록 서버를 사용 하 여 완료 된 Spark 응용 프로그램을 디버깅 및 진단 하는 방법에 대 한 지침을 제공 합니다.

확장에는 데이터 탭, 그래프 탭 및 진단 탭이 포함 되어 있습니다. **데이터** 탭을 사용 하 여 Spark 작업의 입력 및 출력 데이터를 확인 하십시오. **그래프** 탭은 작업 그래프의 데이터 흐름과 재생을 보여 줍니다. **진단** 탭에는 **데이터 기울이기**, **시간 오차**및 **실행자 사용 현황 분석이**표시 됩니다.

## <a name="access-the-apache-spark-history-server"></a>Apache Spark 기록 서버에 액세스

Apache Spark history 서버는 Spark 응용 프로그램을 완료 하 고 실행 하기 위한 웹 사용자 인터페이스입니다. Azure Synapse Analytics에서 Apache Spark 기록 서버 웹 인터페이스를 열 수 있습니다.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Apache spark 응용 프로그램 노드에서 Spark 기록 서버 웹 UI 열기

1. [Azure Synapse Analytics](https://web.azuresynapse.net/)를 엽니다.

2. **모니터**를 클릭 한 다음 **Apache Spark 응용 프로그램**을 선택 합니다.

    ![모니터를 클릭 하 고 spark 응용 프로그램을 선택 합니다.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. 응용 프로그램을 선택한 다음 **로그 쿼리** 를 클릭 하 여 엽니다.

    ![로그 쿼리 창을 엽니다.](./media/apache-spark-history-server/open-application-window.png)

4. **Spark 기록 서버**를 선택 하면 Spark 기록 서버 웹 UI가 표시 됩니다.

    ![Spark 기록 서버를 엽니다.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>데이터 노드에서 Spark 기록 서버 웹 UI 열기

1. Azure Synapse Studio 노트북에서 작업 실행 출력 셀 또는 노트북 문서의 아래쪽에 있는 상태 패널에서 **Spark 기록 서버** 를 선택 합니다. **세션 세부 정보**를 선택합니다.

   ![Spark 기록 서버 시작](./media/apache-spark-history-server/launch-history-server2.png "Spark 기록 서버 시작")

2. 밀기 패널에서 **Spark 기록 서버** 를 선택 합니다.

   ![Spark 기록 서버 시작](./media/apache-spark-history-server/launch-history-server.png "Spark 기록 서버 시작")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Spark 기록 서버에서 데이터 탭 탐색

보려는 작업의 작업 ID를 선택 합니다. 그런 다음 도구 메뉴에서 **데이터** 를 선택 하 여 데이터 뷰를 가져옵니다. 이 섹션에서는 데이터 탭에서 다양 한 작업을 수행 하는 방법을 보여 줍니다.

* 각 탭을 선택하여 **입력**, **출력** 및 **테이블 작업**을 확인합니다.

    ![Spark 응용 프로그램 탭에 대 한 데이터](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* **복사**를 선택 하 여 모든 행을 복사 합니다.

    ![Spark 응용 프로그램 복사에 대 한 데이터](./media/apache-spark-history-server/apache-spark-data-copy.png)

* **Csv**를 선택 하 여 모든 데이터를 csv 파일로 저장 합니다.

    ![Spark 응용 프로그램 저장에 대 한 데이터](./media/apache-spark-history-server/apache-spark-data-save.png)

* 필드 **검색**에 키워드를 입력 하 여 검색 합니다. 검색 결과가 즉시 표시 됩니다.

    ![Spark 응용 프로그램 검색에 대 한 데이터](./media/apache-spark-history-server/apache-spark-data-search.png)

* 테이블을 정렬 하려면 열 머리글을 선택 하 고 더하기 기호를 선택 하 여 행을 확장 하 여 세부 정보를 표시 하거나 빼기 기호를 선택 하 여 행을 축소 합니다.

    ![Spark 응용 프로그램 테이블에 대 한 데이터](./media/apache-spark-history-server/apache-spark-data-table.png)

* **부분 다운로드**를 선택 하 여 단일 파일을 다운로드 합니다. 선택한 파일이 로컬에 다운로드 됩니다. 파일이 더 이상 존재 하지 않으면 오류 메시지가 표시 된 새 탭이 표시 됩니다.

    ![Spark 응용 프로그램 다운로드 행에 대 한 데이터](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 전체 경로 또는 상대 경로를 복사 하려면 드롭다운 메뉴에서 확장 되는 **전체 경로 복사** 또는 **상대 경로 복사** 옵션을 선택 합니다. Azure Data Lake Storage 파일의 경우 **Azure Storage 탐색기** 를 시작 Azure Storage 탐색기 시작 하 고가 로그인 할 때 폴더를 찾습니다.

    ![Spark 응용 프로그램 복사 경로에 대 한 데이터](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 한 페이지에 표시할 행이 너무 많은 경우 페이지 아래에서 페이지 번호를 선택 하 여 페이지를 탐색 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지](./media/apache-spark-history-server/apache-spark-data-page.png)

* **데이터** 옆에 있는 물음표를 가리켜 도구 설명을 표시 하거나 물음표를 선택 하 여 자세한 정보를 확인 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 추가 정보](./media/apache-spark-history-server/sparkui-data-more-info.png)

* **사용자 의견 제공**을 선택 하 여 문제에 대 한 피드백을 보내 주세요.

    ![Spark graph에서 피드백을 다시 제공 합니다.](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark 기록 서버의 그래프 탭

보려는 작업의 작업 ID를 선택 합니다. 그런 다음 도구 메뉴에서 **그래프** 를 선택 하 여 작업 그래프 뷰를 가져옵니다.

### <a name="overview"></a>개요

생성 된 작업 그래프에서 작업에 대 한 개요를 볼 수 있습니다. 기본적으로 그래프는 모든 작업을 표시 합니다. **작업 ID**를 기준으로이 뷰를 필터링 할 수 있습니다.

![Spark 응용 프로그램 및 작업 그래프 작업 ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>디스플레이

기본적으로 **진행률** 표시가 선택 되어 있습니다. **표시** 드롭다운 목록에서 **읽기** 또는 **쓰기** 를 선택 하 여 데이터 흐름을 확인할 수 있습니다.

![Spark 응용 프로그램 및 작업 그래프 표시](./media/apache-spark-history-server/sparkui-graph-display.png)

그래프 노드는 열 지도 범례에 표시 된 색을 표시 합니다.

![Spark 응용 프로그램 및 작업 그래프 열 지도](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>재생

작업을 재생 하려면 **재생**을 선택 합니다. 언제 든 지 **중지** 를 선택 하 여 중지할 수 있습니다. 작업 색은 재생할 때 다른 상태를 표시 합니다.

|색상|의미|
|-|-|
|녹색|성공: 작업이 성공적으로 완료 되었습니다.|
|주황색|다시 시도 됨: 실패 한 작업의 인스턴스는 작업의 최종 결과에 영향을 주지 않습니다. 해당 작업에는 중복 또는 다시 시도 인스턴스가 있었으며, 나중에 성공할 수 있습니다.|
|파랑|실행 중: 태스크가 실행 되 고 있습니다.|
|흰색|대기 중이거나 건너뜀: 태스크가 실행 되기를 기다리고 있거나 단계를 건너뛰었습니다.|
|빨강|실패: 태스크가 실패 했습니다.|

다음 이미지는 녹색, 주황색 및 파란색 상태 색을 보여 줍니다.

![Spark 응용 프로그램 및 작업 그래프 색 샘플, 실행](./media/apache-spark-history-server/sparkui-graph-color-running.png)

다음 이미지는 녹색 및 흰색 상태 색을 보여 줍니다.

![Spark 응용 프로그램 및 작업 그래프 색 샘플, 건너뛰기](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

다음 이미지는 빨강 및 녹색 상태 색을 보여 줍니다.

![Spark 응용 프로그램 및 작업 그래프 색 샘플, 실패](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 각 작업을 재생할 수 있습니다. 완료 되지 않은 작업의 경우 재생이 지원 되지 않습니다.

### <a name="zoom"></a>Zoom

마우스 스크롤을 사용 하 여 작업 그래프를 확대 및 축소 하거나 **크기에 맞게** 를 선택 하 여 화면에 맞게 확대 합니다.

![Spark 응용 프로그램 및 작업 그래프 크기에 맞게 확대](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>도구 설명

실패 한 작업이 있을 때 도구 설명을 보려면 그래프 노드에 마우스를 가져가서 스테이지 페이지를 열려면 스테이지를 선택 합니다.

![Spark 응용 프로그램 및 작업 그래프 도구 설명](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

작업 그래프 탭에서 단계에는 다음 조건을 충족 하는 작업이 있는 경우 도구 설명 및 작은 아이콘이 표시 됩니다.

|조건|Description|
|-|-|
|데이터 기울이기|데이터 읽기 크기 >이 단계 내의 모든 태스크에 대 한 평균 데이터 읽기 크기 * 2 및 데이터 읽기 크기 > 10mb|
|시간차|실행 시간 >이 단계 내 모든 작업의 평균 실행 시간 * 2 및 실행 시간 > 2 분|
   
![Spark 응용 프로그램 및 작업 그래프 기울이기 아이콘](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>그래프 노드 설명

작업 그래프 노드에는 각 단계에 대 한 다음 정보가 표시 됩니다.

  * ID를 삭제합니다.
  * 이름 또는 설명
  * 총 작업 수
  * 읽은 데이터: 입력 크기 및 순서 섞기 읽기 크기의 합계입니다.
  * 데이터 쓰기: 출력 크기 및 순서 섞기 크기의 합계입니다.
  * 실행 시간: 첫 번째 시도 시작 시간과 마지막 시도 완료 시간 사이의 시간
  * 행 개수: 입력 레코드, 출력 레코드, 무작위 읽기 레코드 및 무작위 쓰기 레코드의 합계
  * 진행률

    > [!NOTE]  
    > 기본적으로 작업 그래프 노드는 단계 실행 시간을 제외 하 고 각 단계에 대 한 마지막 시도의 정보를 표시 합니다. 그러나 재생 하는 동안 그래프 노드에는 각 시도에 대 한 정보가 표시 됩니다.
    >  
    > 읽기 및 쓰기의 데이터 크기는 1MB = 1000 KB = 1000 * 1000 바이트입니다.

### <a name="provide-feedback"></a>피드백 제공

**사용자 의견 제공**을 선택 하 여 문제에 대 한 피드백을 보내 주세요.

![Spark 응용 프로그램 및 작업 그래프 피드백](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark 기록 서버에서 진단 탭 살펴보기

진단 탭에 액세스 하려면 작업 ID를 선택 합니다. 그런 다음 도구 메뉴에서 **진단** 을 선택 하 여 작업 진단 보기를 가져옵니다. 진단 탭에는 **데이터 기울이기**, **시간 기울이기** 및 **실행기 사용량 분석**이 포함되어 있습니다.

각 탭을 선택하여 **데이터 기울이기**, **시간 기울이기** 및 **실행기 사용량 분석**을 확인합니다.

![SparkUI 진단 데이터 기울이기 탭 다시](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>데이터 기울이기

**데이터 기울이기** 탭을 선택 하면 지정 된 매개 변수를 기준으로 해당 하는 기울어진 태스크가 표시 됩니다.

* **매개 변수 지정** - 첫 번째 섹션에는 데이터 기울이기를 검색하는 데 사용되는 매개 변수가 표시됩니다. 기본 규칙은 작업 데이터 읽기는 평균 태스크 데이터 읽기의 3 배 보다 크고 태스크 데이터 읽기는 10mb 보다 큽니다. 기울어진 태스크에 대 한 사용자 고유의 규칙을 정의 하려는 경우에는 매개 변수를 선택할 수 있습니다. 그러면 **기울어짐 단계** 및 **기울이기 문자** 섹션이 그에 따라 새로 고쳐집니다.

* **기울어진 단계** - 두 번째 섹션에는 위에 지정된 조건을 충족하는 기울어진 작업이 있는 단계가 표시됩니다. 단계에 기울어진 작업이 두 개 이상 있는 경우 기울어진 단계 테이블에는 가장 많이 기울어진 작업(예: 데이터 기울이기 데이터가 가장 큰 작업)만 표시됩니다.

    ![sparkui 진단 데이터 기울이기 탭](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **차트 기울이기** – 기울이기 단계 테이블에서 행을 선택 하면 데이터 읽기 및 실행 시간을 기반으로 더 많은 작업 배포 세부 정보가 표시 됩니다. 기울어진 작업은 빨간색으로 표시되고 일반 작업은 파란색으로 표시됩니다. 차트에는 최대 100 개의 샘플 태스크가 표시 되 고 작업 세부 정보는 오른쪽 아래 패널에 표시 됩니다.

    ![10 단계에 대 한 sparkui 기울이기 차트](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>시간 기울이기

**시간 기울이기** 탭에는 작업 실행 시간을 기준으로 기울어진 작업이 표시됩니다.

* **매개 변수 지정** -첫 번째 섹션에는 시간 오차를 검색 하는 데 사용 되는 매개 변수가 표시 됩니다. 시간 기울이기를 검색하는 기본 기준은 작업 실행 시간이 평균 실행 시간의 3배보다 크고 작업 실행 시간이 30초를 초과하는 것입니다. 필요에 따라 매개 변수를 변경할 수 있습니다. **기울어진 작업** 및 **기울이기 차트**에는 위의 **데이터 기울이기** 탭과 마찬가지로 해당 단계 및 작업 정보가 표시됩니다.

* **시간 오차**를 선택 하 고 필터링 된 결과는 **매개 변수 지정**섹션에 설정 된 매개 변수에 따라 **기울어진 단계** 섹션에 표시 됩니다. **기울어진 단계** 섹션에서 한 항목을 선택 하 고 해당 차트가 section3에서 초안을 만든 다음 오른쪽 아래 패널에 작업 세부 정보가 표시 됩니다.

    ![sparkui 진단 시간 오차 섹션](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>실행기 사용량 분석

Executor 사용 그래프는 Spark 작업 실행 기의 할당 및 실행 상태를 시각화 합니다.  

1. **Executor 사용 분석**을 선택 합니다. 그러면 **할당 된 실행자**, **실행 실행자**, **유휴 실행자**및 **Max executor 인스턴스**를 포함 하 여 executor 사용에 대 한 네 가지 유형의 곡선이 초안입니다. 할당 된 실행자와 관련 하 여, 각 "Executor 추가" 또는 "Executor가 제거 되었습니다." 이벤트는 할당 된 실행자를 늘리거나 줄입니다. 자세한 비교를 위해 “작업” 탭에서 “이벤트 타임라인”을 확인할 수 있습니다.

   ![sparkui 진단 실행자 탭](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 색 아이콘을 선택 하 여 모든 초안에서 해당 콘텐츠를 선택 하거나 선택 취소 합니다.

    ![sparkui 진단 차트 선택](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>알려진 문제

Rds (복원 력 있는 분산 데이터 집합)를 사용 하는 입력/출력 데이터는 데이터 탭에 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../overview-what-is.md)
- [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

