---
title: "HDInsight에서 Curl을 통해 Hadoop Hive 사용 - Azure | Microsoft Docs"
description: "원격으로 Curl을 사용하여 HDInsight에 Pig 작업을 제출하는 방법에 대해 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: 07386c2fbb5fe4ce4fe1ca6844f6308cf59cbfb4
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>REST를 사용하여 HDInsight에서 Hadoop으로 Hive 쿼리 실행

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

WebHCat REST API를 사용하여 Azure HDInsight 클러스터에서 Hadoop으로 Hive 쿼리를 실행하는 방법에 대해 알아보세요.

[Curl](http://curl.haxx.se/)은 원시 HTTP 요청을 사용하여 HDInsight와 상호 작용하는 방법을 보여주는 데 사용합니다. [jq](http://stedolan.github.io/jq/) 유틸리티는 REST 요청에서 반환된 JSON 데이터를 처리하는 데 사용합니다.

> [!NOTE]
> Linux 기반 Hadoop 서버를 익숙하게 사용하지만 HDInsight는 처음인 경우 [HDInsight의 Linux 기반 Hadoop에 대해 알아야 할 정보](../hdinsight-hadoop-linux-information.md) 문서를 참조하세요.

## <a id="curl"></a>Hive 쿼리 실행

> [!NOTE]
> WebHCat에서 cURL 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다.
>
> 이 섹션의 명령에서 **admin**을 클러스터에 인증할 사용자로 바꿉니다. **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다. 메시지가 표시되면 사용자 계정의 암호를 입력합니다.
>
> REST API는 [기본 인증](http://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 Secure HTTP(HTTPS)를 사용하여 요청하십시오.

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    다음 텍스트와 유사한 응답이 수신됩니다.

        {"status":"ok","version":"v1"}

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-u** - 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
    * **-G** - 이 요청이 GET 작업임을 나타냅니다.

   URL 시작 부분인 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**은 모든 요청에서 동일합니다. **/status** 경로는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다. 또한 다음 명령을 사용하여 Hive의 버전을 요청할 수 있습니다.

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    이 요청은 다음 텍스트와 유사한 응답을 반환합니다.

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. 다음을 사용하여 **log4jLogs**라는 새 테이블을 만듭니다.

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    이 요청에는 사용된 매개 변수는 다음과 같습니다.

   * **-d** - `-G`가 사용되지 않으므로 요청은 POST 메서드로 기본 설정됩니다. `-d` 는 요청과 함께 전송되는 데이터 값을 지정합니다.

     * **user.name** - 명령을 실행하는 사용자입니다.
     * **execute** - 실행할 HiveQL 문입니다.
     * **statusdir** - 이 작업의 상태를 기록할 디렉터리입니다.

   이러한 문은 다음 작업을 수행합니다.
   
   * **DROP TABLE** - 이미 테이블이 있으면 삭제됩니다.
   * **CREATE EXTERNAL TABLE** - Hive에서 새 ‘외부’ 테이블을 만듭니다. 외부 테이블은 테이블 정의만 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

     > [!NOTE]
     > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예를 들어 자동화된 데이터 업로드 프로세스 또는 다른 MapReduce 작업이 있습니다.
     >
     > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

   * **ROW FORMAT** - 데이터의 형식을 지정하는 방식입니다. 각 로그의 필드는 공백으로 구분됩니다.
   * **STORED AS TEXTFILE LOCATION** - 데이터가 저장되는 위치(example/data 디렉터리). 이 데이터는 텍스트로 저장됩니다.
   * **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 값이 포함된 행이 3개이므로 이 문은 **3** 값을 반환합니다.

     > [!NOTE]
     > Curl과 함께 사용할 경우 HiveQL 문 사이의 공백이 `+` 문자로 바뀝니다. 구분 기호와 같이 공백을 포함하는 따옴표로 묶인 값은 `+`로 바뀌지 않아야 합니다.

   * **INPUT__FILE__NAME LIKE '%25.log'** - 이 문은 .log로 끝나는 파일만 사용하기 위해 검색을 제한합니다.

     > [!NOTE]
     > `%25`는 %의 URL 인코딩 형식이므로 실제 조건은 `like '%.log'`입니다. %를 URL로 인코드해야 하므로 URL에서 특수 문자로 처리됩니다.

   이 명령은 작업 상태를 확인하는 데 사용할 수 있는 작업 ID를 반환합니다.

       {"id":"job_1415651640909_0026"}

3. 작업 상태를 확인하려면 다음 명령을 사용합니다.

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    **JOBID** 를 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어 반환 값이 `{"id":"job_1415651640909_0026"}`인 경우 **JOBID**는 `job_1415651640909_0026`이 됩니다.

    작업이 완료된 경우 상태는 **SUCCEEDED**입니다.

   > [!NOTE]
   > 이 Curl 요청은 작업에 관한 정보가 있는 JSON(JavaScript Object Notation) 문서를 반환합니다. Jq는 상태 값을 검색하는 데에만 사용됩니다.

4. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob Storage에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 경우 **/example/curl**)가 포함됩니다. 이 주소는 클러스터 기본 저장소의 **example/curl** 디렉터리에 출력을 저장합니다.

    [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)를 사용하여 이러한 파일을 나열하고 다운로드할 수 있습니다. Azure Storage에서 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure Storage에서 Azure CLI 2.0 사용](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs) 문서를 참조하세요.

5. 다음 문을 사용하여 **errorLogs**라는 새 ‘내부' 테이블을 만듭니다.

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    이러한 문은 다음 작업을 수행합니다.

   * **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없는 경우 테이블을 만듭니다. 이 문은 Hive 데이터 웨어하우스에 저장되는 내부 테이블을 만듭니다. 이 테이블은 Hive에서 관리합니다.

     > [!NOTE]
     > 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

   * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. ORC는 Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
   * **덮어쓰기 삽입... SELECT** - **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.
   * **SELECT** - 새 **errorLogs** 테이블에서 모든 행을 선택합니다.

6. 작업 상태를 확인하려면 반환된 작업 ID를 사용합니다. 작업이 성공하면 이전에 설명한 대로 Azure CLI를 사용하여 결과를 다운로드하고 볼 수 있습니다. 결과는 **[ERROR]**를 포함하여 모두 3줄이어야 합니다.

## <a id="nextsteps"></a>다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

Hive와 함께 Tez를 사용하는 경우 디버깅 정보에 대한 다음 문서를 참조하세요.

* [Linux 기반 HDInsight에서 Ambari Tez 보기 사용](../hdinsight-debug-ambari-tez-view.md)

이 문서에 사용된 REST API에 대한 자세한 내용은 [WebHCat 참조](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) 문서를 참조하세요.

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


