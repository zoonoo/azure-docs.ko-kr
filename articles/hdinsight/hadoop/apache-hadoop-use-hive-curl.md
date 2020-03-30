---
title: HDInsight에서 Curl과 Apache Hadoop Hive 사용 - Azure
description: 컬을 사용하여 아파치 돼지 작업을 Azure HDInsight에 원격으로 제출하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298753"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>REST를 사용하여 HDInsight에서 Apache Hadoop과 함께 Apache Hive 쿼리 실행

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

WebHCat REST API를 사용하여 Azure HDInsight 클러스터에서 Apache Hadoop과 함께 Apache Hive 쿼리를 실행하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Hadoop 클러스터. [리눅스에서 HDInsight로 시작하기를](./apache-hadoop-linux-tutorial-get-started.md)참조하십시오.

* REST 클라이언트 이 문서는 Windows PowerShell에서 [호출-웹Request를](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) 사용하고 [Bash에서](https://docs.microsoft.com/windows/wsl/install-win10) [Curl을](https://curl.haxx.se/) 사용합니다.

* Bash를 사용하는 경우 명령줄 JSON 프로세서인 jq도 필요합니다.  을 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)참조하십시오.

## <a name="base-uri-for-rest-api"></a>나머지 API에 대한 기본 URI

HDInsight의 REST API에 대한 기본 균일 리소스 `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`식별자(URI)는 클러스터 의 `CLUSTERNAME` 이름입니다.  URI의 클러스터 이름은 **대/소문자를 구분합니다.**  URI(URI)의`CLUSTERNAME.azurehdinsight.net`정규화된 도메인 이름(FQDN) 부분의 클러스터 이름은 대/소문자를 구분하지 않지만 URI의 다른 발생은 대/소문자를 구분합니다.

## <a name="authentication"></a>인증

WebHCat에서 cURL 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. REST API는 [기본 인증](https://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 Secure HTTP(HTTPS)를 사용하여 요청하십시오.

### <a name="setup-preserve-credentials"></a>설정(자격 증명 보존)

자격 증명을 보존하여 각 예제에 대해 자격 증명을 다시 입력하지 않도록 합니다.  클러스터 이름은 별도의 단계로 유지됩니다.

**A. 배쉬**  
실제 암호로 대체하여 `PASSWORD` 아래 스크립트를 편집합니다.  그런 다음 명령을 입력합니다.

```bash
export password='PASSWORD'
```  

**B. PowerShell** 아래 코드를 실행하고 팝업 창에서 자격 증명을 입력합니다.

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>올바르게 대/소문자 클러스터 이름 식별

클러스터 생성 방법에 따라 클러스터 이름의 실제 대/소문자가 예상과 다를 수 있습니다.  여기에 있는 단계는 실제 대/소문자와 함께 다음 모든 이후 예제에 대 한 변수에 저장 됩니다.

클러스터 이름으로 바꾸려면 `CLUSTERNAME` 아래 스크립트를 편집합니다. 그런 다음 명령을 입력합니다. (FQDN의 클러스터 이름은 대/소문자를 구분하지 않습니다.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>HIVE 쿼리 실행

1. HDInsight 클러스터에 연결할 수 있는지 확인하려면 다음 명령 중 하나를 사용합니다.

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    다음 텍스트와 유사한 응답이 수신됩니다.

    ```json
    {"status":"ok","version":"v1"}
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * `-u` - 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
    * `-G` - 이 요청이 GET 작업임을 나타냅니다.

1. URL의 시작 부분, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`은 모든 요청에 대해 동일합니다. 경로, `/status`는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다. 또한 다음 명령을 사용하여 Hive의 버전을 요청할 수 있습니다.

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    이 요청은 다음 텍스트와 유사한 응답을 반환합니다.

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. 다음을 사용하여 **log4jLogs**라는 새 테이블을 만듭니다.

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    이 요청은 REST API에 대한 요청의 일부로 데이터를 전송하는 POST 메서드를 사용합니다. 다음 데이터 값은 요청과 함께 전송됩니다.

     * `user.name` - 명령을 실행하는 사용자입니다.
     * `execute` - 실행할 HiveQL 문입니다.
     * `statusdir` - 이 작업의 상태를 기록할 디렉터리입니다.

   이러한 문은 다음 작업을 수행합니다.

   * `DROP TABLE`- 테이블이 이미 있으면 삭제됩니다.
   * `CREATE EXTERNAL TABLE` - Hive에서 새 "외부" 테이블을 만듭니다. 외부 테이블은 테이블 정의만 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

     > [!NOTE]  
     > 외부 원본에서 기본 데이터를 업데이트하길 원하는 경우에는 외부 테이블을 사용해야 합니다. 예를 들어 자동화된 데이터 업로드 프로세스 또는 다른 MapReduce 작업이 있습니다.
     >
     > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

   * `ROW FORMAT` - 데이터의 형식을 지정하는 방식입니다. 각 로그의 필드는 공백으로 구분됩니다.
   * `STORED AS TEXTFILE LOCATION`- 데이터가 저장되는 위치(예제/데이터 디렉토리) 및 텍스트로 저장되는 위치입니다.
   * `SELECT`- 열 **t4값** **[ERROR]을**포함하는 모든 행의 수를 선택합니다. 이 값이 포함된 행이 3개이므로 이 문은 **3** 값을 반환합니다.

     > [!NOTE]  
     > Curl과 함께 사용할 경우 HiveQL 문 사이의 공백이 `+` 문자로 바뀝니다. 구분 기호와 같이 공백을 포함하는 따옴표로 묶인 값은 `+`로 바뀌지 않아야 합니다.

      이 명령은 작업 상태를 확인하는 데 사용할 수 있는 작업 ID를 반환합니다.

1. 작업 상태를 확인하려면 다음 명령을 사용합니다.

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

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

    작업이 완료된 경우 상태는 **SUCCEEDED**입니다.

1. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob Storage에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 경우 `/example/rest`)가 포함됩니다. 이 주소는 클러스터 기본 스토리지의 `example/curl` 디렉터리에 출력을 저장합니다.

    [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 사용하여 이러한 파일을 나열하고 다운로드할 수 있습니다. Azure Storage에서 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure Storage에서 Azure CLI 사용](https://docs.microsoft.com/azure/storage/storage-azure-cli) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

이 문서에 사용된 REST API에 대한 자세한 내용은 [WebHCat 참조](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) 문서를 참조하세요.