<properties
   pageTitle="HDInsight에서 Hadoop Pig 사용 | Azure"
   description="원격으로 Curl을 사용하여 HDInsight에 Pig 작업을 제출하는 방법에 대해 알아봅니다."
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

# Curl을 사용하여 HDInsight에서 Hadoop으로 Hive 쿼리 실행

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

이 문서에서 HDInsight 클러스터의 Hadoop에서 Hive 쿼리를 실행하기 위해 Curl을 사용하는 방법에 대해 배웁니다. 

Curl은 실행, 모니터링 및 Hive 쿼리 검색 결과를 원시 HTTP 요청을 사용하여 HDInsight와 함께 작용하는 방법을 설명하기 위해 사용됩니다. HDInsight 클러스터에서 제공한 WebHCat REST API(이전의 Templeton)를 사용하여 작업합니다.

> [AZURE.NOTE] 이미 익숙한 Linux 기반 Hadoop 서버를 사용하지만 HDInsight는 처음인 경우, <a href="../hdinsight-hadoop-linux-information/" target="_blank">Linux 기반 HDInsight에서 Hadoop에 대해 알아야 할 내용</a>을 참조하세요.

## <a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터에서 Hadoop(Linux 또는 Windows 기반)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Curl을 사용하여 Hive 쿼리 실행

> [AZURE.NOTE] Curl 또는 기타 모든 REST 통신을 WebHCat 함께 사용하면 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 요청을 보낼 서버에 사용되는 URI의 일부로 클러스터 이름을 사용해야 합니다.
> 
> 이 섹션에 있는 명령의 경우 **USERNAME**은 클러스터에 인증한 사용자로, **PASSWORD**는 사용자 계정 암호로 바꿉니다. **CLUSTERNAME**은 클러스터의 이름으로 바꿉니다.
> 
> 기본 인증을 사용하여 <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">REST API의 보안을 설정합니다.</a>. 자격 증명이 안전하게 서버에 전송되려면 항상 HTTPS를 사용하여 요청을 보냅니다.

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    그러면 다음과 같은 응답이 표시됩니다.

        {"status":"ok","version":"v1"}

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-u** - 요청을 인증하는데 사용되는 사용자 이름 및 암호
    * **-G** - GET 요청임을 나타냅니다.

    Url 시작 부분인 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**은 모든 요청에서 동일합니다. **/status** 경로는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다. 또한 다음 명령을 사용하여 Hive의 버전을 요청할 수 있습니다.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    그러면 다음과 같은 응답이 반환됩니다.

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. **log4jLogs**라는 새 테이블을 만들려면 다음 문을 사용합니다.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-d** - `-G`를 사용하지 않았다면 요청은 POST 메서드를 기본으로 합니다. `-d`는 요청과 함께 전송되는 데이터 값을 지정합니다.

        * **user.name** - 명령을 실행하는 사용자
        
        * **execute** - HiveQL 문 실행
        
        * **statusdir** - 이 작업 상태를 기록할 디렉터리

    이러한 문은 다음 작업을 수행합니다.

    * **DROP TABLE** - 테이블과 데이터 파일을 삭제합니다(테이블이 이미 있는 경우).
    
    * **CREATE EXTERNAL TABLE** - Hive에서 새 'external' 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.

		> [AZURE.NOTE] 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
		>
		> 외부 테이블을 삭제하면 데이터**뿐만 아니라** 테이블 정의도 삭제됩니다.

    * **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
    
    * **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치와 텍스트로 저장되었다는 것을 Hive에 알립니다.
    
    * **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 값이 포함된 3개 행이 있으므로 **3** 값이 반환되어야 합니다.

    > [AZURE.NOTE] Curl와 함께 사용하는 경우 HiveQL 문 사이의 공백은 `+` 문자로 바뀝니다. 구분자와 같이 공백을 포함하는 따옴표로 둘러싼 값은 `+`로 바뀌지 않아야 합니다.

    이 명령은 작업 상태를 확인하는데 사용할 수 있는 작업 ID를 반환해야 합니다.

        {"id":"job_1415651640909_0026"}

3. 작업 상태를 확인하려면 다음 명령을 사용합니다. **JOBID**는 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어, 반환 값이 `{"id":"job_1415651640909_0026"}`이면 JOBID는 `job_1415651640909_0026`이 됩니다.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	작업이 완료되면, 상태는 "SUCCEEDED"가 됩니다.

    > [AZURE.NOTE] 이 curl 요청은 작업에 관한 정보가 있는 JSON 문서를 반환합니다. jq는 상태 값을 검색하는데만 사용됩니다. 

4. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob 저장소에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수는 출력 파일의 위치를 포함합니다. 이 경우 **wasb:///example/curl**. HDInsight 클러스터에서 사용하는 기본 저장소 컨테이너의 **example/curl** 디렉터리에 작업의 출력을 저장합니다.

    Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)를 사용하여 <a href="../xplat-cli/" target="_blank">이러한 파일을 나열하고 다운로드합니다</a>. 예를 들어 **example/curl**에 있는 파일을 나열하고 다음 명령을 사용합니다.

		azure storage blob list <container-name> example/curl

	파일을 다운로드하려면 다음을 사용합니다.

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] `-a` 및 `-k` 매개 변수를 사용하여 blob을 포함하는 저장소 계정 이름을 지정하거나 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS\_KEY** 환경 변수를 설정합니다. <a href="../hdinsight-upload-data/" target="_blank" for more information. 참조

6. **errorLogs**라는 새 테이블을 만들려면 다음을 사용합니다.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    이러한 문은 다음 작업을 수행합니다.

    * **CREATE TABLE IF NOT EXISTS** - 테이블을 만듭니다(아직 없는 경우). **EXTERNAL** 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 '내부' 테이블입니다.

		> [AZURE.NOTE] **EXTERNAL** 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

    * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
    * **INSERT OVERWRITE ... SELECT** - **[ERROR]**를 포함하는 **log4jLogs** 테이블에서 열을 선택한 후 **errorLogs** 테이블에 데이터를 삽입합니다.
    * **SELECT** - 새 **errorLogs** 테이블에서 모든 행을 선택합니다.

7. 작업 상태를 확인하려면 반환된 작업 ID를 사용합니다. 작업이 성공하면 이전에 설명한 대로 xplat-cli을 사용하여 결과를 다운로드하고 볼 수 있습니다. 결과는 **[ERROR]**를 포함하여 모두 3줄이어야 합니다.


## <a id="summary"></a>요약

이 문서에서 볼 수 있듯이, 실행, 모니터링 및 HDInsight 클러스터의 Hive 작업의 결과 확인하려면 원시 HTTP 요청을 사용할 수 있습니다.

이 문서에서 사용된 REST 인터페이스에 대한 자세한 정보는 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat 참조</a>를 참조하세요.

## <a id="nextsteps"></a>다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](../hdinsight-use-hive/)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Pig 사용](../hdinsight-use-pig/)

* [HDInsight에서 Hadoop과 MapReduce 사용](../hdinsight-use-mapreduce/)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/ pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/ documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=45--> 
