---
title: 확장된 Spark 기록 서버를 사용하여 앱 디버그
description: Azure Synapse Analytics에서 확장된 Spark 기록 서버를 사용하여 Spark 애플리케이션을 디버그하고 진단합니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 2cdf4277c1177f0a963e7b7bb05813d47d2c1f57
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814337"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>확장된 Apache Spark 기록 서버를 사용하여 Apache Spark 애플리케이션 디버그 및 진단

이 문서에서는 확장된 Apache Spark 기록 서버를 사용하여 완료되거나 실행 중인 Spark 애플리케이션을 디버그 및 진단하는 방법에 대한 지침을 제공합니다.

이 확장에는 데이터 탭, 그래프 탭 및 진단 탭이 포함되어 있습니다. **데이터** 탭은 Spark 작업의 입력 및 출력 데이터를 확인하는 데 사용합니다. **그래프** 탭은 작업 그래프의 데이터 흐름과 재생을 보여 줍니다. **진단** 탭은 **데이터 기울이기**, **시간 기울이기** 및 **실행기 사용량 분석** 을 보여 줍니다.

## <a name="access-the-apache-spark-history-server"></a>Apache Spark 기록 서버 액세스

Apache Spark 기록 서버는 완료되거나 실행 중인 Spark 애플리케이션의 웹 사용자 인터페이스입니다. Azure Synapse Analytics에서 Apache Spark 기록 서버 웹 인터페이스를 열 수 있습니다.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Apache Spark 애플리케이션 노드에서 Spark 기록 서버 웹 UI 열기

1. [Azure Synapse Analytics](https://web.azuresynapse.net/)를 엽니다.

2. **모니터** 를 선택한 다음, **Apache Spark 애플리케이션** 을 선택합니다.

    ![모니터를 선택한 다음, Spark 애플리케이션을 선택합니다.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. 애플리케이션을 선택한 다음, **로그 쿼리** 를 선택하여 엽니다.

    ![로그 쿼리 창을 엽니다.](./media/apache-spark-history-server/open-application-window.png)

4. **Spark 기록 서버** 를 선택하면 Spark 기록 서버 웹 UI가 표시됩니다.

    ![Spark 기록 서버를 엽니다.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node&quot;></a>데이터 노드에서 Spark 기록 서버 웹 UI 열기

1. Synapse Studio Notebook의 작업 실행 출력 셀 또는 Notebook 문서 맨 아래에 있는 상태 패널에서 **Spark 기록 서버** 를 선택합니다. **세션 세부 정보** 를 선택합니다.

   ![Spark 기록 서버 시작 1](./media/apache-spark-history-server/launch-history-server2.png &quot;Spark 기록 서버 시작")

2. 슬라이드 아웃 패널에서 **Spark 기록 서버** 를 선택합니다.

   ![Spark 기록 서버 시작 2](./media/apache-spark-history-server/launch-history-server.png "Spark 기록 서버 시작")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Spark 기록 서버의 데이터 탭 살펴보기

보려는 작업의 작업 ID를 선택합니다. 그런 다음, 도구 메뉴에서 **데이터** 를 선택하여 데이터 뷰를 표시합니다. 이 섹션에서는 데이터 탭에서 다양한 태스크를 수행하는 방법을 보여 줍니다.

* 각 탭을 선택하여 **입력**, **출력** 및 **테이블 작업** 을 확인합니다.

    ![데이터 - Spark 애플리케이션 탭](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* **복사** 를 선택하여 모든 행을 복사합니다.

    ![데이터 - Spark 애플리케이션 복사](./media/apache-spark-history-server/apache-spark-data-copy.png)

* **csv** 를 선택하여 모든 데이터를 CSV 파일로 저장합니다.

    ![데이터 - Spark 애플리케이션 저장](./media/apache-spark-history-server/apache-spark-data-save.png)

* **검색** 필드에 키워드를 입력하여 검색합니다. 검색 결과가 즉시 표시됩니다.

    ![데이터 - Spark 애플리케이션 검색](./media/apache-spark-history-server/apache-spark-data-search.png)

* 열 머리글을 선택하여 테이블을 정렬하거나, 더하기 기호를 선택하여 더 많은 정보를 표시하도록 행을 확장하거나, 빼기 기호를 선택하여 행을 축소합니다.

    ![데이터 - Spark 애플리케이션 테이블](./media/apache-spark-history-server/apache-spark-data-table.png)

* **부분 다운로드** 를 선택하여 단일 파일을 다운로드합니다. 선택한 파일이 로컬에 다운로드됩니다. 파일이 더 이상 존재하지 않으면 오류 메시지가 포함된 새 탭이 표시됩니다.

    ![데이터 - Spark 애플리케이션 다운로드 행](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 전체 경로 또는 상대 경로를 복사하려면 드롭다운 메뉴에서 확장되는 **전체 경로 복사** 또는 **상대 경로 복사** 옵션을 선택합니다. Azure Data Lake Storage 파일의 경우 **Azure Storage Explorer에서 열기** 를 실행하면 Azure Storage Explorer가 시작되고 로그인 시 해당 폴더를 찾습니다.

    ![데이터 - Spark 애플리케이션 경로 복사](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 행 수가 너무 많아서 한 페이지에 표시할 수 없는 경우 테이블 아래에 있는 페이지 번호를 선택하여 페이지를 탐색합니다.

    ![데이터 - Spark 애플리케이션 페이지](./media/apache-spark-history-server/apache-spark-data-page.png)

* **데이터** 옆에 있는 물음표를 마우스로 가리켜 도구 설명을 표시하거나, 물음표를 선택하여 추가 정보를 가져옵니다.

    ![데이터 - Spark 애플리케이션 추가 정보](./media/apache-spark-history-server/sparkui-data-more-info.png)

* **피드백 보내기** 를 선택하여 문제에 관한 피드백을 보냅니다.

    ![Spark 그래프가 피드백을 다시 제공합니다.](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark 기록 서버의 그래프 탭

보려는 작업의 작업 ID를 선택합니다. 그런 다음, 도구 메뉴에서 **그래프** 를 선택하여 작업 그래프 뷰를 가져옵니다.

### <a name="overview"></a>개요

생성된 작업 그래프에서 작업에 대한 개요를 볼 수 있습니다. 기본적으로 이 그래프는 모든 작업을 표시합니다. **작업 ID** 를 기준으로 이 뷰를 필터링할 수 있습니다.

![Spark 애플리케이션 및 작업 그래프 작업 ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>표시

기본적으로 **진행률** 표시가 선택되어 있습니다. **표시** 드롭다운 목록에서 **읽음** 또는 **기록됨** 을 선택하여 데이터 흐름을 확인할 수 있습니다.

![Spark 애플리케이션 및 작업 그래프 표시](./media/apache-spark-history-server/sparkui-graph-display.png)

그래프 노드는 열 지도 범례에 표시된 색상을 표시합니다.

![Spark 애플리케이션 및 작업 그래프 열 지도](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>재생

작업을 재생하려면 **재생** 을 선택합니다. 언제든지 **중지** 를 선택하여 중지할 수 있습니다. 태스크 색상은 재생 시 여러 상태를 표시합니다.

|색상|의미|
|-|-|
|녹색|성공: 작업이 성공적으로 완료되었습니다.|
|Orange|다시 시도: 실패했지만 작업의 최종 결과에 영향을 주지 않는 태스크 인스턴스입니다. 해당 작업에는 중복 또는 다시 시도 인스턴스가 있었으며, 나중에 성공할 수 있습니다.|
|파랑|실행 중: 태스크가 실행 중입니다.|
|흰색|대기 중이거나 건너뜀: 태스크가 실행을 위해 대기 중이거나 단계를 건너뛰었습니다.|
|빨간색|실패: 태스크가 실패했습니다.|

다음 이미지는 녹색, 주황색 및 파란색 상태 색상을 보여 줍니다.

![Spark 애플리케이션 및 작업 그래프 색상 샘플, 실행 중](./media/apache-spark-history-server/sparkui-graph-color-running.png)

다음 이미지는 녹색 및 흰색 상태 색상을 보여 줍니다.

![Spark 애플리케이션 및 작업 그래프 색상 샘플, 건너뛰기](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

다음 이미지는 빨간색 및 녹색 상태 색상을 보여 줍니다.

![Spark 애플리케이션 및 작업 그래프 색상 샘플, 실패](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 각 작업을 재생할 수 있습니다. 완료되지 않은 작업에는 재생이 지원되지 않습니다.

### <a name="zoom"></a>확대/축소

마우스 스크롤을 사용하여 작업 그래프를 확대 및 축소하거나 **크기에 맞게** 를 선택하여 화면에 맞게 조정합니다.

![Spark 애플리케이션 및 작업 그래프 크기에 맞게](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>도구 설명

실패한 태스크가 있을 경우 그래프 노드를 마우스로 가리켜 도구 설명을 확인하고, 단계를 클릭하여 단계 페이지를 엽니다.

![Spark 애플리케이션 및 작업 그래프 도구 설명](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

작업 그래프 탭의 단계에 다음 조건을 충족하는 태스크가 있는 경우 도구 설명 및 작은 아이콘이 표시됩니다.

|조건|Description|
|-|-|
|데이터 기울이기|데이터 읽기 크기 > 이 단계에 속한 모든 작업의 평균 데이터 읽기 크기 * 2 및 데이터 읽기 크기 > 10MB|
|시간 기울이기|실행 시간 > 이 단계에 속한 모든 작업의 평균 실행 시간 * 2 및 실행 시간 > 2분|
   
![Spark 애플리케이션 및 작업 그래프 기울이기 아이콘](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>그래프 노드 설명

작업 그래프 노드에는 각 단계에 대한 다음 정보가 표시됩니다.

  * ID를 삭제합니다.
  * 이름 또는 설명
  * 총 작업 수
  * 읽은 데이터: 입력 크기 및 순서 섞기 읽기 크기의 합계입니다.
  * 데이터 쓰기: 출력 크기 및 순서 섞기 쓰기 크기의 합계입니다.
  * 실행 시간: 첫 번째 시도 시작 시간과 마지막 시도 완료 시간 사이의 시간
  * 행 개수: 입력 레코드, 출력 레코드, 무작위 읽기 레코드 및 무작위 쓰기 레코드의 합계
  * 진행률

    > [!NOTE]  
    > 기본적으로 작업 그래프 노드에는 단계 실행 시간을 제외하고 각 단계의 마지막 시도 정보가 표시됩니다. 그러나 재생하는 동안 그래프 노드에는 각 시도에 대한 정보가 표시됩니다.
    >  
    > 읽기 및 쓰기의 데이터 크기는 1MB = 1,000KB = 1,000 * 1,000바이트입니다.

### <a name="provide-feedback"></a>피드백 제공

**피드백 보내기** 를 선택하여 문제에 관한 피드백을 보냅니다.

![Spark 애플리케이션 및 작업 그래프 피드백](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark 기록 서버의 진단 탭 살펴보기

진단 탭에 액세스하려면 작업 ID를 선택합니다. 그런 다음, 도구 메뉴에서 **진단** 을 선택하여 작업 진단 뷰를 표시합니다. 진단 탭에는 **데이터 기울이기**, **시간 기울이기** 및 **실행기 사용량 분석** 이 포함되어 있습니다.

각 탭을 선택하여 **데이터 기울이기**, **시간 기울이기** 및 **실행기 사용량 분석** 을 확인합니다.

![SparkUI 진단 데이터 기울이기 탭 다시](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>데이터 기울이기

**데이터 기울이기** 탭을 선택하면 지정한 매개 변수에 따라 해당 기울이기 작업이 표시됩니다.

* **매개 변수 지정** - 첫 번째 섹션에는 데이터 기울이기를 검색하는 데 사용되는 매개 변수가 표시됩니다. 기본 규칙은 읽은 태스크 데이터가 평균 읽은 작업 데이터의 3배보다 크고, 10MB를 초과하는 것입니다. 기울어진 작업에 대한 고유 규칙을 정의하려는 경우 매개 변수를 선택할 수 있습니다. **기울어진 단계** 및 **기울어진 문자** 섹션이 그에 따라 새로 고침됩니다.

* **기울어진 단계** - 두 번째 섹션에는 위에 지정된 조건을 충족하는 기울어진 작업이 있는 단계가 표시됩니다. 단계에 기울어진 작업이 두 개 이상 있는 경우 기울어진 단계 테이블에는 가장 많이 기울어진 작업(예: 데이터 기울이기 데이터가 가장 큰 작업)만 표시됩니다.

    ![SparkUI 진단 데이터 기울이기 탭](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **기울이기 차트** - 기울이기 단계 테이블에서 행을 선택하면 기울이기 차트에 데이터 읽기 및 실행 시간을 기준으로 더 많은 작업 분포 정보가 표시됩니다. 기울어진 작업은 빨간색으로 표시되고 일반 작업은 파란색으로 표시됩니다. 차트에는 최대 100개의 샘플 태스크가 표시되고 태스크 세부 정보가 오른쪽 아래 패널에 표시됩니다.

    ![10단계에 대한 SparkUI 기울이기 차트](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>시간 기울이기

**시간 기울이기** 탭에는 작업 실행 시간을 기준으로 기울어진 작업이 표시됩니다.

* **매개 변수 지정** - 첫 번째 섹션에는 시간 기울이기를 검색하는 데 사용되는 매개 변수가 표시됩니다. 시간 기울이기를 검색하는 기본 기준은 작업 실행 시간이 평균 실행 시간의 3배보다 크고 작업 실행 시간이 30초를 초과하는 것입니다. 필요에 따라 매개 변수를 변경할 수 있습니다. **기울어진 작업** 및 **기울이기 차트** 에는 위의 **데이터 기울이기** 탭과 마찬가지로 해당 단계 및 작업 정보가 표시됩니다.

* **시간 기울이기** 를 선택하면 **매개 변수 지정** 섹션에 설정된 매개 변수에 따라 필터링된 결과가 **기울어진 단계** 섹션에 표시됩니다. **기울어진 단계** 섹션에서 한 항목을 선택하면 해당 차트가 section3에 초안으로 작성되고 태스크 세부 정보가 오른쪽 아래 패널에 표시됩니다.

    ![SparkUI 진단 시간 기울이기 섹션](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>실행기 사용량 분석

실행기 사용량 그래프는 Spark 작업 실행기의 할당 및 실행 상태를 시각화합니다.  

1. **실행기 사용량 분석** 을 선택하면 **할당된 실행기**, **실행 중인 실행기**,**유휴 실행기** 및 **최대 실행기 인스턴스** 등 실행기 사용량에 관한 4가지 곡선에 대한 초안이 작성됩니다. 할당된 실행기의 경우, 각 “실행기 추가됨” 또는 “실행기 제거됨” 이벤트는 할당된 실행기를 늘리거나 줄입니다. 자세한 비교를 위해 “작업” 탭에서 “이벤트 타임라인”을 확인할 수 있습니다.

   ![SparkUI 진단 실행기 탭](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 색상 아이콘을 선택하여 모든 초안에서 해당 콘텐츠를 선택하거나 선택 취소합니다.

    ![SparkUI 진단 차트 선택](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>알려진 문제

RDD(복원력 있는 분산 데이터 세트)를 사용하는 입력/출력 데이터는 데이터 탭에 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../overview-what-is.md)
- [Apache Spark용 .NET 설명서](/dotnet/spark)

