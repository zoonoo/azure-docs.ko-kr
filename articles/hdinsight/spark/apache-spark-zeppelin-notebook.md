---
title: 아파치 스파크 클러스터에 & 제플린 노트북 - Azure HDInsight
description: Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook을 사용하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598275"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용

HDInsight Spark 클러스터에는 [Apache Spark](https://spark.apache.org/) 작업을 실행하는 데 사용할 수 있는 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook이 포함되어 있습니다. 이 문서에서는 HDInsight 클러스터에서 Zeppelin Notebook을 사용하는 방법에 대해 알아 봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* 클러스터 기본 스토리지에 대한 URI 체계입니다. `wasb://` Azure Blob 저장소, `abfs://` Azure 데이터 레이크 저장소 Gen2 또는 `adl://` Azure 데이터 레이크 저장소 Gen1용용입니다. Blob Storage에 대해 보안 전송을 사용하도록 `wasbs://`설정하면 URI가 됩니다.  자세한 내용은 [Azure 저장소에서 보안 전송 필요를](../../storage/common/storage-require-secure-transfer.md) 참조하십시오.

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin Notebook 시작

1. 스파크 클러스터 **개요에서**클러스터 대시보드에서 **Zeppelin 노트북을** **선택합니다.** 클러스터에 대한 관리자 자격 증명을 입력합니다.  

   > [!NOTE]  
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Zeppelin Notebook에 도달할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 새 Notebook을 만듭니다. 헤더 창에서 **전자 필기장으로 이동하여** > **새 메모 만들기.**

    ![새 Zeppelin 노트북 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "새 Zeppelin 노트북 만들기")

    전자 필기장에 대한 이름을 입력한 다음 **메모 만들기를**선택합니다.

3. 노트북 헤더에 연결된 상태가 표시되는지 확인합니다. 오른쪽 상단에 녹색 점이 표시됩니다.

    ![Zeppelin 노트북 상태](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 노트북 상태")

4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 스파크 클러스터를 만들면 샘플 데이터 `hvac.csv`파일이 에서 연결된 저장소 `\HdiSamples\SensorSampleData\hvac`계정에 복사됩니다.

    새 노트북에 기본적으로 만들어지는 빈 단락에 다음 코드 조각을 붙여넣습니다.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    **SHIFT + ENTER를** 누르거나 단락의 **재생** 버튼을 선택하여 스니펫을 실행합니다. 단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음과 같습니다.

    ![원시 데이터에서 임시 테이블 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "원시 데이터에서 임시 테이블 만들기")

    또한 각 단락에 제목을 제공할 수도 있습니다. 단락의 오른쪽 모서리에서 **설정** 아이콘(스프로킷)을 선택한 다음 **제목 표시를**선택합니다.  

    > [!NOTE]  
    > %spark2 인터프리터는 모든 HDInsight 버전의 Zeppelin 노트북에서 지원되지 않으며 %sh 인터프리터는 HDInsight 4.0 이상에서는 지원되지 않습니다.

5. 이제 `hvac` 테이블에서 Spark SQL 문을 실행할 수 있습니다. 새 단락에 다음 쿼리를 붙여넣습니다. 이 쿼리는 지정된 날짜에서 각 건물에 대한 건물 ID와 대상 및 실제 온도 간의 차이를 검색합니다. **Shift + Enter**를 누릅니다.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    **%sql** 문의 처음 부분은 Notebook에 Livy Scala 인터프리터를 사용하도록 지시합니다.

6. **표시를** 변경하려면 막대 차트 아이콘을 선택합니다.  **설정은** **막대 차트를**선택한 후에 나타나므로 **키**및 **값을**선택할 수 있습니다.  다음 스크린샷은 출력을 보여 줍니다.

    ![노트북을 사용하여 Spark SQL 문을 실행합니다1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "노트북을 사용하여 Spark SQL 문을 실행합니다1")

7. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 조각은 쿼리할 가능한 `Temp`값을 사용하여 쿼리에서 변수를 정의하는 방법을 보여 주십습니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    새 단락에 이 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 그런 다음 **온도** 드롭다운 목록에서 **65를** 선택합니다.

8. **표시를** 변경하려면 막대 차트 아이콘을 선택합니다.  그런 다음 **설정을** 선택하고 다음을 변경합니다.

   * **그룹:**  **대상 온도**추가 .  
   * **값:** 1. **날짜를**제거합니다.  2. **temp_diff**추가합니다.  3.  집계자를 **SUM에서** **AVG로**변경합니다.  

     다음 스크린샷은 출력을 보여 줍니다.

     ![노트북2를 사용하여 Spark SQL 문을 실행합니다.](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "노트북2를 사용하여 Spark SQL 문을 실행합니다.")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Notebook에서 외부 패키지 사용 방법

HDInsight의 아파치 스파크 클러스터에서 Zeppelin 노트북을 구성하여 클러스터에 즉시 포함되지 않은 외부 커뮤니티 기여 패키지를 사용할 수 있습니다. 사용할 수 있는 패키지의 전체 목록은 [Maven 리포지토리](https://search.maven.org/) 를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 커뮤니티 제공 패키지의 전체 목록은 [Spark 패키지](https://spark-packages.org/)에서 사용할 수 있습니다.

이 문서에서는 Jupyter 노트북과 함께 [스파크 csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하는 방법을 살펴보겠습니다.

1. 인터프리터 설정을 엽니다. 오른쪽 상단 모서리에서 로그인한 사용자 이름을 선택한 다음 **인터프리터를**선택합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. **livy2로**스크롤한 다음 **편집을**선택합니다.

    ![인터프리터 설정 변경1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "인터프리터 설정 변경1")

3. 키로 `livy.spark.jars.packages`이동하여 해당 값을 형식으로 `group:id:version`설정합니다. 따라서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하려면 키 값을 `com.databricks:spark-csv_2.10:1.4.0`으로 설정해야 합니다.

    ![인터프리터 설정 변경2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "인터프리터 설정 변경2")

    **저장을** 선택한 다음 **확인을** 선택하여 Livy 인터프리터를 다시 시작합니다.

4. 위에서 입력한 키 값에 도달하는 방법을 이해하려면 여기를 참조하세요.

    a. Maven Repository에서 패키지를 찾습니다. 이 문서에서는 [spark-csv를](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)사용했습니다.

    b. 해당 리포지토리에서 **GroupId**, **ArtifactId** 및 **Version** 값을 수집합니다.

    ![Jupyter 노트북에서 외부 패키지 사용](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter 노트북에서 외부 패키지 사용")

    다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 저장 위치

Zeppelin Notebook은 클러스터 헤드 노드에 저장됩니다. 따라서 클러스터를 삭제하면 Notebook도 삭제됩니다. 나중에 다른 클러스터에서 사용하기 위해 Notebook을 유지하려면 작업 실행을 완료 한 후 Notebook을 내보내야 합니다. 전자 필기장을 내보내려면 아래 이미지와 같이 **내보내기** 아이콘을 선택합니다.

![Notebook 다운로드](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "노트북 다운로드")

이렇게 하면 Notebook이 다운로드 위치에 JSON 파일로 저장됩니다.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Shiro를 사용하여 ESP(엔터프라이즈 보안 패키지) 클러스터에서 Zeppelin 인터프리터에 대한 액세스를 구성합니다.
위에서 `%sh` 언급했듯이, 인터프리터는 HDInsight 4.0 이후부터 지원되지 않습니다. 또한 인터프리터는 셸 명령을 사용하는 액세스 키탭과 같은 잠재적인 보안 문제를 소개하므로 `%sh` HDInsight 3.6 ESP 클러스터에서도 제거되었습니다. 기본적으로 `%sh` **새 메모 만들기** 또는 인터프리터 UI를 클릭할 때 인터프리터를 사용할 수 없습니다. 

권한이 있는 도메인 `Shiro.ini` 사용자는 파일을 사용하여 인터프리터 UI에 대한 액세스를 제어할 수 있습니다. 따라서 이러한 사용자만 새 `%sh` 인터프리터를 만들고 각 `%sh` 새 인터프리터에 대한 권한을 설정할 수 있습니다. `shiro.ini` 파일을 사용하여 액세스를 제어하려면 다음 단계를 사용합니다.

1. 기존 도메인 그룹 이름을 사용하여 새 역할을 정의합니다. 다음 `adminGroupName` 예제에서는 AAD의 권한 있는 사용자 그룹입니다. 그룹 이름에 특수 문자 나 공백을 사용하지 마십시오. 이 역할에 `=` 대한 권한을 부여한 후의 문자입니다. `*`즉, 그룹에 전체 권한이 있습니다.

    ```
    [roles]
    adminGroupName = *
    ```

2. Zeppelin 통역사에 액세스할 수 있는 새 역할을 추가합니다. 다음 예제에서는 모든 `adminGroupName` 사용자에게 Zeppelin 통역사에 대한 액세스 권한이 주어지며 새 통역사를 만들 수 있습니다. 쉼표로 구분된 대괄호 `roles[]`사이에 여러 역할을 넣을 수 있습니다. 그런 다음 필요한 권한이 있는 사용자는 Zeppelin 통역사에 액세스할 수 있습니다.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy 세션 관리

Zeppelin Notebook에서 첫 번째 코드 단락을 실행하면 HDInsight Spark 클러스터에 새로운 Livy 세션이 만들어집니다. 이 세션은 이후에 만드는 모든 Zeppelin Notebook에서 공유됩니다. 어떤 이유로 Livy 세션이 죽으면(클러스터 재부팅 등), Zeppelin 노트북에서 작업을 실행할 수 없습니다.

이 경우 Zeppelin Notebook에서 작업 실행을 시작하기 전에 다음 단계를 수행해야 합니다.  

1. Zeppelin Notebook에서 Livy 인터프리터를 다시 시작합니다. 이렇게 하려면 오른쪽 상단 모서리에서 로그인한 사용자 이름을 선택하여 인터프리터 설정을 연 다음 **인터프리터를**선택합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. **livy2로**스크롤한 다음 **다시 시작을**선택합니다.

    ![리비 통역사 다시 시작](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "제플린 통역사 다시 시작")

3. 기존 Zeppelin Notebook에서 코드 셀을 실행합니다. 이렇게 하면 HDInsight 클러스터에 새로운 Livy 세션이 만들어집니다.

## <a name="general-information"></a>일반 정보

### <a name="validate-service"></a>서비스 유효성 검사

Ambari에서 서비스의 유효성을 검사하려면 CLUSTERNAME이 클러스터의 이름인 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` 곳으로 이동합니다.

명령줄에서 헤드 노드로 SSH의 서비스를 확인합니다. 명령을 `sudo su zeppelin`사용하여 사용자를 제플린으로 전환합니다. 상태 명령:

|명령 |설명 |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|서비스 상태입니다.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|서비스 버전.|
|`ps -aux | grep zeppelin`|PID를 식별합니다.|

### <a name="log-locations"></a>로그 위치

|서비스 |경로 |
|---|---|
|제플린 서버|/usr/hdp/전류/제플린 서버/|
|서버 로그|/var/로그/제플린|
|구성 인터프리터, 시로, site.xml, log4j|/usr/HDP/전류/제플린 서버/conf//etc/제플린/conf|
|PID 디렉토리|/var/런/제플린|

### <a name="enable-debug-logging"></a>디버그 로깅 활성화

1. CLUSTERNAME이 클러스터의 이름인 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` 곳으로 이동합니다.

1. **CONFIGS** > **고급 제플린-log4j-속성** > **log4j_properties_content**로 이동합니다.

1. 로 `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`수정합니다.

1. `log4j.logger.org.apache.zeppelin.realm=DEBUG`를 추가합니다.

1. 변경 내용을 저장하고 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

[개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오

* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark Scala 애플리케이션 생성 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
