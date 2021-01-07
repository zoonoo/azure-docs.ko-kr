---
title: HDInsight에서 Apache Hadoop으로 MapReduce 및 Curl 사용 - Azure
description: Curl을 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업을 원격으로 실행하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: 407db727f521ea7731f0cbdbdd05c4338c9f452e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207727"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>REST를 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업 실행

Apache Hive WebHCat REST API를 사용 하 여 HDInsight 클러스터의 Apache Hadoop에서 MapReduce 작업을 실행 하는 방법에 대해 알아봅니다. Curl은 MapReduce 작업 실행을 원시 HTTP 요청을 사용하여 HDInsight와 함께 작용하는 방법을 설명하기 위해 사용됩니다.

> [!NOTE]  
> Linux 기반 Hadoop 서버를 사용 하는 방법을 이미 잘 알고 있지만 HDInsight를 처음 사용 하는 경우 [hdinsight의 linux 기반 Apache Hadoop](../hdinsight-hadoop-linux-information.md) 에 대해 알아야 할 사항 문서를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Hadoop 클러스터. [Azure Portal을 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.

다음 중 하나
  * Windows PowerShell 또는
  * [Jq](https://stedolan.github.io/jq/) 로 [말아 넘기기](https://curl.haxx.se/)

## <a name="run-a-mapreduce-job"></a>MapReduce 작업 실행

> [!NOTE]  
> Curl 또는 기타 모든 REST 통신을 WebHCat 함께 사용하면 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 서버로 요청을 보내는 데 사용하는 URI의 일부에 클러스터 이름을 사용해야 합니다.
>
> REST API는 [기본 액세스 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)을 사용하여 보안이 유지됩니다. 자격 증명이 안전하게 서버에 전송되려면 항상 HTTPS를 사용하여 요청을 보냅니다.

### <a name="curl"></a>Curl

1. 사용 편의성을 위해 아래 변수를 설정 합니다. 이 예제는 Windows 환경을 기반으로 하며, 사용자 환경에 필요한 대로 수정 합니다.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

   * **-u**: 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
   * **-G**: 이 작업이 GET 요청임을 나타냅니다.

   URL의 시작 부분, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`은 모든 요청에 대해 동일합니다.

    다음 JSON과 유사한 응답이 수신됩니다.

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce 작업을 제출 하려면 다음 명령을 사용 합니다. 필요에 따라 **jq** 경로를 수정 합니다.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    URI(/mapreduce/jar)의 끝부분은 WebHCat에 이 요청이 jar 파일의 클래스에서 MapReduce 작업을 시작함을 알려줍니다. 이 명령에서 사용된 매개 변수는 다음과 같습니다.

   * **-d**:가 `-G` 사용 되지 않으므로 요청은 POST 메서드로 기본 설정 됩니다. `-d` 는 요청과 함께 전송되는 데이터 값을 지정합니다.
     * **user.name**: 명령을 실행하는 사용자입니다.
     * **jar**: 실행할 클래스가 포함된 jar 파일의 위치입니다.
     * **class**: MapReduce 논리가 포함된 클래스입니다.
     * **arg**: MapReduce 작업에 전달할 인수. 이 경우는 출력에 사용되는 입력 텍스트 파일과 디렉터리입니다.

    이 명령은 작업 상태를 확인 하는 데 사용할 수 있는 작업 ID를 반환 `job_1415651640909_0026` 해야 합니다.

1. 작업 상태를 확인하려면 다음 명령을 사용합니다. 의 값을 `JOBID` 이전 단계에서 반환 된 **실제** 값으로 바꿉니다. 필요에 따라 **jq** 의 위치를 수정 합니다.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. 사용 편의성을 위해 아래 변수를 설정 합니다. `CLUSTERNAME`을 실제 클러스터 이름으로 바꿉니다. 명령을 실행 하 고 메시지가 표시 되 면 클러스터 로그인 암호를 입력 합니다.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. 다음 명령을 사용 하 여 HDInsight 클러스터에 연결할 수 있는지 확인 합니다.

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    다음 JSON과 유사한 응답이 수신됩니다.

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce 작업을 제출하려면 다음 명령을 사용합니다.

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    URI(/mapreduce/jar)의 끝부분은 WebHCat에 이 요청이 jar 파일의 클래스에서 MapReduce 작업을 시작함을 알려줍니다. 이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **user.name**: 명령을 실행하는 사용자입니다.
    * **jar**: 실행할 클래스가 포함된 jar 파일의 위치입니다.
    * **class**: MapReduce 논리가 포함된 클래스입니다.
    * **arg**: MapReduce 작업에 전달할 인수. 이 경우는 출력에 사용되는 입력 텍스트 파일과 디렉터리입니다.

   이 명령은 작업 상태를 확인 하는 데 사용할 수 있는 작업 ID를 반환 `job_1415651640909_0026` 해야 합니다.

1. 작업 상태를 확인하려면 다음 명령을 사용합니다.

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing

    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

### <a name="both-methods"></a>두 방법 모두

1. 작업이 완료된 경우 상태는 `SUCCEEDED`입니다.

1. 작업 상태가 `SUCCEEDED`로 변경되면 Azure Blob Storage에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달되는 `statusdir` 매개 변수에는 출력 파일의 위치가 포함됩니다. 이 예제에서 위치는 `/example/curl`입니다. 이 주소는 작업의 출력을 클러스터 기본 스토리지인 `/example/curl`에 저장합니다.

[Azure CLI](/cli/azure/install-azure-cli)를 사용하여 이러한 파일을 나열하고 다운로드할 수 있습니다. Azure CLI를 사용 하 여 Azure Blob storage로 작업 하는 방법에 대 한 자세한 내용은 [빠른 시작: Azure CLI를 사용 하 여 Blob 만들기, 다운로드 및 나열](../../storage/blobs/storage-quickstart-blobs-cli.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)

이 문서에 사용된 REST 인터페이스에 대한 자세한 내용은 [WebHCat 참조](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)를 참조하세요.
