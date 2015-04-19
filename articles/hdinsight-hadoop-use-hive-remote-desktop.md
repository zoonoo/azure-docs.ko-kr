<properties
   pageTitle="HDInsight에서 Hadoop Hive 사용 | Azure"
   description="원격 데스크톱을 사용하여 HDInsight에서 Hive를 사용하는 방법에 대해 알아봅니다."
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

# 원격 데스크톱을 사용하여 HDInsight에서 Hadoop과 Hive 사용

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

이 문서에서는 원격 데스크톱을 사용하여 HDInsight 클러스터에 연결한 다음 Hive 명령줄 인터페이스(CLI)를 사용하여 Hive 쿼리를 실행하는 방법을 배웁니다.

> [AZURE.NOTE] 이 문서에서는 예제에 사용된 HiveQL 문이 수행하는 작업에 대한 자세한 설명을 제공하지 않습니다. 이 예제에 사용된 HiveQL에 대한 자세한 내용은 <a href="../hdinsight-use-hive/" target="_blank">HDInsight에서 Hadoop과 Hive 사용</a>을 참조하세요.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Windows 기반 HDInsight(HDInsight의 Hadoop) 클러스터

* Windows 7 또는 최신 클라이언트 운영 체제

##<a id="connect"></a>원격 데스크톱을 사용하여 연결

HDInsight 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP를 사용하여 HDInsight 클러스터에 연결</a>의 지침에 따라 연결합니다.

##<a id="hive"></a>Hive 명령 사용

HDInsight 클러스터용 데스크톱에 연결되면, Hive에서 작업하기 위해 다음 단계를 사용합니다.

1. HDInsight 데스크톱에서 **Hadoop 명령줄**을 시작합니다.

2. Hive CLI를 시작하려면 다음 명령을 입력합니다.

        %hive_home%\bin\hive

    CLI가 시작되면 Hive CLI 프롬프트- `hive>`가 표시됩니다.

3. CLI에서 다음 문을 입력하여 샘플 데이터를 사용하는 **log4jLogs**라는 새 테이블을 만듭니다.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    이러한 문은 다음 작업을 수행합니다.

    * **DROP TABLE** - 테이블과 데이터 파일을 삭제합니다(테이블이 이미 있는 경우).
    
    * **CREATE EXTERNAL TABLE** - Hive에서 새 '외부' 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.

		> [AZURE.NOTE] 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
    	>
    	> 외부 테이블을 삭제하면 데이터**뿐만 아니라** 테이블 정의도 삭제됩니다.
    
	* **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
	
    * **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치와 텍스트로 저장되었다는 것을 Hive에 알립니다.
    
    * **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 값이 포함된 3개 행이 있으므로 **3** 값이 반환되어야 합니다.


4. 다음 문을 사용하여 **errorLogs**라는 새 '내부' 테이블을 만듭니다.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    이러한 문은 다음 작업을 수행합니다.

    * **CREATE TABLE IF NOT EXISTS** - 테이블을 만듭니다(아직 없는 경우). **EXTERNAL** 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 '내부' 테이블입니다.
    
		> [AZURE.NOTE] **EXTERNAL** 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.
		
    * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
    
    * **INSERT OVERWRITE ... SELECT** - **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.

    t4 열에 **[ERROR]**가 포함된 행만 **errorLogs** 테이블에 저장되었는지 확인하려면 다음 문을 사용하여 **errorLogs**의 모든 행을 반환합니다.

        SELECT * from errorLogs;

    데이터 중 t4 열에 모두 **[ERROR]**가 포함된 3개 행이 반환되어야 합니다.

##<a id="summary"></a>요약

여기에서 볼 수 있듯이 Hive 명령은 HDInsight 클러스터에서 Hive 쿼리 실행 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다.

##<a id="nextsteps"></a>다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


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

<!--HONumber=47-->
