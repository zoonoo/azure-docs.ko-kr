---
title: "스크립트 작업을 사용하여 Azure HDInsight에서 Spark 클러스터용 Zeppelin Notebook 설치 | Microsoft Docs"
description: "HDInsight Linux에서 Spark 클러스터와 함께 Zeppelin Notebook을 설치 및 사용하는 방법에 대한 단계별 지침입니다."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9e6e0a696ba27c7758c21fa46754a8539ae2255b
ms.lasthandoff: 03/29/2017


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>HDInsight에서 Apache Spark 클러스터용 Zeppelin Notebook 설치

Apache Spark 클러스터에 Zeppelin Notebook을 설치하는 방법 및 Zeppelin Notebook을 사용하여 Spark 작업을 실행하는 방법을 알아봅니다.

> [!IMPORTANT]
> HDInsight 3.5에서 Spark 1.6 클러스터를 프로비전한 경우 [HDInsight Linux에서 Apache Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-zeppelin-notebook.md)의 지침에 따라 기본적으로 Zeppelin에 액세스할 수 있습니다. HDInsight 클러스터 버전 3.3 또는 3.4에서 Zeppelin을 사용하려는 경우 이 문서의 지침에 따라 Zeppelin을 설치해야 합니다.
>
> 이 문서에서는 스크립트를 사용하여 Spark 2.0 클러스터에서 Zeppelin을 설치하는 것은 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 있어야 합니다. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.


## <a name="install-zeppelin-on-a-spark-cluster"></a>Spark 클러스터에 Zeppelin 설치
스크립트 작업을 사용하여 Spark 클러스터에서 Zeppelin을 설치할 수 있습니다. 스크립트 작업은 사용자 지정 스크립트를 사용하여 기본적으로 사용할 수 없는 클러스터에 구성 요소를 설치합니다. 사용자 지정 스크립트를 사용하여 Azure 포털에서 HDInsight .NET SDK를 사용하거나 Azure PowerShell을 사용하여 Zeppelin을 설치할 수 있습니다. 클러스터 생성 과정의 일부로 또는 클러스터가 작동 및 실행된 이후에 이 스크립트를 사용하여 Zeppelin을 설치할 수 있습니다. 아래 섹션의 링크는 이러한 작업을 수행하는 방법에 지침을 제공합니다.

### <a name="using-the-azure-portal"></a>Azure 포털 사용
Azure 포털을 사용하여 Zeppelin을 설치하는 스크립트 동작을 실행하는 방법은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)을 참조하세요. 해당 문서의 지침을 약간 변경해야 합니다.

* Zeppelin을 설치하기 위해 스크립트를 사용해야 합니다. HDInsight의 Spark 클러스터에서 Zeppelin을 설치하는 사용자 지정 스크립트는 다음 링크에서 사용 가능합니다.

  * Spark 1.6.0 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* 헤드 노드에서만 스크립트 작업을 실행해야 합니다.
* 이 스크립트는 매개 변수가 필요 없습니다.

### <a name="using-hdinsight-net-sdk"></a>HDInsight .NET SDK 사용
HDInsight .NET SDK를 사용하여 Zeppelin을 설치하는 스크립트 작업을 실행하는 방법은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)을 참조하세요. 해당 문서의 지침을 약간 변경해야 합니다.

* Zeppelin을 설치하기 위해 스크립트를 사용해야 합니다. HDInsight의 Spark 클러스터에서 Zeppelin을 설치하는 사용자 지정 스크립트는 다음 링크에서 사용 가능합니다.

  * Spark 1.6.0 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Spark 1.5.2 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* 이 스크립트는 매개 변수가 필요 없습니다.
* Spark에 만들려는 클러스터 유형을 설정합니다.

### <a name="using-azure-powershell"></a>Azure PowerShell 사용
다음 PowerShell 조각을 사용하여 Zeppelin이 설치된 HDInsight Linux에 Spark 클러스터를 만듭니다. Spark 클러스터의 버전에 따라 해당 사용자 지정 스크립트에 대한 링크를 포함하도록 다음 PowerShell 조각을 업데이트해야 합니다.

* Spark 1.6.0 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Spark 1.5.2 클러스터의 경우 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>Zeppelin Notebook에 액세스

스크립트 작업을 사용하여 Zeppelin을 설치했으면 아래 단계에 따라 Spark 클러스터의 Zeppelin Notebook에 액세스할 수 있습니다. 이 섹션에서는 %sql 및 %hive 문을 실행하는 방법을 알아봅니다.

1. 웹 브라우저에서 다음 끝점을 엽니다.

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. 새 Notebook을 만듭니다. 헤더 창에서 **노트북**을 클릭하고 **새 메모 만들기**를 클릭합니다.

    ![새 Zeppelin Notebook 만들기](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "새 Zeppelin Notebook 만들기")

    같은 페이지의 **노트북** 제목 아래에 **Note XXXXXXXXX**로 시작하는 이름의 새 노트북이 표시됩니다. 새 노트북을 클릭합니다.
3. 새 노트북에 대한 웹 페이지에서 제목을 클릭하고 원하는 경우 노트북의 이름을 변경합니다. Enter 키를 눌러 변경된 이름을 저장합니다. 또한 노트북 헤더의 오른쪽 위 모서리에 **연결됨** 상태가 표시되는지 확인합니다.

    ![Zeppelin Notebook 상태](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin Notebook 상태")

### <a name="run-sql-statements"></a>SQL 문 실행
1. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 만들면 샘플 데이터 파일인 **hvac.csv**가 **\HdiSamples\SensorSampleData\hvac** 아래 연결된 저장소 계정에 복사됩니다.

    새 노트북에 기본적으로 만들어지는 빈 단락에 다음 코드 조각을 붙여넣습니다.

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

    **Shift + Enter**를 누르거나 단락에 대한 **재생** 단추를 클릭하여 코드 조각을 실행합니다. 단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음과 같습니다.

    ![원시 데이터에서 임시 테이블 만들기](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "원시 데이터에서 임시 테이블 만들기")

    또한 각 단락에 제목을 제공할 수도 있습니다. 오른쪽 모서리에서 **설정** 아이콘을 클릭하고 **제목 표시**를 클릭합니다.
2. 이제 **hvac** 테이블에서 Spark SQL 문을 실행할 수 있습니다. 새 단락에 다음 쿼리를 붙여넣습니다. 이 쿼리는 지정된 날짜에서 각 건물에 대한 건물 ID와 대상 및 실제 온도 간의 차이를 검색합니다. **Shift + Enter**를 누릅니다.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    시작 부분의 **%sql** 문은 Notebook에서 Spark SQL 인터프리터를 사용함을 알려줍니다. Notebook 헤더의 **인터프리터** 탭에서 정의된 인터프리터를 확인할 수 있습니다.

    다음 스크린샷은 출력을 보여 줍니다.

    ![Notebook을 사용하여 Spark SQL 문 실행](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Notebook을 사용하여 Spark SQL 문 실행")

     동일한 출력에 대해 서로 다른 표현 간을 전환하려면 표시 옵션(사각형으로 강조 표시됨)을 클릭합니다. **설정** 을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp_diff**의 평균을 값으로 사용합니다.
3. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 조각에서는 쿼리할 수 있는 값이 포함된 쿼리에 변수 **Temp**를 정의하는 방법을 보여 줍니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    새 단락에 이 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 다음 스크린샷은 출력을 보여 줍니다.

    ![Notebook을 사용하여 Spark SQL 문 실행](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Notebook을 사용하여 Spark SQL 문 실행")

    후속 쿼리에 대해서는 드롭다운에서 새 값을 선택하고 쿼리를 다시 실행할 수 있습니다. **설정** 을 클릭하여 출력에서 키 및 값을 구성하는 항목을 선택합니다. 위 화면 캡처에서는 **buildingID**를 키로, **temp_diff**의 평균을 값으로, **targettemp**를 그룹으로 사용합니다.
4. Spark SQL 인터프리터를 다시 시작하여 응용 프로그램을 종료합니다. 맨 위쪽에서 **인터프리터** 탭을 클릭하고 Spark 인터프리터에 대해 **다시 시작**을 클릭합니다.

    ![Zeppelin 인터프리터 다시 시작](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Zeppelin 인터프리터 다시 시작")

### <a name="run-hive-statements"></a>Hive 문 실행
1. Zeppelin Notebook에서 **Interpreter** 단추를 클릭합니다.

    ![Hive 인터프리터 업데이트](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Hive 인터프리터 업데이트")
2. **hive** 인터프리터에 대해 **edit**을 클릭합니다.

    ![Hive 인터프리터 업데이트](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Hive 인터프리터 업데이트")

    다음 속성을 업데이트합니다.

   * **default.password** 를 HDInsight Spark 클러스터를 만드는 동안 관리자에 대해 지정한 암호로 설정합니다.
   * **default.url**을 `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`로 설정합니다. **\<<spark_cluster_name>**을 Spark 클러스터의 이름으로 바꿉니다.
   * **default.user**를 클러스터를 만들 때 지정한 관리자의 이름으로 설정합니다. 예: *admin*.
3. **Save**를 클릭하고 Hive 인터프리터를 다시 시작하라는 메시지가 표시되면 **OK**를 클릭합니다.
4. 새 Notebook을 만들고 다음 문을 실행하여 클러스터의 모든 hive 테이블을 나열합니다.

        %hive
        SHOW TABLES

    기본적으로 HDInsight 클러스터에는 **hivesampletable**이라는 샘플 테이블이 있으므로 다음과 같은 출력이 표시됩니다.

    ![Hive 출력](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "출력 Hive")
5. 다음 문을 실행하여 테이블의 레코드를 나열합니다.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    다음과 유사한 출력이 표시됩니다.

    ![Hive 출력](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "출력 Hive")

## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>응용 프로그램 만들기 및 실행
* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark 응용 프로그램을 원격으로 디버그](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight의 Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

