---
title: Azure HDInsight에서 Livy Spark를 사용하여 Spark 클러스터에 작업 제출
description: Apache Spark REST API를 사용하여 Azure HDInsight 클러스터에 원격으로 Spark 작업을 제출하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: f5b3500e1e700abf894fc4e21fb540eb258d5e35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066068"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Apache Spark REST API를 사용하여 HDInsight Spark 클러스터에 원격 작업 제출

Azure HDInsight Spark 클러스터에 원격 작업을 제출하는 데 사용되는 [Apache Livy](https://livy.incubator.apache.org/), [Apache Spark](https://spark.apache.org/) REST API를 사용하는 방법을 알아봅니다. 자세한 설명서는 [https://livy.incubator.apache.org/](https://livy.incubator.apache.org/)를 참조하세요.

Livy를 사용하여 대화형 Spark 셸을 실행하거나 Spark에서 실행되도록 배치 작업을 제출할 수 있습니다. 이 문서는 Livy를 사용하여 배치 작업을 제출하는 방법에 대해 설명합니다. 이 문서의 코드 조각은 cURL을 사용하여 Livy Spark 엔드포인트에 대한 REST API를 호출합니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

* [cURL](https://curl.haxx.se/). 이 문서에서는 cURL을 사용하여 HDInsight Spark 클러스터에 대해 REST API 호출을 수행하는 방법을 보여 줍니다.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Apache Livy Spark 일괄 작업 제출

배치 작업을 제출하기 전에 클러스터와 연결된 클러스터 스토리지에 애플리케이션 jar을 업로드해야 합니다. 사용할 수 있습니다 [AzCopy](../../storage/common/storage-use-azcopy.md), 이렇게 하려면 명령줄 유틸리티를 합니다. 데이터를 업로드하는 데 사용할 수 있는 다른 클라이언트도 많이 있습니다. [HDInsight에서 Apache Hadoop 작업용 데이터 업로드](../hdinsight-upload-data.md)에서 자세한 정보를 찾을 수 있습니다.

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>예

* jar 파일이 클러스터 저장소(WASB)에 있는 경우

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* 입력 파일의 일부분으로 jar 파일 이름 및 클래스 이름을 전달하려는 경우(이 예제에서는 input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>클러스터에서 실행 중인 Livy Spark 배치에 대한 정보 가져오기

구문

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>예

* 클러스터에서 실행 중인 모든 Livy Spark 배치를 검색하려는 경우:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
    ```

* 지정 된 일괄 처리 ID로 특정 배치를 검색 하려는 경우

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Livy Spark 배치 작업 삭제

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>예

일괄 처리 ID 사용 하 여 일괄 처리 작업을 삭제 `5`합니다.

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark 및 고가용성

Livy는 클러스터에서 실행 중인 Spark 작업에 대해 고가용성을 제공합니다. 다음은 몇 가지 예입니다.

* Spark 클러스터에 원격으로 작업을 제출한 후에 Livy 서비스가 다운되면 작업은 백그라운드에서 계속 실행됩니다. Livy는 백업된 후 작업의 상태를 복원하고 다시 보고합니다.
* HDInsight용 Jupyter 노트북은 백 엔드에서 Livy를 통해 구동됩니다. 노트북에서 Spark 작업이 실행되고 있으며 Livy 서비스가 다시 시작되면 해당 노트북은 코드 셀을 계속 실행합니다. 

## <a name="show-me-an-example"></a>예제 보기

이 섹션에서는 Livy Spark를 사용하여 배치 작업을 제출하고 작업의 진행 상황을 모니터링한 다음 작업을 삭제하기 위한 예제를 살펴봅니다. 이 예제에서 사용하는 애플리케이션은 문서 [독립 실행형 Scala 애플리케이션을 만들고 HDInsight Spark 클러스터에서 실행하기](apache-spark-create-standalone-application.md)에서 개발된 것입니다. 여기에 나오는 단계는 다음 상태로 간주합니다.

* 애플리케이션 jar를 클러스터와 연결된 저장소 계정에 이미 복사했습니다.
* 이 단계를 시도하려는 컴퓨터에 CuRL을 설치했습니다.

다음 단계를 수행합니다.

1. 먼저 Livy Spark가 클러스터에서 실행 중인지 확인하도록 합니다. 실행 중인 배치 목록을 가져와서 확인할 수 있습니다. 처음으로 Livy를 사용하여 작업을 실행하는 경우 출력은 0을 반환해야 합니다.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

    다음 코드 조각과 유사한 결과가 표시됩니다.

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    출력의 마지막 줄이 실행 중인 배치가 없는 것을 나타내는 **total:0**을 말하는 방법을 확인합니다.

2. 이제 배치 작업을 제출하도록 합니다. 다음 코드 조각은 입력 파일(input.txt)을 사용하여 매개 변수로 jar 이름 및 클래스 이름을 전달합니다. Windows 컴퓨터에서 이러한 단계를 실행하는 경우 입력 파일을 사용하는 것이 권장됩니다.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    파일 **input.txt** 에서 매개 변수는 다음과 같이 정의됩니다.

    ```text
    { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    다음 코드 조각과 유사한 결과가 표시됩니다.

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    출력의 마지막 줄이 **state:starting**을 말하는 방법을 확인합니다. 또한 **id:0**을 말합니다. 여기서 **0**은 배치 ID입니다.

3. 이제 배치 ID를 사용하여 이 특정 배치의 상태를 검색할 수 있습니다.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    다음 코드 조각과 유사한 결과가 표시됩니다.

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    이제 출력은 작업이 성공적으로 완료됐음을 나타내는 **state:success**를 보여 줍니다.

4. 원하는 경우 이제 배치를 삭제할 수 있습니다.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    다음 코드 조각과 유사한 결과가 표시됩니다.

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    출력의 마지막 줄은 배치가 성공적으로 삭제된 것을 보여 줍니다. 실행 중인 작업을 삭제하면 작업이 종료됩니다. 성공적으로 완료됐거나 그렇지 않은 작업을 삭제하는 경우 작업 정보를 완전히 삭제합니다.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>HDInsight 버전 3.5로 시작하는 Livy 구성에 대한 업데이트

기본적으로 HDInsight 클러스터 3.5 이상은 로컬 파일 경로를 사용하여 샘플 데이터 파일이나 jar를 액세스하지 못하도록 합니다. 클러스터에서 jar 또는 샘플 데이터 파일에 액세스하는 대신 `wasb://` 경로를 사용하는 것이 좋습니다.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Azure Virtual Network 내에서 클러스터에 대한 Livy 작업 제출

Azure Virtual Network 내에서 HDInsight Spark 클러스터에 연결하는 경우 클러스터의 Livy에 직접 연결할 수 있습니다. 이러한 경우 Livy 엔드포인트의 URL은 `http://<IP address of the headnode>:8998/batches`입니다. 여기서 **8998**은 클러스터 헤드 노드에서 Livy가 실행되는 포트입니다. 비공용 포트의 서비스 액세스에 대한 자세한 내용은 [HDInsight의 Apache Hadoop 서비스에 사용되는 포트](../hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Apache Livy REST API 설명서](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)