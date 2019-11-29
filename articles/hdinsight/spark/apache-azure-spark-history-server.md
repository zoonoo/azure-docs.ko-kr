---
title: 앱을 디버그 하는 확장 Spark 기록 서버-Azure HDInsight
description: 확장된 Spark 기록 서버를 사용하여 Spark 애플리케이션 디버그 및 진단 - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561819"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>확장된 Apache Spark 기록 서버를 사용하여 Apache Spark 애플리케이션 디버그 및 진단

이 문서에서는 확장된 Apache Spark 기록 서버를 사용하여 완료되어 실행되는 Spark 애플리케이션을 디버그 및 진단하는 방법에 대한 지침을 제공합니다. 확장에는 데이터 탭 및 그래프 탭과 진단 탭이 포함 됩니다. 사용자는 **데이터** 탭에서 Spark 작업의 입력 및 출력 데이터를 확인할 수 있습니다. **그래프** 탭에서 사용자는 데이터 흐름을 확인하고 작업 그래프를 재생할 수 있습니다. **진단** 탭에서 사용자는 **데이터 기울이기**, **시간 오차**및 **실행자 사용량 분석**을 참조할 수 있습니다.

## <a name="get-access-to-apache-spark-history-server"></a>Apache Spark 기록 서버에 액세스

Apache Spark 기록 서버는 완료되고 실행되는 Spark 애플리케이션의 웹 UI입니다.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Azure Portal에서 Apache Spark 기록 서버 웹 UI 열기

1. [Azure Portal](https://portal.azure.com/)에서 Spark 클러스터를 엽니다. 자세한 내용은 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요.
2. **클러스터 대시보드에서** **Spark 기록 서버**를 선택 합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.

    ![포털 시작 Spark 기록 서버](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark 기록 서버")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>URL을 통해 Spark 기록 서버 웹 UI 열기

`https://CLUSTERNAME.azurehdinsight.net/sparkhistory`로 이동 하 여 Spark 기록 서버를 엽니다. 여기서 CLUSTERNAME은 Spark 클러스터의 이름입니다.

Spark 기록 서버 웹 UI는 다음과 유사 하 게 보일 수 있습니다.

![HDInsight Spark 기록 서버](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Spark 기록 서버의 데이터 탭

작업 ID를 선택 하 고 도구 메뉴에서 **데이터** 를 선택 하 여 데이터 뷰를 가져옵니다.

+ 탭을 별도로 선택 하 여 **입력**, **출력**및 **테이블 작업** 을 검토 합니다.

    ![Spark 응용 프로그램 탭에 대 한 데이터](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 단추 **복사**를 선택 하 여 모든 행을 복사 합니다.

    ![Spark 응용 프로그램 복사에 대 한 데이터](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 단추 **csv**를 선택 하 여 모든 데이터를 csv 파일로 저장 합니다.

    ![Spark 응용 프로그램 저장에 대 한 데이터](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ **검색** 필드에서 키워드를 입력하여 검색하면 검색 결과가 즉시 표시됩니다.

    ![Spark 응용 프로그램 검색에 대 한 데이터](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 테이블을 정렬 하려면 열 머리글을 선택 하 고 더하기 기호를 선택 하 여 행을 확장 하 여 세부 정보를 표시 하거나 빼기 기호를 선택 하 여 행을 축소 합니다.

    ![Spark 응용 프로그램 테이블에 대 한 데이터](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 오른쪽에 있는 단추 **부분 다운로드** 를 선택 하 여 단일 파일을 다운로드 합니다. 그러면 선택한 파일이 로컬에 다운로드 됩니다. 파일이 더 이상 존재 하지 않는 경우 오류 메시지를 표시 하는 새 탭이 열립니다.

    ![Spark 응용 프로그램 다운로드 행에 대 한 데이터](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 다운로드 메뉴에서 확장되는 **전체 경로 복사** 또는 **상대 경로 복사**를 선택하여 전체 경로 또는 상대 경로를 복사합니다. Azure data lake storage 파일의 경우 **에서 열기를 Azure Storage 탐색기** 하 여 Azure Storage 탐색기를 시작 하 고 로그인 할 때 폴더를 찾습니다.

    ![Spark 응용 프로그램 복사 경로에 대 한 데이터](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 한 페이지에 표시할 행이 너무 많은 경우 페이지 아래에 있는 숫자를 선택 하 여 페이지를 탐색 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 페이지](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 데이터 옆에 있는 물음표를 가리켜 도구 설명을 표시 하거나 물음표를 선택 하 여 자세한 정보를 확인 합니다.

    ![Spark 응용 프로그램에 대 한 데이터 추가 정보](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ **피드백 제공**을 클릭하여 문제와 함께 피드백을 보냅니다.

    ![Spark graph에서 피드백을 다시 제공 합니다.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark 기록 서버의 그래프 탭

작업 그래프 보기를 가져오려면 작업 ID를 선택한 다음, 도구 메뉴에서 **Graph**를 클릭합니다.

+ 생성 된 작업 그래프에서 작업 개요를 검토 합니다.

+ 기본적으로 모든 작업이 표시되며, **작업 ID**에서 필터링할 수 있습니다.

    ![Spark 응용 프로그램 및 작업 그래프 작업 ID](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 기본적으로 **진행률**을 선택하면 사용자는 **표시**의 드롭다운 목록에서 **읽기/쓰기**를 선택하여 데이터 흐름을 확인할 수 있습니다.

    ![Spark 응용 프로그램 및 작업 그래프 표시](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    열 지도를 보여주는 컬러판 그래프 노드 표시입니다.

    ![Spark 응용 프로그램 및 작업 그래프 열 지도](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ **재생** 단추를 선택 하 여 작업을 재생 하 고 중지 단추를 선택 하 여 언제 든 지 중지 합니다. 재생 시 다른 상태를 표시하기 위한 컬러판 작업 표시입니다.

    |컬러 |설명 |
    |---|---|
    |녹색|작업이 완료되었습니다.|
    |Orange|실패 한 작업의 인스턴스는 작업의 최종 결과에 영향을 주지 않습니다. 이러한 작업은 나중에 성공할 수 있는 인스턴스를 복제하거나 다시 시도합니다.|
    |파랑|작업이 실행 중입니다.|
    |흰색|작업이 실행을 위해 대기 중이거나 단계를 건너뛰었습니다.|
    |빨강|작업이 실패했습니다.|

    ![Spark 응용 프로그램 및 작업 그래프 색 샘플, 실행](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    건너뛴 단계는 흰색으로 표시됩니다.
    Spark 응용 프로그램 및 작업 그래프 색 샘플 ![건너뜀](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 응용 프로그램 및 작업 그래프 색 샘플, 실패](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > 각 작업에 대한 재생이 허용됩니다. 불완전한 작업의 경우 재생이 지원되지 않습니다.

+ 마우스를 스크롤하여 작업 그래프를 확대/축소하거나 **크기에 맞게**를 클릭하여 화면에 맞춥니다.

    ![Spark 응용 프로그램 및 작업 그래프 크기에 맞게 확대](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 실패한 작업이 있는 경우 도구 설명을 보려면 그래프 노드를 마우스로 가리키고 단계를 클릭하여 단계 페이지를 엽니다.

    ![Spark 응용 프로그램 및 작업 그래프 도구 설명](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 작업 그래프 탭에서 아래 조건을 충족하는 작업이 있을 경우 단계에 도구 설명과 작은 아이콘이 표시됩니다.
  + 데이터 편향: 읽은 데이터 크기 > 이 단계 내 모든 작업에서 읽은 데이터의 평균 크기 * 2 및 읽은 데이터 크기 > 10MB
  + 시간 편향: 실행 시간 > 이 단계 내 모든 작업의 평균 실행 시간 * 2와 실행 시간 > 2분

    ![Spark 응용 프로그램 및 작업 그래프 기울이기 아이콘](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 작업 그래프 노드는 다음과 같은 각 단계의 정보를 표시합니다.
  + ID.
  + 이름 또는 설명.
  + 총 작업 수.
  + 읽은 데이터: 입력 크기 및 순서 섞기 읽기 크기의 합계입니다.
  + 데이터 쓰기: 출력 크기 및 순서 섞기 쓰기 크기의 합계입니다.
  + 실행 시간: 첫 번째 시도의 시작 시간과 마지막 시도의 완료 시간 간의 시간입니다.
  + 행 개수: 입력 레코드, 출력 레코드, 순서 섞기 읽기 레코드 및 순서 섞기 쓰기 레코드의 합계입니다.
  + 진행률.

    > [!NOTE]  
    > 기본적으로 작업 그래프 노드는 각 단계(스테이지 실행 시간 제외)의 마지막 시도에서 정보를 표시하지만 재생 중 노드 그래프는 각 시도에 대한 정보를 표시합니다.

    > [!NOTE]  
    > 읽기 및 쓰기의 데이터 크기에 대해 1MB = 1000 KB = 1000 * 1000바이트를 사용합니다.

+ **사용자 의견 제공**을 선택 하 여 문제에 대 한 피드백을 보내 주세요.

    ![Spark 응용 프로그램 및 작업 그래프 피드백](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark 기록 서버의 진단 탭

작업 ID를 선택 하 고 도구 메뉴에서 **진단** 을 선택 하 여 작업 진단 보기를 가져옵니다. 진단 탭에는 **데이터 편향**, **시간 편향** 및 **실행기 사용량 분석**이 있습니다.

+ 각각 탭을 선택 하 여 **데이터 기울이기**, **시간 오차**및 **실행자 사용 분석** 을 검토 합니다.

    ![SparkUI 진단 데이터 기울이기 탭 다시](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>데이터 기울이기

**데이터 기울이기** 탭을 선택 하면 지정 된 매개 변수를 기준으로 해당 하는 기울어진 태스크가 표시 됩니다.

+ **매개 변수 지정** -첫 번째 섹션은 데이터 오차를 검색 하는 데 사용 되는 매개 변수를 표시 합니다. 기본 제공 규칙: 태스크 데이터 읽기는 평균 태스크 데이터 읽기의 3 배 이상이 고, 태스크 데이터 읽기는 10mb 보다 큽니다. 편향된 작업에 사용자 지정 규칙을 정의하려면 매개 변수, **편향된 단계**를 선택하면 됩니다. 그러면 **편향 차트** 섹션이 적절히 새로 고쳐집니다.

+ **기울어진 스테이지** -두 번째 섹션은 위에 지정 된 조건을 충족 하는 기울어진 작업을 포함 하는 단계를 표시 합니다. 스테이지에 기울어진 태스크가 두 개 이상 있을 경우 기울어진 스테이지 테이블에는 가장 왜곡 된 작업 (예: 데이터 오차를 위한 가장 큰 데이터)만 표시 됩니다.

    ![sparkui 진단 데이터 기울이기 탭](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **편향 차트** – 편향 단계 테이블의 행이 선택되면 데이터 읽기 및 실행 시간에 따라 편향 차트에 더 많은 작업 분포 세부 정보가 표시됩니다. 편향된 작업은 빨간색으로 표시되며 일반 작업은 파란색으로 표시됩니다. 성능을 고려하여 차트에는 최대 100개의 샘플 작업만 표시됩니다. 작업 세부 정보는 오른쪽 아래 패널에 표시됩니다.

    ![10 단계에 대 한 sparkui 기울이기 차트](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>시간 편향

**시간 편향** 탭에는 작업 실행 시간에 따라 편향된 작업이 표시됩니다.

+ **매개 변수 지정** -첫 번째 섹션에는 시간 오차를 검색 하는 데 사용 되는 매개 변수가 표시 됩니다. 시간 오차를 검색 하는 기본 기준은 작업 실행 시간이 평균 실행 시간의 3 배 보다 크고 태스크 실행 시간이 30 초를 초과 하는 것입니다. 필요에 따라 매개 변수를 변경할 수 있습니다. **편향된 단계**와 **편향 차트**에는 위의 **데이터 편향** 탭과 같은 해당 단계 및 작업 정보가 표시됩니다.

+ **시간 오차**를 선택 하 고 필터링 된 결과는 **매개 변수 지정**섹션에 설정 된 매개 변수에 따라 **기울어진 단계** 섹션에 표시 됩니다. **기울어진 단계** 섹션에서 한 항목을 선택 하 고 해당 차트가 section3에서 초안을 만든 다음 오른쪽 아래 패널에 작업 세부 정보가 표시 됩니다.

    ![sparkui 진단 시간 오차 섹션](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>실행기 사용량 분석

실행기 사용량 그래프는 Spark 작업의 실제 실행기 할당 및 실행 상태를 시각화합니다.  

+ **Executor 사용 분석**을 선택 합니다. 그러면 **할당 된 실행자**, **실행 실행자**, **유휴 실행자**및 **Max executor 인스턴스**를 포함 하 여 executor 사용에 대 한 네 가지 유형의 곡선이 초안입니다. 할당된 실행기와 관련하여 각 "실행기 추가됨" 또는 "실행기 제거됨" 이벤트가 할당된 실행기를 늘리거나 줄이며, “작업" 탭의 "이벤트 타임라인"에서 자세한 비교 정보를 확인할 수 있습니다.

    ![sparkui 진단 실행자 탭](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ 색 아이콘을 선택 하 여 모든 초안에서 해당 콘텐츠를 선택 하거나 선택 취소 합니다.

    ![sparkui 진단 차트 선택](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>FAQ

### <a name="1-revert-to-community-version"></a>1. 커뮤니티 버전으로 되돌리기

커뮤니티 버전으로 되돌리려면 다음 단계를 수행합니다.

1. Ambari에서 클러스터를 엽니다.
1. **Spark2** > **Configs** > **Custom Spark2-defaults**로 이동 합니다.
1. **속성 추가 ...** 를 선택 하 고, 추가 기능을 추가 합니다. **사용 = false**, 저장을 선택 합니다.
1. 이제 속성을 **false**로 설정합니다.
1. **저장** 을 선택하여 구성을 저장합니다.

    ![Apache Ambari 기능 해제](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 왼쪽 패널에서 **Spark2** 를 선택 하 고 **요약** 탭에서 **Spark2 History 서버**를 선택 합니다.

    ![Apache Ambari Spark2 요약 뷰](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. **Spark2 기록 서버** **다시 시작** 을 선택 하 여 기록 서버를 다시 시작 합니다.

    ![Apache Ambari Spark2 기록 다시 시작](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. Spark 기록 서버 웹 UI를 새로 고침하면 커뮤니티 버전으로 되돌려집니다.

### <a name="2-upload-history-server-event"></a>2. history 서버 이벤트 업로드

기록 서버 오류가 발생한 경우 이벤트를 제공하기 위한 단계를 수행합니다.

1. 기록 서버 웹 UI에서 **다운로드** 를 선택 하 여 이벤트를 다운로드 합니다.

    ![Spark2 History Server 다운로드](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 데이터/그래프 탭에서 **피드백 제공** 을 선택 합니다.

    ![Spark graph 사용자 의견 제공](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 오류의 제목 및 설명을 제공하고, zip 파일을 편집 필드를 끌어온 다음, **새 문제 제출**을 클릭합니다.

    ![apache spark 파일 문제 예](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. 핫픽스 시나리오에 대 한 jar 파일 업그레이드

핫픽스를 사용하여 업그레이드하려는 경우 아래 스크립트를 사용하여 spark-enhancement.jar*을 업그레이드합니다.

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

**사용 현황**

`upgrade_spark_enhancement.sh https://${jar_path}`

**예제**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Azure Portal에서 Bash 파일을 사용하려면**

1. [Azure Portal](https://ms.portal.azure.com)를 시작 하 고 클러스터를 선택 합니다.
2. 다음 매개 변수를 사용 하 여 [스크립트 동작](../hdinsight-hadoop-customize-cluster-linux.md) 을 완료 합니다.

    |자산 |Value |
    |---|---|
    |스크립트 유형|- 사용자 지정|
    |name|업그레이드 Jar|
    |Bash 스크립트 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |노드 유형|헤드, 작업자|
    |parameters|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![스크립트 동작 Azure Portal 제출](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>알려진 문제

+ 현재는 Spark 2.3 및 2.4 클러스터에 대해서만 작동 합니다.

+ RDD를 사용 하는 입력/출력 데이터는 데이터 탭에 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

+ [HDInsight에서 Apache Spark 클러스터용 리소스 관리](apache-spark-resource-manager.md)
+ [Apache Spark 설정 구성](apache-spark-settings.md)

## <a name="contact-us"></a>문의하기

의견이 있거나이 도구를 사용할 때 문제가 발생 하는 경우 ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com))에서 전자 메일을 보냅니다.
