<properties
   pageTitle="HDInsight에서 Hadoop Hive 사용 | Azure"
   description="웹 기반 HDInsight 쿼리 콘솔에서 Hive를 사용하는 방법에 대해 알아봅니다."
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

# 쿼리 콘솔을 사용하여 Hive 쿼리 실행

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

이 문서에서는 브라우저에서 HDInsight Hadoop 클러스터의 Hive 쿼리를 실행하려면 HDInsight 쿼리 콘솔을 사용하는 방법에 대해 배웁니다.

> [AZURE.NOTE] 쿼리 콘솔은 Windows 기반 HDInsight 클러스터에서만 사용할 수있습니다.

## <a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Windows 기반 HDInsight Hadoop 클러스터

* 최신 웹 브라우저

## <a id="run"></a> 쿼리 콘솔을 사용하여 Hive 쿼리 실행

1. 이 <a href="https://manage.windowsazure.com" target="_blank">Azure 관리 포털</a> 을 열고 HDInsight 클러스터를 선택합니다. 페이지 맨 아래에서 **쿼리 콘솔**을 선택합니다. 메시지가 표시되면 클러스터를 만들 때 사용한 사용자 이름 및 암호를 입력합니다.

    > [AZURE.NOTE] 브라우저에서 **https://CLUSTERNAME.azurehdinsight.net**을 입력하여 쿼리 콘솔에 연결할 수 있습니다.

2. 페이지 위쪽에 있는 링크 중 **Hive 편집기**를 선택합니다. HDInsight 클러스터에서 실행하려는 HiveQL 문을 입력하는데 사용할 수 있는 양식이 표시 됩니다. 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	이 `Select * from hivesampletable` 텍스트는 다음 HiveQL 문으로 바꿉니다.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    이러한 문은 다음 작업을 수행합니다.

    * **DROP TABLE** - 테이블과 데이터 파일을 삭제합니다(테이블이 이미 있는 경우).
    * **CREATE EXTERNAL TABLE** - Hive에서 새 'external' 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.

    > [AZURE.NOTE] 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
    >
    > 외부 테이블을 삭제하면 데이터**뿐만 아니라** 테이블 정의도 삭제됩니다.

    * **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
    * **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치와 텍스트로 저장되었다는 것을 Hive에 알립니다.
    * **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 값이 포함된 3개 행이 있으므로 **3** 값이 반환되어야 합니다.

2. **제출**을 선택합니다. 페이지의 맨 아래에 **작업 세션**은 작업에 대한 세부 정보를 표시해야 합니다.

3. **상태** 핕드가 **완료**로 변경되면, 작업에 대한 **세부 정보 보기**를 선택합니다. 세부 정보 페이지에서 **작업 출력**은 `[오류]	3`을 포함합니다. 이 필드 아래의 **다운로드** 단추를 사용하여 작업의 출력을 포함하는 파일을 다운로드할 수 있습니다.


## <a id="summary"></a>요약

여기에서 볼 수 있듯이 Query 콘솔은 HDInsight 클러스터에서 Hive 쿼리 실행 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다. 

Hive 쿼리 콘솔을 사용하는 방법에 대한 자세한 내용을 보려면 쿼리 콘솔의 맨 위에서 **시작** 선택하고 샘플을 클릭합니다. 각 샘플은 샘플에 사용되는 HiveQL 문의 설명을 포함하여 Hive를 사용하여 데이터를 분석 프로세스를 안내합니다.

## <a id="nextsteps"></a>다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](../hdinsight-use-hive/)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Pig 사용](../hdinsight-use-pig/)

* [HDInsight에서 Hadoop과 MapReduce 사용](../hdinsight-use-mapreduce/)

[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

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
