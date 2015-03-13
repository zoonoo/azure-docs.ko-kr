<properties
   pageTitle="HDInsight에서 Hadoop Pig 사용 | Azure"
   description="SSH를 사용하여 HDInsight에서 Hadoop과 Pig를 사용하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>
 
# Curl을 사용하여 HDInsight에서 Hadoop과 Pig 작업 실행

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

이 문서에서 HDInsight 클러스터의 Hadoop에서 Pig Latin 작업을 실행하기 위해 Curl을 사용하는 방법에 대해 배웁니다. Pig Latin 프로그래밍 언어를 사용하면 원하는 출력을 생성하기 위해 입력 데이터에 적용한 변환 내용을 설명할 수 있습니다.

Curl은 실행, 모니터링 및 Pig 작업 결과를 검색하기 위해 원시 HTTP 요청을 사용하여 HDInsight와 작용하는 방법을 설명하기 위해 사용 됩니다. HDInsight 클러스터에서 제공한 WebHCat REST API(이전의 Templeton)를 사용하여 작업합니다.

> [AZURE.NOTE] 이미 익숙한 Linux 기반 Hadoop 서버를 사용하지만 HDInsight는 처음인 경우 <a href="../hdinsight-hadoop-linux-information/" target="_blank">HDInsight에서 Linux 기반 Hadoop에 대해 알아야 할 내용</a>을 참조하세요.

## <a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Azure HDInsight(HDInsight의 Hadoop)클러스터(Linux 또는 Windows 기반)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Curl을 사용하여 Pig 작업 실행

> [AZURE.NOTE] Curl 또는 기타 모든 REST 통신을 WebHCat 함께 사용하면 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 요청을 보낼 서버에 사용되는 URI의 일부로 클러스터 이름을 사용해야 합니다.
> 
> 이 섹션에 있는 명령의 경우 **USERNAME**은 클러스터에 인증한 사용자로, **PASSWORD**는 사용자 계정 암호로 바꿉니다. **CLUSTERNAME**은 클러스터의 이름으로 바꿉니다.
> 
> 기본 인증을 사용하여 <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">REST API의 보안을 설정합니다</a>. 자격 증명이 안전하게 서버에 전송되려면 항상 HTTPS를 사용하여 요청을 보냅니다.

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다. 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    그러면 다음과 같은 응답이 표시됩니다.

        {"status":"ok","version":"v1"}

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-u** - 요청을 인증하는데 사용되는 사용자 이름 및 암호
    * **-G** - GET 요청임을 나타냅니다.

    Url 시작 부분인 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**은 모든 요청에서 동일합니다. **/status** 경로는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다.

2. 다음을 사용하여 클러스터에 Pig Latin 작업을 제출합니다.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-d** - `-G`를 사용하지 않았다면 요청은 POST 메서드를 기본으로 합니다. `-d`는 요청과 함께 전송되는 데이터 값을 지정합니다.

        * **user.name** - 명령을 실행하는 사용자
        * **execute** - 실행할 Pig Latin 문
        * **statusdir** - 이 작업 상태를 기록할 디렉터리

    > [AZURE.NOTE] Curl과 함께 사용하면 Pig Latin 문에서 공백은 `+` 문자로 대치됩니다.

    이 명령은 작업 상태를 확인하는데 사용할 수 있는 작업 ID를 반환해야 합니다.

        {"id":"job_1415651640909_0026"}

3. 작업 상태를 확인하려면 다음 명령을 사용합니다. **JOBID**는 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어, 반환 값이 `{"id":"job_1415651640909_0026"}`이면 JOBID는 `job_1415651640909_0026`이 됩니다.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	작업이 완료되면 상태는, "SUCCEEDED"가 됩니다.

    > [AZURE.NOTE] 이 curl 요청은 작업에 관한 정보가 있는 JSON 문서를 반환합니다. jq는 상태 값을 검색하는데만 사용됩니다. 

## <a id="results"></a>결과 보기

작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob 저장소에서 작업 결과를 검색할 수 있습니다. `statusdir`  매개 변수는 출력 파일의 위치를 포함한 쿼리를 전달합니다. 이 경우 **wasb:///example/pigcurl**. 이 주소는 HDInsight 클러스터에서 사용된 기본 저장소 컨테이너의 **example/pigcurl** 디렉터리에 있는 작업의 출력을 저장합니다.

Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)를 사용하여 <a href="../xplat-cli/" target="_blank">이러한 파일을 나열하고 다운로드합니다</a>. 예를 들어 **example/pigcurl**에 파일을 나열하려면 다음 명령을 사용합니다.

	azure storage blob list <container-name> example/pigcurl

파일을 다운로드하려면 다음을 사용합니다.

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] `-a` 및 `-k` 매개 변수를 사용하여 blob을 포함하는 저장소 계정 이름을 지정하거나 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS\_KEY** 환경 변수를 설정합니다. <a href="../hdinsight-upload-data/" target="_blank" for more information. 참조


## <a id="summary"></a>요약

이 문서에서 볼 수 있듯이, 실행, 모니터링 및 HDInsight 클러스터에서 Pig 작업의 결과를 확인하려면 원시 HTTP 요청을 사용할 수 있습니다.

이 문서에 사용된 REST 인터페이스에 대한 자세한 정보는 [WebHCat 참조](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)를 참조하세요.

## <a id="nextsteps"></a>다음 단계

HDInsight에서 Pig의 일반적인 내용.

* [HDInsight에서 Hadoop과 Pig 사용](../hdinsight-use-pig/)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](../hdinsight-use-hive/)

* [HDInsight에서 Hadoop과 MapReduce 사용](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
