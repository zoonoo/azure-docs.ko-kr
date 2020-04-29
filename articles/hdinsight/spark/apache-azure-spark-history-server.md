---
title: Apache Spark 기록 서버의 확장 기능을 사용 하 여 앱 디버그-Azure HDInsight
description: Apache Spark 기록 서버의 확장 기능을 사용 하 여 Spark 응용 프로그램을 디버그 하 고 진단할 수 있습니다 (Azure HDInsight).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76548937"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Apache Spark 기록 서버의 확장 기능을 사용 하 여 Spark 응용 프로그램 디버그 및 진단

이 문서에서는 Apache Spark 기록 서버의 확장 기능을 사용 하 여 완료 또는 실행 중인 Spark 응용 프로그램을 디버깅 및 진단 하는 방법을 보여 줍니다. 확장에는 **데이터** 탭, **그래프** 탭 및 **진단** 탭이 포함 되어 있습니다. **데이터** 탭에서 Spark 작업의 입력 및 출력 데이터를 확인할 수 있습니다. **그래프** 탭에서 데이터 흐름을 확인 하 고 작업 그래프를 재생할 수 있습니다. **진단** 탭에서 **데이터 기울이기**, **시간 오차**및 **실행자 사용 현황 분석** 기능을 참조할 수 있습니다.

## <a name="get-access-to-the-spark-history-server"></a>Spark 기록 서버에 대 한 액세스 권한 얻기

Spark 기록 서버는 완료 되 고 Spark 응용 프로그램을 실행 하는 웹 UI입니다. Azure Portal 또는 URL에서 열 수 있습니다.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Azure Portal에서 Spark 기록 서버 웹 UI를 엽니다.

1. [Azure Portal](https://portal.azure.com/)에서 Spark 클러스터를 엽니다. 자세한 내용은 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요.
2. **클러스터 대시보드에서** **Spark 기록 서버**를 선택 합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.

    ![Azure Portal에서 Spark 기록 서버를 시작 합니다.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark 기록 서버")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>URL로 Spark 기록 서버 웹 UI 열기

로 `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`이동 하 여 Spark 기록 서버를 엽니다. 여기서 **CLUSTERNAME** 은 spark 클러스터의 이름입니다.

Spark 기록 서버 웹 UI는 다음 이미지와 유사 하 게 보일 수 있습니다.

![Spark 기록 서버 페이지입니다.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Spark 기록 서버에서 데이터 탭 사용

작업 ID를 선택한 다음 도구 메뉴에서 **데이터** 를 선택 하 여 데이터 뷰를 표시 합니다.

+ 개별 탭을 선택 하 여 **입력**, **출력**및 **테이블 작업** 을 검토 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지의 데이터 탭](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ **복사** 단추를 선택 하 여 모든 행을 복사 합니다.

    ![Spark 응용 프로그램 페이지에서 데이터를 복사 합니다.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 모든 데이터를로 저장 합니다. **Csv 파일** 을 선택 합니다.

    ![데이터를로 저장 합니다. Spark 응용 프로그램에 대 한 데이터 페이지에서 CSV 파일을 시작 합니다.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ **검색** 필드에 키워드를 입력 하 여 데이터를 검색 합니다. 검색 결과가 즉시 표시 됩니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지에서 데이터를 검색 합니다.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 테이블을 정렬 하려면 열 머리글을 선택 합니다. 더하기 기호를 선택 하 여 행을 확장 하 고 자세한 정보를 표시 합니다. 빼기 기호를 선택 하 여 행을 축소 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지의 데이터 테이블입니다.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 오른쪽의 **부분 다운로드** 단추를 선택 하 여 단일 파일을 다운로드 합니다. 선택한 파일은 로컬에서 다운로드 됩니다. 파일이 더 이상 존재 하지 않는 경우 오류 메시지를 표시 하는 새 탭이 열립니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지의 데이터 다운로드 행입니다.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 다운로드 메뉴에서 확장 되는 **전체 경로 복사** 또는 **상대 경로 복사** 옵션을 선택 하 여 전체 경로나 상대 경로를 복사 합니다. Azure Data Lake Storage 파일의 경우 **Azure Storage 탐색기에서 열기** 를 선택 하 Azure Storage 탐색기 시작 하 고 로그인 한 후 폴더를 찾습니다.

    ![Spark 응용 프로그램의 데이터 페이지에서 전체 경로를 복사 하 고 상대 경로를 복사 합니다.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 단일 페이지에 표시할 행이 너무 많으면 테이블 아래쪽의 페이지 번호를 선택 하 여 탐색 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지의 페이지 번호입니다.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 자세한 내용을 보려면 **Spark 응용 프로그램에 대 한 데이터** 옆에 있는 물음표를 마우스로 가리키고 선택 하 여 도구 설명을 표시 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지에서 자세한 정보를 확인 하세요.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  문제에 대 한 피드백을 보내려면 **사용자 의견 제공**을 선택 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지의 피드백을 제공 합니다.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Spark 기록 서버에서 그래프 탭 사용

+ 작업 ID를 선택한 다음 도구 메뉴에서 **그래프** 를 선택 하 여 작업 그래프를 표시 합니다. 기본적으로 그래프에는 모든 작업이 표시 됩니다. **작업 ID** 드롭다운 메뉴를 사용 하 여 결과를 필터링 합니다.

    ![Spark 응용 프로그램 & 작업 그래프 페이지의 작업 ID 드롭다운 메뉴](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **진행률** 은 기본적으로 선택 됩니다. **표시** 드롭다운 메뉴에서 **읽기** 또는 **쓰기** 를 선택 하 여 데이터 흐름을 확인 합니다.

    ![Spark 응용 프로그램 & 작업 그래프 페이지에서 데이터 흐름을 확인 합니다.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ 각 작업의 배경색은 열 지도에 해당 합니다.

   ![Spark 응용 프로그램 & 작업 그래프 페이지의 열 지도입니다.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |색상 |Description |
    |---|---|
    |녹색|작업이 완료되었습니다.|
    |주황색|태스크가 실패 했지만 작업의 최종 결과에는 영향을 주지 않습니다. 이러한 작업에는 나중에 성공할 수 있는 중복 또는 다시 시도 인스턴스가 있습니다.|
    |파랑|작업이 실행 중입니다.|
    |흰색|작업이 실행 대기 중이거나 단계를 건너뛰었습니다.|
    |빨강|작업이 실패했습니다.|

     ![Spark 응용 프로그램 & 작업 그래프 페이지에서 작업을 실행 합니다.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     건너뛴 단계는 흰색으로 표시 됩니다.
    ![Spark 응용 프로그램 & 작업 그래프 페이지의 건너뛴 작업입니다.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 응용 프로그램 & 작업 그래프 페이지의 실패 한 작업입니다.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > 완료 된 작업에 대해 재생을 사용할 수 있습니다. **재생** 단추를 선택 하 여 작업을 다시 실행 합니다. 언제 든 지 중지 단추를 선택 하 여 작업을 중지 합니다. 작업을 재생할 때 각 작업은 색으로 상태를 표시 합니다. 불완전 한 작업에 대해서는 재생이 지원 되지 않습니다.

+ 작업 그래프를 확대 하거나 축소 하려면 스크롤 하거나 [ **맞추기** ]를 선택 하 여 화면에 맞게 설정 합니다.

    ![Spark 응용 프로그램 & 작업 그래프 페이지에서 크기에 맞게를 선택 합니다.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 작업이 실패 하면 그래프 노드 위로 마우스를 이동 하 여 도구 설명을 확인 한 다음 단계를 선택 하 여 새 페이지에서 엽니다.

    ![Spark 응용 프로그램 & 작업 그래프 페이지에서 도구 설명을 봅니다.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 작업이 다음 조건을 충족 하는 경우 Spark 응용 프로그램 & 작업 그래프 페이지에서 단계는 도구 설명 및 작은 아이콘을 표시 합니다.
  + 데이터 오차: 데이터 읽기 크기 >이 단계 내의 모든 태스크에 대 한 평균 데이터 읽기 크기 * 2 *및* 데이터 읽기 크기 > 10mb입니다.
  + 시간 오차: 실행 시간 >이 단계 내 모든 작업의 평균 실행 시간 * 2 *및* 실행 시간 > 2 분입니다.

    ![Spark 응용 프로그램 & 작업 그래프 페이지의 기울어진 작업 아이콘입니다.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 작업 그래프 노드에는 각 단계에 대 한 다음 정보가 표시 됩니다.
  + ID
  + 이름 또는 설명
  + 총 작업 수
  + 읽은 데이터: 입력 크기의 합계와 무작위 읽기 크기
  + 데이터 쓰기: 출력 크기와 순서 섞기 쓰기 크기의 합계입니다.
  + 실행 시간: 마지막 시도의 시작 시간 및 마지막 시도의 완료 시간 사이의 시간입니다.
  + 행 개수: 입력 레코드의 합계, 출력 레코드, 읽기 레코드 순서 섞기 및 쓰기 레코드 순서 섞기
  + 진행

    > [!NOTE]  
    > 기본적으로 작업 그래프 노드에는 각 단계에 대 한 마지막 시도의 정보가 표시 됩니다 (단계 실행 시간 제외). 그러나 재생 하는 동안 작업 그래프 노드에는 각 시도에 대 한 정보가 표시 됩니다.

    > [!NOTE]  
    > 데이터 읽기 및 데이터 쓰기 크기의 경우 1MB = 1000 KB = 1000 * 1000 바이트를 사용 합니다.

+ **사용자 의견 제공**을 선택 하 여 문제에 대 한 피드백을 보내 주세요.

    ![Spark 응용 프로그램 & 작업 그래프 페이지의 피드백 옵션입니다.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Spark 기록 서버에서 진단 탭 사용

작업 ID를 선택 하 고 도구 메뉴에서 **진단** 을 선택 하 여 작업 진단 보기를 표시 합니다. **진단** 탭에는 **데이터 기울이기**, **시간 오차**및 **실행자 사용 분석이**포함 되어 있습니다.

+ 각각 탭을 선택 하 여 **데이터 기울이기**, **시간 오차**및 **실행자 사용 분석** 을 검토 합니다.

    ![진단 탭의 데이터 기울이기 탭](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>데이터 기울이기

**데이터 기울이기** 탭을 선택 합니다. 지정 된 매개 변수를 기준으로 해당 하는 기울어진 작업이 표시 됩니다.

#### <a name="specify-parameters"></a>매개 변수 지정

**매개 변수 지정** 섹션에는 데이터 오차를 검색 하는 데 사용 되는 매개 변수가 표시 됩니다. 기본 규칙은 다음과 같습니다. 작업 데이터 읽기가 평균 태스크 데이터를 읽은 3 시간 보다 크고 작업 데이터 읽기가 10mb를 초과 합니다. 기울어진 태스크에 대 한 고유 규칙을 정의 하려는 경우 매개 변수를 선택할 수 있습니다. **기울어진 스테이지** 및 **기울이기 차트** 섹션은 그에 따라 업데이트 됩니다.

#### <a name="skewed-stage"></a>기울어진 스테이지

**기울어진 단계** 섹션에는 지정 된 조건을 충족 하는 기울어진 태스크가 있는 단계가 표시 됩니다. 스테이지에 기울어진 태스크가 두 개 이상 있을 경우 **기울어진 스테이지** 섹션에는 가장 왜곡 된 작업 (즉, 데이터 기울이기에 가장 큰 데이터)만 표시 됩니다.

![진단 탭 내에서 데이터 기울이기 탭의 보기가 큽니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>차트 기울이기

**기울이기 단계** 테이블에서 행을 선택 하면 데이터 읽기 및 실행 시간을 기반으로 더 많은 작업 배포 세부 **정보가 표시 됩니다** . 기울어진 작업은 빨간색으로 표시 되 고 일반 작업은 파란색으로 표시 됩니다. 성능 고려 사항을 위해 차트는 최대 100 개의 샘플 작업을 표시 합니다. 작업 세부 정보는 오른쪽 아래 패널에 표시 됩니다.

![Spark UI의 10 단계에 대 한 기울이기 차트입니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>시간 기울이기

**시간 기울이기** 탭에는 작업 실행 시간을 기준으로 기울어진 작업이 표시됩니다.

#### <a name="specify-parameters"></a>매개 변수 지정

**매개 변수 지정** 섹션에는 시간 오차를 검색 하는 데 사용 되는 매개 변수가 표시 됩니다. 기본 규칙은 작업 실행 시간이 평균 실행 시간의 3 배 보다 크고 태스크 실행 시간이 30 초를 초과 하는 것입니다. 필요에 따라 매개 변수를 변경할 수 있습니다. **기울어진 스테이지** 및 **기울이기 차트** 는 **데이터 기울이기** 탭에서와 마찬가지로 해당 단계 및 작업 정보를 표시 합니다.

**시간 오차**를 선택 하면 **매개 변수 지정** 섹션에 설정 된 매개 변수에 따라 필터링 된 결과가 **기울어진 단계** 섹션에 표시 됩니다. **기울어진 단계** 섹션에서 한 항목을 선택 하면 해당 차트가 세 번째 섹션에서 초안 되 고 작업 세부 정보는 오른쪽 아래 패널에 표시 됩니다.

![진단 탭 내의 시간 오차 탭입니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>실행자 사용 분석 그래프

**Executor 사용 그래프** 는 작업의 실제 실행 기 할당 및 실행 상태를 표시 합니다.  

**실행자 사용 현황 분석**을 선택 하면 실행자 사용에 대 한 네 가지 다른 곡선이 초안 됩니다. **할당**된 실행 기, **실행 실행자**, **유휴 실행자**및 **Max executor 인스턴스**. **추가 된 실행** 기 또는 **실행자 제거** 이벤트는 할당 된 실행자를 늘리거나 줄입니다. **작업** 탭에서 **이벤트 타임 라인** 을 확인 하 여 더 비교할 수 있습니다.

![진단 탭에 있는 Executor 사용 분석 탭](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

색 아이콘을 선택 하 여 모든 초안에서 해당 콘텐츠를 선택 하거나 선택 취소 합니다.

 ![실행자 사용 분석 탭에서 차트를 선택 합니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>FAQ

### <a name="how-do-i-revert-to-the-community-version"></a>커뮤니티 버전으로 되돌릴 어떻게 할까요? 있나요?

커뮤니티 버전으로 되돌리려면 다음 단계를 수행 합니다.

1. Ambari에서 클러스터를 엽니다.
1. **Spark2** > **Configs**로 이동 합니다.
1. **사용자 지정 spark2-기본값**을 선택 합니다.
1. **속성 추가**...를 선택 합니다.
1. Spark. ui&gt; **enabled = false**를 추가 하 고 저장 합니다.
1. 이제 속성을 **false**로 설정합니다.
1. **저장** 을 선택하여 구성을 저장합니다.

    ![Apache Ambari의 기능을 해제 합니다.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 왼쪽 패널에서 **Spark2** 를 선택 합니다. 그런 다음 **요약** 탭에서 **Spark2 History 서버**를 선택 합니다.

    ![Apache Ambari의 요약 뷰입니다.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Spark 기록 서버를 다시 시작 하려면 **Spark2 History 서버**오른쪽에 있는 **시작 됨** 단추를 선택한 다음 드롭다운 메뉴에서 **다시 시작** 을 선택 합니다.

    ![Apache Ambari에서 Spark 기록 서버를 다시 시작 합니다.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Spark 기록 서버 웹 UI를 새로 고칩니다. 그러면 커뮤니티 버전으로 되돌아갑니다.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Spark 기록 서버 이벤트를 업로드 하 여 문제를 보고 어떻게 할까요??

Spark 기록 서버에서 오류가 발생 하는 경우 다음 단계를 수행 하 여 이벤트를 보고 합니다.

1. Spark 기록 서버 웹 UI에서 **다운로드** 를 선택 하 여 이벤트를 다운로드 합니다.

    ![Spark 기록 서버 UI에서 이벤트를 다운로드 합니다.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. **Spark 응용 프로그램 & 작업 그래프** 페이지에서 **피드백 제공** 을 선택 합니다.

    ![Spark 응용 프로그램 & 작업 그래프 페이지에서 피드백 제공](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 오류에 대 한 제목 및 설명을 제공 합니다. 그런 다음 .zip 파일을 편집 필드로 끌어 **새 문제 제출**을 선택 합니다.

    ![새 문제를 업로드 하 고 제출 합니다.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>핫픽스 시나리오에서 jar 파일을 업그레이드 어떻게 할까요??

핫픽스를 사용 하 여 업그레이드 하려면 다음 스크립트를 사용 합니다 .이 스크립트는에서 `spark-enhancement.jar*`업그레이드 됩니다.

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>사용

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>예제

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Azure Portal에서 bash 파일 사용

1. [Azure Portal](https://ms.portal.azure.com)를 시작 하 고 클러스터를 선택 합니다.
2. 다음 매개 변수를 사용 하 여 [스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md) 을 완료 합니다.

    |속성 |값 |
    |---|---|
    |스크립트 유형|- 사용자 지정|
    |속성|업그레이드 Jar|
    |Bash 스크립트 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |노드 유형|헤드, 작업자|
    |매개 변수|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![스크립트 동작 Azure Portal 제출](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>알려진 문제

+ 현재 Spark 기록 서버는 Spark 2.3 및 2.4에 대해서만 작동 합니다.

+ RDD를 사용 하는 입력 및 출력 데이터는 **데이터** 탭에 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

+ [HDInsight에서 Apache Spark 클러스터용 리소스 관리](apache-spark-resource-manager.md)
+ [Apache Spark 설정 구성](apache-spark-settings.md)

## <a name="feedback"></a>사용자 의견

이 도구를 사용할 때 의견이 있거나 모든 문제를 해결 하는 경우 ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com))로 전자 메일을 보내 주세요.
