---
title: 확장된 Spark 기록 서버를 사용하여 Spark 응용 프로그램 디버그 및 진단 - Azure HDInsight | Microsoft Docs
description: 확장된 Spark 기록 서버를 사용하여 Spark 응용 프로그램 디버그 및 진단 - Azure HDInsight.
services: hdinsight
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: ''
ms.topic: article
ms.date: 07/12/2018
ms.author: jejiang
ms.openlocfilehash: 61e857b01f75cb30e3d213703bd8ee8014d902d6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309628"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>확장된 Spark 기록 서버를 사용하여 Spark 응용 프로그램 디버그 및 진단

이 문서에서는 확장된 Spark 기록 서버를 사용하여 완료되고 실행되는 Spark 응용 프로그램을 디버그 및 진단하는 방법에 대한 지침을 제공합니다. 현재 확장은 데이터 탭 및 그래프 탭을 포함합니다. 데이터 탭에서 사용자는 Spark 작업의 입출력 데이터를 확인할 수 있습니다. 그래프 탭에서 사용자는 데이터 흐름을 확인하고 작업 그래프를 재생할 수 있습니다.

## <a name="open-the-spark-history-server"></a>Spark 기록 서버 열기

Spark 기록 서버는 완료되고 실행되는 Spark 응용 프로그램의 웹 UI입니다. 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>Azure Portal에서 Spark 기록 서버 웹 UI를 열려면

1. [Azure Portal](https://portal.azure.com/)에서 Spark 클러스터를 엽니다. 자세한 내용은 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)를 참조하세요.
2. **빠른 링크**에서 **클러스터 대시보드**를 클릭한 다음, **Spark 기록 서버**를 클릭합니다. 메시지가 표시되면 Spark 클러스터에 대한 관리자 자격 증명을 입력합니다. 

    ![Spark 기록 서버](./media/apache-azure-spark-history-server/launch-history-server.png "Spark 기록 서버")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>URL을 통해 Spark 기록 서버 웹 UI를 열려면
다음 URL로 이동하여 Spark 기록 서버를 열고 <ClusterName>을 고객의 Spark 클러스터 이름으로 바꿉니다.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Spark 기록 서버 웹 UI는 다음과 같습니다.

![HDInsight Spark 기록 서버](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Spark 기록 서버에서 데이터 탭 열기
데이터 보기를 가져오려면 작업 ID를 선택한 다음, 도구 메뉴에서 **데이터**를 클릭합니다.

+ 별도로 탭을 선택하여 **입력**, **출력** 및 **테이블 작업**을 확인합니다.

    ![데이터 탭](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ **복사** 단추를 클릭하여 모든 행을 복사합니다.

    ![데이터 복사](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ **csv** 단추를 클릭하여 모든 데이터를 CSV 파일로 저장합니다.

    ![데이터 저장](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ **검색** 필드에서 키워드를 입력하여 검색하면 검색 결과가 즉시 표시됩니다.

    ![데이터 검색](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ 열 헤더를 클릭하여 테이블을 정렬하고, 자세한 세부 정보를 표시하려면 더하기 기호를 클릭하여 행을 확장하거나, 빼기 기호를 클릭하여 행을 축소합니다.

    ![데이터 테이블](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ 오른쪽에 있는 **부분 다운로드** 단추를 클릭하여 단일 파일을 다운로드한 다음, 선택한 파일을 로컬에 다운로드합니다. 파일이 더 이상 존재하지 않는 경우 새 탭이 열리고 오류 메시지가 표시됩니다.

    ![데이터 다운로드 행](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 다운로드 메뉴에서 확장되는 **전체 경로 복사** 또는 **상대 경로 복사**를 선택하여 전체 경로 또는 상대 경로를 복사합니다. Azure 데이터 레이크 저장소 파일의 경우 **Azure Storage 탐색기에서 열기**는 Azure Storage 탐색기를 시작하고 로그인 시 해당 폴더를 찾습니다.

    ![데이터 복사 경로](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 한 페이지에 너무 많은 행이 표시될 경우 테이블 아래의 숫자를 클릭하여 페이지를 탐색합니다. 

    ![데이터 페이지](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ 데이터 옆의 물음표를 마우스로 가리켜 도구 설명을 표시하거나 물음표를 클릭하여 자세한 정보를 가져옵니다.

    ![자세한 데이터 정보](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ **피드백 제공**을 클릭하여 문제와 함께 피드백을 보냅니다.

    ![그래프 피드백](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Spark 기록 서버에서 Graph 탭 열기
작업 그래프 보기를 가져오려면 작업 ID를 선택한 다음, 도구 메뉴에서 **Graph**를 클릭합니다.

+ 생성된 작업 그래프를 통해 작업의 개요를 확인합니다. 

+ 기본적으로 모든 작업이 표시되며, **작업 ID**에서 필터링할 수 있습니다.

    ![그래프 작업 ID](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ 기본적으로 **진행률**을 선택하면 사용자는 **표시**의 드롭다운 목록에서 **읽기/쓰기**를 선택하여 데이터 흐름을 확인할 수 있습니다.

    ![그래프 표시](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    열 지도를 보여주는 컬러판 그래프 노드 표시입니다.

    ![그래프 열 지도](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ **재생** 단추를 클릭하여 작업을 재생하고 중지 단추를 클릭하여 언제든 중지할 수 있습니다. 재생 시 다른 상태를 표시하기 위한 컬러판 작업 표시입니다.

    + 성공의 경우 녹색: 작업이 완료되었습니다.
    + 다시 시도의 경우 주황색: 실패했지만 작업의 최종 결과에는 영향을 미치지 않는 작업의 인스턴스입니다. 이러한 작업은 나중에 성공할 수 있는 인스턴스를 복제하거나 다시 시도합니다.
    + 실패의 경우 빨간색: 작업이 실패했습니다.
    + 실행의 경우 파란색: 작업이 실행 중입니다.
    + 건너뜀 또는 대기 중인 경우 흰색: 작업이 실행을 위해 대기 중이거나 단계를 건너뛰었습니다.

    ![실행 중인 그래프 색 샘플](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![실패한 그래프 색 샘플](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > 각 작업에 대한 재생이 허용됩니다. 작업에 단계가 없거나 완료되지 않은 경우 재생은 지원되지 않습니다.


+ 마우스를 스크롤하여 작업 그래프를 확대/축소하거나 **크기에 맞게**를 클릭하여 화면에 맞춥니다.
 
    ![그래프 크기에 맞게](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 실패한 작업이 있는 경우 도구 설명을 보려면 그래프 노드를 마우스로 가리키고 단계를 클릭하여 단계 페이지를 엽니다.

    ![그래프 도구 설명](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

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

+ **피드백 제공**을 클릭하여 문제와 함께 피드백을 보냅니다.

    ![그래프 피드백](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>FAQ

### <a name="1-revert-to-community-version"></a>1. 커뮤니티 버전으로 되돌리기

커뮤니티 버전으로 되돌리려면 다음 단계를 수행합니다.

1. Ambari에서 클러스터를 엽니다. 왼쪽 패널에서 **Spark2**를 클릭합니다.
2. **Configs** 탭을 클릭합니다.
3. **사용자 지정 spark2-defaults** 그룹을 확장합니다.
4. **속성 추가**를 클릭하고 **spark.ui.enhancement.enabled=false**를 추가하고 저장합니다.
5. 이제 속성을 **false**로 설정합니다.
6. 구성을 저장하려면 **Save**를 클릭합니다.

    ![기능 끄기](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. **요약** 탭 아래의 왼쪽 패널에서 **Spark2**를 클릭하고 **Spark2 기록 서버**를 클릭합니다.

    ![서버1 다시 시작](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. **Spark2 기록 서버**의 **다시 시작**을 클릭하여 기록 서버를 다시 시작합니다.

    ![서버2 다시 시작](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Spark 기록 서버 웹 UI를 새로 고침하면 커뮤니티 버전으로 되돌려집니다.

### <a name="2-upload-history-server-event"></a>2. 기록 서버 이벤트 업로드

기록 서버 오류가 발생한 경우 이벤트를 제공하기 위한 단계를 수행합니다.
1. 기록 서버 웹 UI에서 **다운로드**를 클릭하여 이벤트를 다운로드합니다.

    ![다운로드 이벤트](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 데이터/그래프 탭에서 **피드백 제공**을 클릭합니다.

    ![그래프 피드백](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 오류의 제목 및 설명을 제공하고, zip 파일을 편집 필드를 끌어온 다음, **새 문제 제출**을 클릭합니다.

    ![파일 문제](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. 핫픽스 시나리오에 대한 jar 파일 업그레이드

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

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**Azure Portal에서 Bash 파일을 사용하려면**

1. [Azure Portal](https://ms.portal.azure.com)을 시작하고 클러스터를 선택합니다.
2. **스크립트 동작** 및 **새로운 항목 제출**을 차례로 클릭합니다. **스크립트 동작 제출** 양식을 완료한 다음, **만들기** 단추를 클릭합니다.
    
    + **스크립트 유형**: **사용자 지정**을 선택합니다.
    + **이름**: 스크립트 이름을 지정합니다.
    + **Bash 스크립트 URI**: 개인 클러스터에 Bash 파일을 업로드한 다음, 여기에 URL을 복사합니다. 또는 제공된 URI를 사용합니다.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + **헤드** 및 **작업자**를 확인합니다.
    + **매개 변수**: Bash 사용량에 따라 매개 변수를 설정합니다.

    ![로그 업로드 또는 핫픽스 업그레이드](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>알려진 문제

1.  현재 Spark 2.3 클러스터에 대해서만 작동합니다.

2.  RDD를 사용한 데이터 입/출력은 데이터 탭에 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [Spark 설정 구성](apache-spark-settings.md)


## <a name="contact-us"></a>문의처

의견이 있거나 이 도구를 사용할 때 다른 문제가 발생하는 경우 ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com))으로 메일을 보내주시기 바랍니다.