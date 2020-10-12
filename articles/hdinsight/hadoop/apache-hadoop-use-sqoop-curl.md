---
title: Azure HDInsight에서 말아를 사용 하 여 Apache Sqoop로 데이터 내보내기
description: 말아 넘기기를 사용 하 여 Azure HDInsight에 Apache Sqoop 작업을 원격으로 제출 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 9104be9975568c52f6a96994a0afb782a406fe4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86076269"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>' 말아 '를 사용 하 여 HDInsight에서 Apache Sqoop 작업 실행

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight의 Apache Hadoop 클러스터에서 Curl을 사용하여 Apache Sqoop 작업을 실행하는 방법에 대해 알아봅니다. 이 문서에서는 Azure Storage에서 데이터를 내보내고, 말아를 사용 하 여 SQL Server 데이터베이스로 가져오는 방법을 보여 줍니다. 이 문서는 [HDInsight에서 Hadoop과 함께 Apache Sqoop 사용](./hdinsight-use-sqoop.md)의 연속입니다.

Sqoop 작업을 실행하고 모니터링하며 결과를 검색하는 원시 HTTP 요청을 사용하여 HDInsight와 상호 작용하는 방법을 설명하는 데 Curl을 사용합니다. 이 Curl은 HDInsight 클러스터에서 제공하는 WebHCat REST API(이전의 Templeton)를 사용하여 작동합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [HDInsight에서 Hadoop과 함께 Apache Sqoop 사용](./hdinsight-use-sqoop.md)에서 [테스트 환경 설정](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) 완료.

* Azure SQL Database를 쿼리 하는 클라이언트입니다. [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 또는 [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md)를 사용 하는 것이 좋습니다.

* [말아](https://curl.haxx.se/). Curl은 HDInsight 클러스터에서(로) 데이터를 전송하는 도구입니다.

* [jq](https://stedolan.github.io/jq/). jq 유틸리티는 REST 요청에서 반환된 JSON 데이터를 처리하는 데 사용합니다.

* Sqoop에 익숙해야 합니다. 자세한 내용은 [Sqoop 사용자 가이드](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)를 참조하세요.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Curl을 사용하여 Apache Sqoop 작업 제출

Azure Storage에서 Apache Sqoop 작업을 사용 하 여 데이터를 SQL Server로 내보내려면 말아를 사용 합니다.

> [!NOTE]  
> WebHCat에서 Curl 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 클러스터 이름을 서버로 요청을 보내는 데 사용되는 URI(Uniform Resource Identifier)의 일부로 사용해야 합니다.

이 섹션의 명령에 대해를 `USERNAME` 클러스터에 인증할 사용자로 바꾸고,를 `PASSWORD` 사용자 계정의 암호로 바꿉니다. `CLUSTERNAME`을 클러스터의 이름으로 바꿉니다.

REST API는 [기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 보안 HTTP(HTTPS)를 사용하여 요청해야 합니다.

1. 사용 편의성을 위해 아래 변수를 설정 합니다. 이 예제는 Windows 환경을 기반으로 하며, 사용자 환경에 필요한 대로 수정 합니다.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    그러면 다음과 같은 응답이 표시됩니다.

    ```json
    {"status":"ok","version":"v1"}
    ```

1. 다음을 사용하여 Sqoop 작업을 제출합니다.

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

   * **-d** -가 `-G` 사용 되지 않으므로 요청은 POST 메서드로 기본 설정 됩니다. `-d` 는 요청과 함께 전송되는 데이터 값을 지정합니다.

       * **user.name** - 명령을 실행하는 사용자입니다.

       * **명령** - 실행할 Sqoop 명령입니다.

       * **statusdir** - 이 작업의 상태를 기록할 디렉터리입니다.

     이 명령은 작업 상태를 확인 하는 데 사용할 수 있는 작업 ID를 반환 합니다.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. 작업 상태를 확인하려면 다음 명령을 사용합니다. `JOBID`를 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어 반환 값이 인 경우는 `{"id":"job_1415651640909_0026"}` `JOBID` `job_1415651640909_0026` 입니다. 필요에 따라의 위치를 수정 `jq` 합니다.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    작업이 완료되면 상태는 **SUCCEEDED**가 됩니다.

   > [!NOTE]  
   > 이 Curl 요청은 작업에 관한 정보가 있는 JSON(JavaScript Object Notation) 문서를 반환합니다. jq는 상태 값을 검색하는 데만 사용됩니다.

1. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob Storage에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 경우 `wasb:///example/data/sqoop/curl`)가 포함됩니다. 이 주소는 `example/data/sqoop/curl` HDInsight 클러스터에서 사용 하는 기본 저장소 컨테이너의 디렉터리에 작업의 출력을 저장 합니다.

    Azure Portal을 사용하여 stderr 및 stdout Blob에 액세스할 수 있습니다.

1. 데이터를 내보낼지 확인 하려면 SQL 클라이언트에서 다음 쿼리를 사용 하 여 내보낸 데이터를 확인 합니다.

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>제한 사항

* 대량 내보내기-Linux 기반 HDInsight를 사용 하는 경우 Microsoft SQL Server 또는 Azure SQL Database로 데이터를 내보내는 데 사용 되는 Sqoop 커넥터는 현재 대량 삽입을 지원 하지 않습니다.
* Batch - Linux 기반 HDInsight와 함께 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop는 삽입 작업을 일괄 처리하는 대신 여러 삽입 작업을 수행합니다.

## <a name="summary"></a>요약

이 문서에서 볼 수 있듯이 원시 HTTP 요청을 사용하여 HDInsight 클러스터의 Sqoop 작업을 실행하고 모니터링하며 결과를 확인할 수 있습니다.

이 문서에 사용된 REST 인터페이스에 대한 자세한 내용은 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API 가이드</a>를 참조하세요.

## <a name="next-steps"></a>다음 단계

[HDInsight에서 Apache Hadoop과 함께 Apache Sqoop 사용](hdinsight-use-sqoop.md)

curl 관련 기타 HDInsight 문서:

* [Azure REST API를 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [REST를 사용하여 HDInsight에서 Apache Hadoop과 함께 Apache Hive 쿼리 실행](apache-hadoop-use-hive-curl.md)
* [REST를 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업 실행](apache-hadoop-use-mapreduce-curl.md)
