---
title: 아파치 스파크 히스토리 서버의 확장된 기능을 사용하여 앱 디버깅 - Azure HDInsight
description: 아파치 스파크 히스토리 서버의 확장된 기능을 사용하여 스파크 응용 프로그램인 Azure HDInsight를 디버깅하고 진단할 수 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548937"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>아파치 스파크 히스토리 서버의 확장된 기능을 사용하여 스파크 애플리케이션을 디버깅하고 진단합니다.

이 문서에서는 아파치 스파크 히스토리 서버의 확장된 기능을 사용하여 완료되었거나 실행 중인 Spark 응용 프로그램을 디버깅하고 진단하는 방법을 보여 주며 있습니다. 확장에는 **데이터** 탭, **그래프** 탭 및 **진단** 탭이 포함됩니다. **데이터** 탭에서 Spark 작업의 입력 및 출력 데이터를 확인할 수 있습니다. **그래프** 탭에서 데이터 흐름을 확인하고 작업 그래프를 재생할 수 있습니다. **진단** 탭에서 **데이터 기울이기,** 시간 **기울이기**및 **실행기 사용 분석** 기능을 참조할 수 있습니다.

## <a name="get-access-to-the-spark-history-server"></a>스파크 히스토리 서버에 액세스하기

스파크 기록 서버는 Spark 응용 프로그램을 완료하고 실행하기 위한 웹 UI입니다. Azure 포털 또는 URL에서 열 수 있습니다.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Azure 포털에서 스파크 기록 서버 웹 UI 열기

1. [Azure Portal](https://portal.azure.com/)에서 Spark 클러스터를 엽니다. 자세한 내용은 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요.
2. **클러스터 대시보드에서** **스파크 기록 서버를**선택합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다.

    ![Azure 포털에서 스파크 기록 서버를 시작합니다.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark 기록 서버")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>URL로 스파크 히스토리 서버 웹 UI 열기

**클러스터이름이 스파크** 클러스터의 `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`이름인 을 탐색하여 스파크 기록 서버를 엽니다.

스파크 히스토리 서버 웹 UI는 이 이미지와 유사할 수 있습니다.

![스파크 히스토리 서버 페이지입니다.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>스파크 기록 서버의 데이터 탭 사용

작업 ID를 선택한 다음 도구 메뉴에서 **데이터를** 선택하여 데이터 보기를 확인합니다.

+ 개별 **탭을**선택하여 **입력,** 출력 및 **테이블 작업을** 검토합니다.

    ![스파크 응용 프로그램 데이터 페이지의 데이터 탭입니다.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ **복사** 단추를 선택하여 모든 행을 복사합니다.

    ![Spark 응용 프로그램 페이지에서 데이터를 복사합니다.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 모든 데이터를 로 저장합니다. CSV 버튼을 선택하여 **CSV** 파일.

    ![데이터를 로 저장합니다. 스파크 응용 프로그램에 대한 데이터 페이지에서 CSV 파일.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ **검색** 필드에 키워드를 입력하여 데이터를 검색합니다. 검색 결과가 즉시 표시됩니다.

    ![스파크 응용 프로그램 데이터 페이지에서 데이터를 검색합니다.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 테이블을 정렬하려면 열 헤더를 선택합니다. 더하기 기호를 선택하여 행을 확장하여 자세한 내용을 표시합니다. 행을 축소하려면 빼기 기호를 선택합니다.

    ![스파크 응용 프로그램에 대한 데이터 페이지의 데이터 테이블입니다.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 오른쪽에 있는 부분 다운로드 버튼을 선택하여 단일 파일을 **다운로드합니다.** 선택한 파일이 로컬로 다운로드됩니다. 파일이 더 이상 존재하지 않으면 오류 메시지를 표시하는 새 탭이 열립니다.

    ![스파크 응용 프로그램 용 데이터 페이지의 데이터 다운로드 행입니다.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 다운로드 메뉴에서 확장되는 전체 경로 **복사** 또는 **상대 경로 복사** 옵션을 선택하여 전체 경로 또는 상대 경로를 복사합니다. Azure Data Lake Storage 파일의 경우 **Azure 저장소 탐색기에서 열기를** 선택하여 Azure 저장소 탐색기를 시작하고 로그인 후 폴더를 찾습니다.

    ![스파크 응용 프로그램 용 데이터 페이지에서 전체 경로 복사 및 상대 경로 복사 옵션 입니다.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 단일 페이지에 표시할 행이 너무 많은 경우 테이블 하단에 있는 페이지 번호를 선택하여 탐색합니다.

    ![스파크 응용 프로그램 데이터 페이지의 페이지 번호입니다.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 자세한 내용은 **Spark 응용 프로그램에 대한 데이터** 옆에 있는 물음표를 마우스 로 이동하거나 선택하여 도구 설명에 표시합니다.

    ![스파크 응용 프로그램에 대한 데이터 페이지에서 자세한 정보를 가져옵니다.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  문제에 대한 피드백을 보내려면 **피드백 제공을 선택합니다.**

    ![스파크 응용 프로그램에 대한 데이터 페이지에서 피드백을 제공합니다.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>스파크 기록 서버에서 그래프 탭 사용

+ 작업 ID를 선택한 다음 도구 메뉴에서 **그래프를** 선택하여 작업 그래프를 확인합니다. 기본적으로 그래프에는 모든 작업이 표시됩니다. **작업 ID** 드롭다운 메뉴를 사용하여 결과를 필터링합니다.

    ![스파크 응용 프로그램 & 구인 그래프 페이지의 작업 ID 드롭다운 메뉴입니다.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **진행률을** 기본적으로 선택합니다. **표시** 드롭다운 메뉴에서 **읽기** 또는 **기록을** 선택하여 데이터 흐름을 확인합니다.

    ![스파크 애플리케이션 & 구인 그래프 페이지에서 데이터 흐름을 확인합니다.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ 각 작업의 배경색은 히트 맵에 해당합니다.

   ![스파크 응용 & 구인 그래프 페이지의 히트 맵입니다.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |색 |설명 |
    |---|---|
    |녹색|작업이 완료되었습니다.|
    |주황색|작업이 실패했지만 작업의 최종 결과에는 영향을 주지 않습니다. 이러한 작업에는 나중에 성공할 수 있는 중복 또는 재시도 인스턴스가 있습니다.|
    |파랑|작업이 실행 중입니다.|
    |흰색|작업이 실행 대기 중이거나 단계를 건너뛰었습니다.|
    |빨강|작업이 실패했습니다.|

     ![스파크 응용 프로그램 & 작업 그래프 페이지에서 작업을 실행합니다.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     건너뛴 단계는 흰색으로 표시됩니다.
    ![스파크 응용 프로그램 & 작업 그래프 페이지에서 건너뛴 작업입니다.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![스파크 응용 프로그램 & 작업 그래프 페이지에서 실패한 작업입니다.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > 완료된 작업에 대해 재생을 사용할 수 있습니다. **재생** 버튼을 선택하여 작업을 다시 재생합니다. 중지 단추를 선택하여 언제든지 작업을 중지합니다. 작업이 재생되면 각 작업은 해당 상태를 색상별로 표시합니다. 불완전한 작업에는 재생이 지원되지 않습니다.

+ 스크롤하여 작업 그래프를 확대 또는 축소하거나 **화면에 맞게 확대/축소를** 선택합니다.

    ![스파크 응용 프로그램 & 구인 그래프 페이지에 맞게 확대/축소를 선택합니다.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 작업이 실패하면 그래프 노드 위로 마우스를 가져가 서 도구 설명이 표시되고 스테이지를 선택하여 새 페이지에서 엽니다.

    ![스파크 애플리케이션 & 구인 그래프 페이지에서 도구 설명서를 봅니다.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 작업 & 작업 그래프 스파크 페이지에서 작업이 다음 조건을 충족하는 경우 단계에 도구 설명과 작은 아이콘이 표시됩니다.
  + 데이터 기울이기: 데이터 읽기 크기 > 이 단계 내의 모든 작업의 평균 데이터 읽기 크기 * 2 *및* 데이터 읽기 크기 > 10MB입니다.
  + 시간 왜곡: 실행 시간 > 이 단계 내의 모든 작업의 평균 실행 시간 * 2 *및* 실행 시간 > 2분입니다.

    ![스파크 응용 프로그램 & 작업 그래프 페이지의 비뚤어진 작업 아이콘입니다.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ 작업 그래프 노드는 각 단계에 대한 다음 정보를 표시합니다.
  + ID
  + 이름 또는 설명
  + 총 작업 수
  + 데이터 읽기: 입력 크기와 셔플 읽기 크기의 합계
  + 데이터 쓰기: 출력 크기와 셔플 쓰기 크기의 합계
  + 실행 시간: 첫 번째 시도의 시작 시간과 마지막 시도의 완료 시간 사이의 시간
  + 행 수: 입력 레코드, 출력 레코드, 읽기 레코드 셔플 및 쓰기 레코드 셔플의 합계
  + 진행

    > [!NOTE]  
    > 기본적으로 작업 그래프 노드는 각 단계의 마지막 시도(스테이지 실행 시간 제외)의 정보를 표시합니다. 그러나 재생 중에 작업 그래프 노드에는 각 시도에 대한 정보가 표시됩니다.

    > [!NOTE]  
    > 데이터 읽기 및 데이터 쓰기 크기의 경우 1MB = 1000KB = 1000 * 1000 바이트를 사용합니다.

+ **피드백 제공을**선택하여 문제에 대한 피드백을 보냅니다.

    ![스파크 응용 프로그램 & 구인 그래프 페이지의 피드백 옵션입니다.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>스파크 기록 서버의 진단 탭 사용

작업 ID를 선택한 다음 도구 메뉴에서 **진단을** 선택하여 작업 진단 보기를 확인합니다. **진단** 탭에는 **데이터 기울이기,** **시간 기울이기**및 **실행기 사용 분석이**포함됩니다.

+ 각각 탭을 선택하여 **데이터 기울이기,** **시간 기울이기**및 **실행기 사용 분석을** 검토합니다.

    ![진단 탭 내의 데이터 기울이기 탭입니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>데이터 기울이기

데이터 **기울이기** 탭을 선택합니다. 해당 기울어진 작업은 지정된 매개 변수를 기반으로 표시됩니다.

#### <a name="specify-parameters"></a>매개 변수 지정

**매개변수 지정** 섹션에는 데이터 기울이기를 검색하는 데 사용되는 매개 변수가 표시됩니다. 기본 규칙은 읽기 작업 데이터가 평균 작업 데이터 읽기의 3배보다 크고 읽은 작업 데이터가 10MB보다 큽입니다. 왜곡된 작업에 대한 사용자 고유의 규칙을 정의하려면 매개 변수를 선택할 수 있습니다. **기울어진 스테이지** 및 **스큐 차트** 섹션은 그에 따라 업데이트됩니다.

#### <a name="skewed-stage"></a>비뚤어진 스테이지

**기울어진 단계** 섹션에는 지정된 조건을 충족하는 작업이 기울어진 단계가 표시됩니다. 스테이지에 두 개 이상의 비뚤어진 작업이 있는 경우 **Skewed 단계** 섹션에는 가장 비뚤어진 작업(즉, 데이터 기울이기에 대한 가장 큰 데이터)만 표시됩니다.

![진단 탭 내의 데이터 기울이기 탭의 큰 보기입니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>스큐 차트

**기울이기 스테이지** 테이블에서 행을 선택하면 **데이터** 읽기 및 실행 시간을 기반으로 더 많은 작업 분포 세부 정보가 표시됩니다. 기울어진 작업은 빨간색으로 표시되고 일반 작업은 파란색으로 표시됩니다. 성능 고려를 위해 차트에는 최대 100개의 샘플 작업이 표시됩니다. 작업 세부 정보가 오른쪽 아래 패널에 표시됩니다.

![스파크 UI의 스테이지 10에 대한 스큐 차트입니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>시간 기울이기

**시간 기울이기** 탭에는 작업 실행 시간을 기준으로 기울어진 작업이 표시됩니다.

#### <a name="specify-parameters"></a>매개 변수 지정

**매개변수 지정** 섹션에는 시간 기울이기를 검색하는 데 사용되는 매개 변수가 표시됩니다. 기본 규칙은 작업 실행 시간이 평균 실행 시간의 3배보다 크고 작업 실행 시간이 30초보다 크다는 것입니다. 필요에 따라 매개 변수를 변경할 수 있습니다. **기울어진 스테이지** 및 **기울이기 차트는** **데이터 기울이기** 탭과 마찬가지로 해당 단계 및 작업 정보를 표시합니다.

**시간 기울이기를**선택하면 **매개변수 지정** 섹션에 설정된 매개변수에 따라 필터링된 **결과가 기울어진 단계** 섹션에 표시됩니다. **기울이기 스테이지** 섹션에서 한 항목을 선택하면 해당 차트가 세 번째 섹션에 구도되고 작업 세부 정보가 오른쪽 아래 패널에 표시됩니다.

![진단 탭 내의 시간 왜곡 탭입니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>실행기 사용 분석 그래프

**실행기 사용 그래프에는** 작업의 실제 실행기 할당 및 실행 상태가 표시됩니다.  

**실행기 사용 분석을**선택하면 실행기 사용에 대한 네 가지 곡선이 구배됩니다: **할당된 실행기,** **실행 실행기 실행기,** **유휴 실행기**및 **최대 실행기 인스턴스.** **추가된** 각 실행기 또는 **실행기 제거** 이벤트는 할당된 실행기를 늘리거나 줄입니다. 더 많은 비교를 위해 **작업** 탭에서 **이벤트 타임라인을** 확인할 수 있습니다.

![진단 탭 내의 실행기 사용 분석 탭입니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

모든 구문에서 해당 콘텐츠를 선택하거나 선택 취소하려면 색상 아이콘을 선택합니다.

 ![실행기 사용 분석 탭에서 차트를 선택합니다.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>FAQ

### <a name="how-do-i-revert-to-the-community-version"></a>커뮤니티 버전으로 되돌리려면 어떻게 해야 하나요?

커뮤니티 버전으로 되돌리려면 다음 단계를 수행합니다.

1. 암바리에서 클러스터를 엽니다.
1. **Spark2** > **구성으로 이동합니다.**
1. **사용자 지정 스파크2 기본값을 선택합니다.**
1. **속성 추가를 선택합니다.**. .
1. **spark.ui.enhancement.enabled.enabled=false를**추가한 다음 저장합니다.
1. 이제 속성을 **false**로 설정합니다.
1. **저장** 을 선택하여 구성을 저장합니다.

    ![아파치 암바리에서 기능을 끕니다.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 왼쪽 패널에서 **Spark2를** 선택합니다. 그런 다음 **요약** 탭에서 **Spark2 기록 서버를**선택합니다.

    ![아파치 암바리의 요약 보기.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 스파크 기록 서버를 다시 시작하려면 **Spark2 기록 서버**의 오른쪽에 있는 **시작** 단추를 선택한 다음 드롭다운 메뉴에서 **다시 시작을** 선택합니다.

    ![아파치 암바리에서 스파크 히스토리 서버를 다시 시작합니다.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. 스파크 히스토리 서버 웹 UI를 새로 고칩니다. 커뮤니티 버전으로 되돌아갑니다.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Spark 기록 서버 이벤트를 업로드하여 문제로 보고하려면 어떻게 해야 합니까?

Spark 기록 서버에서 오류가 발생하면 다음 단계를 수행하여 이벤트를 보고합니다.

1. 스파크 히스토리 서버 웹 UI에서 **다운로드를** 선택하여 이벤트를 다운로드합니다.

    ![스파크 히스토리 서버 UI에서 이벤트를 다운로드합니다.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. **스파크 애플리케이션 & 구인 그래프** 페이지에서 **피드백 제공을** 선택합니다.

    ![스파크 애플리케이션 & 구인 그래프 페이지에 대한 피드백 제공](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 오류에 대한 제목과 설명을 제공합니다. 그런 다음 .zip 파일을 편집 필드로 드래그하고 **새 문제 제출을**선택합니다.

    ![새 문제를 업로드하고 제출합니다.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>핫픽스 시나리오에서 .jar 파일을 업그레이드하려면 어떻게 해야 합니까?

핫픽스로 업그레이드하려면 다음 스크립트를 사용하여 업그레이드합니다. `spark-enhancement.jar*`

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

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Azure 포털에서 bash 파일 사용

1. Azure [포털을](https://ms.portal.azure.com)실행한 다음 클러스터를 선택합니다.
2. 다음 매개 변수를 사용 하 고 [스크립트 작업을](../hdinsight-hadoop-customize-cluster-linux.md) 완료 합니다.

    |속성 |값 |
    |---|---|
    |스크립트 유형|- 사용자 지정|
    |이름|업그레이드자르|
    |Bash 스크립트 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |노드 유형|머리, 노동자|
    |매개 변수|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure 포털 제출 스크립트 작업](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>알려진 문제

+ 현재 스파크 히스토리 서버는 스파크 2.3과 2.4에서만 작동합니다.

+ RDD를 사용하는 입력 및 출력 데이터는 **데이터** 탭에 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

+ [HDInsight에서 Apache Spark 클러스터용 리소스 관리](apache-spark-resource-manager.md)
+ [Apache Spark 설정 구성](apache-spark-settings.md)

## <a name="feedback"></a>사용자 의견

이 도구를 사용할 때 피드백이 있거나 문제가 발생하면 ()로[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)이메일을 보냅니다.
