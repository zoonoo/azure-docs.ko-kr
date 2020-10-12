---
title: Zeppelin 노트북 & Apache Spark 클러스터-Azure HDInsight
description: Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook을 사용하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: a692f4dd86d110f7f0a91a862a7b16ac28345de5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084531"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용

HDInsight Spark 클러스터는 [Apache Zeppelin](https://zeppelin.apache.org/) 노트북을 포함 합니다. 노트북을 사용 하 여 Apache Spark 작업을 실행 합니다. 이 문서에서는 HDInsight 클러스터에서 Zeppelin Notebook을 사용하는 방법에 대해 알아 봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* 클러스터 기본 스토리지에 대한 URI 체계입니다. 이 체계는 `wasb://` Azure Blob Storage `abfs://` Azure Data Lake Storage Gen2 또는 Azure Data Lake Storage Gen1에 대 한 것입니다 `adl://` . Blob Storage에 대해 보안 전송이 사용 되는 경우 URI는 `wasbs://` 입니다.  자세한 내용은 [Azure Storage에서 보안 전송 필요](../../storage/common/storage-require-secure-transfer.md) 를 참조 하세요.

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin Notebook 시작

1. Spark 클러스터 **개요**에서 **클러스터 대시보드의** **Zeppelin 노트북** 을 선택 합니다. 클러스터에 대 한 관리자 자격 증명을 입력 합니다.  

   > [!NOTE]  
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Zeppelin Notebook에 도달할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 새 Notebook을 만듭니다. 머리글 창에서 **노트북**  >  **새 노트 만들기**로 이동 합니다.

    ![새 Zeppelin 노트북 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "새 Zeppelin 노트북 만들기")

    노트북의 이름을 입력 한 다음 **메모 만들기**를 선택 합니다.

3. 노트북 머리글이 연결 된 상태를 표시 하는지 확인 합니다. 오른쪽 위 모서리에 녹색 점으로 표시 됩니다.

    ![Zeppelin 노트북 상태](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 노트북 상태")

4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 만들 때 샘플 데이터 파일는의 `hvac.csv` 연결 된 저장소 계정에 복사 됩니다 `\HdiSamples\SensorSampleData\hvac` .

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

    **SHIFT + enter** 를 누르거나 단락에 대 한 **재생** 단추를 선택 하 여 코드 조각을 실행 합니다. 단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음 이미지와 같습니다.

    ![원시 데이터에서 임시 테이블 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "원시 데이터에서 임시 테이블 만들기")

    또한 각 단락에 제목을 제공할 수도 있습니다. 단락의 오른쪽 모서리에서 **설정** 아이콘 (sprocket)을 선택한 다음 **제목 표시**를 선택 합니다.  

    > [!NOTE]  
    > %spark2 인터프리터는 모든 HDInsight 버전의 Zeppelin 노트북에서 지원되지 않으며 %sh 인터프리터는 HDInsight 4.0 이상에서는 지원되지 않습니다.

5. 이제 테이블에서 Spark SQL 문을 실행할 수 있습니다 `hvac` . 새 단락에 다음 쿼리를 붙여넣습니다. 쿼리가 건물 ID를 검색 합니다. 또한 지정 된 날짜에 각 건물에 대 한 대상 온도와 실제 온도 간의 차이입니다. **Shift + Enter**를 누릅니다.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    **%sql** 문의 처음 부분은 Notebook에 Livy Scala 인터프리터를 사용하도록 지시합니다.

6. **가로 막대형 차트** 아이콘을 선택 하 여 표시를 변경 합니다.  **가로 막대형 차트**를 선택한 후에 표시 되는 **설정**에서 **키**및 **값**을 선택할 수 있습니다.  다음 스크린샷은 출력을 보여 줍니다.

    ![Notebook1를 사용 하 여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Notebook1를 사용 하 여 Spark SQL 문 실행")

7. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 조각에서는 쿼리할 `Temp` 수 있는 값을 사용 하 여 쿼리에서 변수를 정의 하는 방법을 보여 줍니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    새 단락에 이 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 그런 다음, **임시** 드롭다운 목록에서 **65** 를 선택 합니다.

8. **가로 막대형 차트** 아이콘을 선택 하 여 표시를 변경 합니다.  그런 다음 **설정** 을 선택 하 고 다음과 같이 변경 합니다.

   * **그룹:**  **Targettemp**를 추가 합니다.  
   * **값:** 1(sp1). **날짜**를 제거 합니다.  2. **Temp_diff**를 추가 합니다.  3.  집계를 **SUM** 에서 **AVG**로 변경 합니다.  

     다음 스크린샷은 출력을 보여 줍니다.

     ![Notebook2를 사용 하 여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Notebook2를 사용 하 여 Spark SQL 문 실행")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Notebook에서 외부 패키지 사용 방법

HDInsight의 Apache Spark 클러스터에 있는 Zeppelin 노트북은 클러스터에 포함 되지 않은 외부의 커뮤니티 제공 패키지를 사용할 수 있습니다. [Maven 리포지토리에서](https://search.maven.org/) 사용할 수 있는 패키지의 전체 목록을 검색 합니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 커뮤니티 제공 패키지의 전체 목록은 [Spark 패키지](https://spark-packages.org/)에서 사용할 수 있습니다.

이 문서에서는 Jupyter 노트북에서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용 하는 방법을 알아봅니다.

1. 인터프리터 설정을 엽니다. 오른쪽 위 모서리에서 로그인 한 사용자 이름을 선택 하 고 **인터프리터**를 선택 합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. **Livy2**으로 스크롤한 다음 **편집**을 선택 합니다.

    ![변경 인터프리터 run settings1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "변경 인터프리터 run settings1")

3. 키로 이동 하 여 `livy.spark.jars.packages` 해당 값을 형식으로 설정 `group:id:version` 합니다. 따라서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하려면 키 값을 `com.databricks:spark-csv_2.10:1.4.0`으로 설정해야 합니다.

    ![변경 인터프리터 settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "변경 인터프리터 settings2")

    **저장** 을 선택 하 고 **확인** 을 선택 하 여 Livy 인터프리터를 다시 시작 합니다.

4. 위에서 입력한 키 값에 도달하는 방법을 이해하려면 여기를 참조하세요.

    a. Maven Repository에서 패키지를 찾습니다. 이 문서에서는 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)를 사용 했습니다.

    b. 해당 리포지토리에서 **GroupId**, **ArtifactId** 및 **Version** 값을 수집합니다.

    ![Jupyter 노트북에서 외부 패키지 사용](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter 노트북에서 외부 패키지 사용")

    다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 저장 위치

Zeppelin Notebook은 클러스터 헤드 노드에 저장됩니다. 따라서 클러스터를 삭제하면 Notebook도 삭제됩니다. 나중에 다른 클러스터에서 사용하기 위해 Notebook을 유지하려면 작업 실행을 완료 한 후 Notebook을 내보내야 합니다. 노트북을 내보내려면 아래 이미지에 표시 된 것 처럼 **내보내기** 아이콘을 선택 합니다.

![Notebook 다운로드](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "노트북 다운로드")

이 작업을 수행 하면 전자 필기장이 다운로드 위치에 JSON 파일로 저장 됩니다.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>`Shiro`Enterprise Security Package (ESP) 클러스터에서 Zeppelin 인터프리터에 대 한 액세스를 구성 하는 데 사용 합니다.

위에서 설명한 것 처럼 `%sh` 인터프리터는 HDInsight 4.0 이상에서 지원 되지 않습니다. 또한 인터프리터는 `%sh` 셸 명령을 사용 하는 액세스 keytabs 같은 잠재적인 보안 문제를 도입 하기 때문에 HDInsight 3.6 ESP 클러스터 에서도 제거 되었습니다. 이 `%sh` 는 기본적으로 **새 메모 만들기** 또는 인터프리터 UI에서 인터프리터를 클릭할 때 인터프리터를 사용할 수 없음을 의미 합니다.

권한 있는 도메인 사용자는 파일을 사용 `Shiro.ini` 하 여 인터프리터 UI에 대 한 액세스를 제어할 수 있습니다. 이러한 사용자만 새 인터프리터 `%sh` 를 만들고 각 새 인터프리터에 대 한 사용 권한을 설정할 수 있습니다 `%sh` . 파일을 사용 하 여 액세스를 제어 하려면 `shiro.ini` 다음 단계를 사용 합니다.

1. 기존 도메인 그룹 이름을 사용 하 여 새 역할을 정의 합니다. 다음 예제에서 `adminGroupName` 는 AAD의 권한 있는 사용자 그룹입니다. 그룹 이름에 특수 문자나 공백을 사용 하지 마십시오. 뒤의 문자는 `=` 이 역할에 대 한 사용 권한을 부여 합니다. `*` 그룹에 모든 권한이 있음을 의미 합니다.

    ```
    [roles]
    adminGroupName = *
    ```

2. Zeppelin 인터프리터에 대 한 액세스에 대 한 새 역할을 추가 합니다. 다음 예에서는의 모든 사용자에 `adminGroupName` 게 Zeppelin 인터프리터에 대 한 액세스 권한이 부여 되 고 새 인터프리터를 만들 수 있습니다. 쉼표로 구분 된의 대괄호 사이에 여러 역할을 넣을 수 있습니다 `roles[]` . 그런 다음 필요한 권한이 있는 사용자는 Zeppelin 인터프리터에 액세스할 수 있습니다.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy 세션 관리

Zeppelin 노트북의 첫 번째 코드 단락은 클러스터에 새 Livy 세션을 만듭니다. 이 세션은 나중에 만드는 모든 Zeppelin 노트북에서 공유 됩니다. 어떤 이유로 든 Livy 세션이 중단 되 면 Zeppelin 노트북에서 작업이 실행 되지 않습니다.

이 경우 Zeppelin 노트북에서 작업 실행을 시작 하기 전에 다음 단계를 수행 해야 합니다.  

1. Zeppelin Notebook에서 Livy 인터프리터를 다시 시작합니다. 이렇게 하려면 오른쪽 위 모서리에서 로그인 한 사용자 이름을 선택 하 여 인터프리터 설정을 열고 **인터프리터**를 선택 합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. **Livy2**으로 스크롤한 다음 **다시 시작**을 선택 합니다.

    ![Livy 인터프리터를 다시 시작 합니다.](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin 인터프리터를 다시 시작 합니다.")

3. 기존 Zeppelin Notebook에서 코드 셀을 실행합니다. 이 코드는 HDInsight 클러스터에 새 Livy 세션을 만듭니다.

## <a name="general-information"></a>일반 정보

### <a name="validate-service"></a>서비스 유효성 검사

Ambari에서 서비스의 유효성을 검사 하려면로 이동 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` 합니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.

명령줄에서 서비스의 유효성을 검사 하려면 헤드 노드로 SSH를 수행 합니다. Command를 사용 하 여 사용자를 zeppelin로 전환 `sudo su zeppelin` 합니다. 상태 명령:

|명령 |설명 |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|서비스 상태.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|서비스 버전입니다.|
|`ps -aux | grep zeppelin`|PID를 식별 합니다.|

### <a name="log-locations"></a>로그 위치

|서비스 |경로 |
|---|---|
|zeppelin-서버|/usr/hdp/current/zeppelin-server/|
|서버 로그|/var/log/zeppelin|
|구성 인터프리터, `Shiro` , site.xml, log4j|/usr/hdp/current/zeppelin-server/conf 또는/etc/zeppelin/conf|
|PID 디렉터리|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>디버그 로깅 활성화

1. 로 이동 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` 합니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.

1. **CONFIGS**  >  **Advanced zeppelin-log4j**  >  **log4j_properties_content**로 이동 합니다.

1. `log4j.appender.dailyfile.Threshold = INFO`을로 수정 `log4j.appender.dailyfile.Threshold = DEBUG` 합니다.

1. `log4j.logger.org.apache.zeppelin.realm=DEBUG`를 추가합니다.

1. 변경 내용을 저장 하 고 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)
