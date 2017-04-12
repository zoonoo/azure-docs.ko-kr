---
title: "HDInsight에서 Spark를 사용하여 Application Insights 로그 분석 | Microsoft Docs"
description: "Application Insight 로그를 Blob 저장소에 내보낸 다음 HDInsight에서 Spark를 사용하여 로그를 분석하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/10/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: cb994df3712798d9016401235d4eff09b3518584
ms.lasthandoff: 04/12/2017


---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>HDInsight에서 Spark를 사용하여 Application Insights 원격 분석 로그 분석

HDInsight에서 Spark를 사용하여 Application Insights 원격 분석 데이터를 분석하는 방법에 대해 알아봅니다.

[Visual Studio Application Insights](../application-insights/app-insights-overview.md) 는 웹 응용 프로그램을 모니터링하는 분석 서비스입니다. Application Insights에 의해 생성된 원격 분석 데이터를 Azure Storage로 내보낼 수 있고 해당 위치에서 HDInsight로 분석할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독.

* 응용 프로그램에서 Application Insights를 사용하도록 구성합니다.

* Linux 기반 HDInsight 클러스터를 만드는 데 익숙해야 합니다. 자세한 내용은 [HDInsight에서 Spark 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

  > [!IMPORTANT]
  > 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date)을 참조하세요.

* 웹 브라우저.

이 문서를 개발하고 테스트하는 데 다음 리소스를 사용했습니다.

* [Application Insights를 사용하도록 구성된 Node.js 웹앱](../application-insights/app-insights-nodejs.md)를 사용하여 Application Insights 원격 분석 데이터를 생성했습니다.

* HDInsight 클러스터 버전 3.5의 Linux 기반 Spark는 데이터를 분석하는 데 사용되었습니다.

## <a name="architecture-and-planning"></a>아키텍처 및 계획

다음 다이어그램은 이 예제의 서비스 아키텍처를 보여 줍니다.

![다이어그램에서는 데이터를 Application Insights에서 Blob 저장소로 전달하고 HDInsight의 Spark에서 처리하는 방법을 보여 줍니다.](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure 저장소

Application Insights가 Blob에 원격 분석 정보를 지속적으로 내보내도록 구성될 수 있습니다. 그러면 HDInsight는 Blob에 저장된 데이터를 읽을 수 있습니다. 그러나 따라야 할 몇 가지 요구 사항이 있습니다.

* **위치**: 저장소 계정 및 HDInsight가 다른 위치에 있는 경우 대기 시간이 증가할 수 있습니다. 또한 지역 간에 이동하는 데이터에 송신 요금이 적용되면 비용이 증가합니다.
* **Blob 유형**: HDInsight는 블록 Blob만을 지원합니다. Application Insights의 기본값은 블록 Blob을 사용하므로 기본적으로 HDInsight와 함께 사용해야 합니다.
* **액세스 권한**: Application Insights 연속 내보내기와 HDInsight의 기본 저장소에 동일한 저장소 계정을 사용하는 경우 HDInsight는 Application Insight 원격 분석 데이터에 대한 전체 액세스 권한을 가집니다. 즉, HDInsight 클러스터에서 원격 분석 데이터를 삭제할 수 있습니다.

    대신, HDInsight 및 Application Insights 원격 분석에 별도의 저장소 계정을 사용하고 [SAS(공유 액세스 서명)를 사용하여 HDInsight에서 데이터에 액세스를 제한](hdinsight-storage-sharedaccesssignature-permissions.md)하는 것이 좋습니다. SAS를 사용하면 원격 분석 데이터에 대한 HDInsight 읽기 전용 액세스 권한을 부여할 수 있습니다.

### <a name="data-schema"></a>데이터 스키마

Application Insights는 Blob으로 내보낸 원격 분석 데이터 형식에 대한 [데이터 모델 내보내기](../application-insights/app-insights-export-data-model.md) 정보를 제공합니다. 이 문서의 단계에서는 Spark SQL을 데이터와 함께 사용합니다. Spark SQL은 분석을 수행할 때 스키마를 수동으로 정의하지 않도록 Application Insights에 의해 기록된 JSON 데이터 구조에 대한 스키마를 자동으로 생성할 수 있습니다.

## <a name="export-telemetry-data"></a>원격 분석 데이터 내보내기
[연속 내보내기 구성](../application-insights/app-insights-export-telemetry.md) 의 단계에 따라 Azure Storage Blob으로 원격 분석 정보를 내보내도록 Application Insights를 구성할 수 있습니다.

## <a name="configure-hdinsight-to-access-the-data"></a>HDInsight를 구성하여 데이터에 액세스
[SAS(공유 액세스 서명)를 사용하여 HDInsight에서 데이터에 대한 액세스 제한](hdinsight-storage-sharedaccesssignature-permissions.md) 에 있는 정보를 사용하여 내보낸 원격 분석 데이터를 보유하는 Blob 컨테이너에 대한 SAS를 만듭니다. SAS는 데이터에 대한 읽기 전용 액세스 권한을 제공해야 합니다.

공유 액세스 서명 문서에서는 기존 Linux 기반 HDInsight 클러스터에 SAS 저장소를 추가할 수 있는 방법을 제공합니다. 또한 새 HDInsight 클러스터를 만들 때 해당 저장소를 추가하는 방법도 제공합니다.

## <a name="analyze-the-data-using-python-pyspark"></a>Python를 사용하여 데이터 분석(PySpark)

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터의 Spark를 선택합니다. **빠른 링크** 섹션에서 **클러스터 대시보드**를 선택한 다음 클러스터 대시보드__ 블레이드에서 **Jupyter Notebook**을 선택합니다.

    ![클러스터 대시보드](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. Jupyter 페이지의 오른쪽 위 모퉁이에서 **새로 만들기**, **PySpark**를 차례로 선택합니다. Python 기반 Jupyter Notebook을 포함하는 새 브라우저 탭이 열립니다.

3. 페이지의 첫 번째 필드(**셀**이라고 함)에 다음 텍스트를 입력합니다.

        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')

    이 코드는 Spark가 입력 데이터에 대한 디렉터리 구조에 재귀적으로 액세스하도록 구성합니다. Application Insights 원격 분석은 `/{telemetry type}/YYYY-MM-DD/{##}/`과 유사한 디렉터리 구조에 기록됩니다.

4. **SHIFT+ENTER** 를 사용하여 코드를 실행합니다. '\*'가 셀의 왼쪽에 대괄호 사이에 표시되면 이 셀의 코드가 실행되고 있음을 나타냅니다. 완료되면 '\*'는 번호로 변경되고 셀 아래에 다음 텍스트와 유사한 출력이 표시됩니다.

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. 새 셀은 첫 번째 셀의 아래에 생성됩니다. 새 셀에서 다음 텍스트를 입력합니다. **CONTAINER** 및 **STORAGEACCOUNT**를 Application Insights 연속 내보내기를 구성할 때 사용한 Azure Storage 계정 이름 및 Blob 컨테이너 이름으로 바꿉니다.

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/

    **SHIFT+ENTER**를 사용하여 이 셀을 실행합니다. 다음 텍스트와 유사한 결과가 표시됩니다.

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    반환된 wasb 경로는 Application Insights 원격 분석 데이터의 위치입니다. 셀에서 `hdfs dfs -ls` 줄을 변경하여 반환된 wasb 경로를 사용한 다음 **SHIFT+ENTER**를 사용하여 셀을 다시 실행합니다. 이번 결과는 원격 분석 데이터를 포함하는 디렉터리를 표시해야 합니다.

   > [!NOTE]
   > 이 섹션의 나머지 단계에서는 `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` 디렉터리를 사용했습니다. 사용자의 디렉터리 구조는 다를 수 있습니다.

6. 다음 셀에 다음을 입력합니다. **WASB\_PATH**를 이전 단계의 경로로 바꿉니다.

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    이 코드는 연속 내보내기 프로세스에서 내보낸 JSON 파일에서 데이터 프레임을 만듭니다. **SHIFT+ENTER** 를 사용하여 이 셀을 실행합니다.
7. 다음 셀에서 다음을 입력하고 실행하여 Spark가 JSON 파일에 대해 만든 스키마를 봅니다.

        jsonData.printSchema()

    각 유형의 원격 분석에 대한 스키마는 달라질 수 있습니다. 다음 예제는 웹 요청(`Requests` 하위 디렉터리에 저장된 데이터)에 대해 생성되는 스키마입니다.

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. 다음을 사용하여 데이터 프레임을 임시 테이블로 등록하고 데이터에 대해 쿼리를 실행합니다.

        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")

    이 쿼리는 context.location.city가 null이 아닌 상위 20개 레코드에 대한 도시 정보를 반환합니다.

   > [!NOTE]
   > 상황에 맞는 구조는 Application Insights에 의해 기록된 모든 원격 분석에 있습니다. 그러나 도시 요소는 기록에 채워지지 않을 수 있습니다. 스키마를 사용하여 로그에 대한 데이터를 포함하는 쿼리할 수 있는 다른 요소를 식별합니다.
   >
   >

    이 쿼리는 다음 텍스트와 비슷한 정보를 반환합니다.

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-using-scala"></a>Scala를 사용하여 데이터 분석
1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터의 Spark를 선택합니다. **빠른 링크** 섹션에서 **클러스터 대시보드**를 선택한 다음 클러스터 대시보드__ 블레이드에서 **Jupyter Notebook**을 선택합니다.

    ![클러스터 대시보드](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. Jupyter 페이지의 오른쪽 위 모퉁이에서 **새로 만들기**, **Scala**를 차례로 선택합니다. Scala 기반 Jupyter Notebook을 포함하는 새 브라우저 탭이 열립니다.
3. 페이지의 첫 번째 필드(**셀**이라고 함)에 다음 텍스트를 입력합니다.

        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")

    이 코드는 Spark가 입력 데이터에 대한 디렉터리 구조에 재귀적으로 액세스하도록 구성합니다. Application Insights 원격 분석은 `/{telemetry type}/YYYY-MM-DD/{##}/`과 유사한 디렉터리 구조에 기록됩니다.

4. **SHIFT+ENTER** 를 사용하여 코드를 실행합니다. '\*'가 셀의 왼쪽에 대괄호 사이에 표시되면 이 셀의 코드가 실행되고 있음을 나타냅니다. 완료되면 '\*'는 번호로 변경되고 셀 아래에 다음 텍스트와 유사한 출력이 표시됩니다.

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. 새 셀은 첫 번째 셀의 아래에 생성됩니다. 새 셀에서 다음 텍스트를 입력합니다. **CONTAINER** 및 **STORAGEACCOUNT**를 Application Insights 연속 내보내기를 구성할 때 사용한 Azure Storage 계정 이름 및 Blob 컨테이너 이름으로 바꿉니다.

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/

    **SHIFT+ENTER**를 사용하여 이 셀을 실행합니다. 다음 텍스트와 유사한 결과가 표시됩니다.

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    반환된 wasb 경로는 Application Insights 원격 분석 데이터의 위치입니다. 셀에서 `hdfs dfs -ls` 줄을 변경하여 반환된 wasb 경로를 사용한 다음 **SHIFT+ENTER**를 사용하여 셀을 다시 실행합니다. 이번 결과는 원격 분석 데이터를 포함하는 디렉터리를 표시해야 합니다.

   > [!NOTE]
   > 이 섹션의 나머지 단계에서는 `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` 디렉터리를 사용했습니다. 원격 분석 데이터가 웹앱에 대한 것이 아니면 이 디렉터리는 없을 수도 있습니다. 요청 디렉터리를 포함하지 않는 원격 분석 데이터를 사용하는 경우 다른 디렉터리를 선택하고 단계의 나머지 부분을 조정하여 저장된 데이터에 대한 해당 디렉터리와 스키마를 사용합니다.
   >
   >
6. 다음 셀에 다음을 입력합니다. **WASB\_PATH**를 이전 단계의 경로로 바꿉니다.

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    이 코드는 연속 내보내기 프로세스에서 내보낸 JSON 파일에서 데이터 프레임을 만듭니다. **SHIFT+ENTER** 를 사용하여 이 셀을 실행합니다.
7. 다음 셀에서 다음을 입력하고 실행하여 Spark가 JSON 파일에 대해 만든 스키마를 봅니다.

        jsonData.printSchema

    각 유형의 원격 분석에 대한 스키마는 달라질 수 있습니다. 다음 예제는 웹 요청(`Requests` 하위 디렉터리에 저장된 데이터)에 대해 생성되는 스키마입니다.

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. 다음을 사용하여 데이터 프레임을 임시 테이블로 등록하고 데이터에 대해 쿼리를 실행합니다.

        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()

    이 쿼리는 context.location.city가 null이 아닌 상위 20개 레코드에 대한 도시 정보를 반환합니다.

   > [!NOTE]
   > 상황에 맞는 구조는 Application Insights에 의해 기록된 모든 원격 분석에 있습니다. 그러나 도시 요소는 기록에 채워지지 않을 수 있습니다. 스키마를 사용하여 로그에 대한 데이터를 포함하는 쿼리할 수 있는 다른 요소를 식별합니다.
   >
   >

    이 쿼리는 다음 텍스트와 비슷한 정보를 반환합니다.

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>다음 단계
Azure의 데이터와 서비스로 작업하기 위해 Spark를 사용하는 방법의 자세한 예제는 다음 문서를 참조하세요.

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)
* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)
* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Spark 응용 프로그램을 만들고 실행하는 자세한 내용은 다음 문서를 참조하세요.

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)
* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

