<properties
	pageTitle="Hive의 정의 및 HiveQL을 사용하는 방법을 알아봅니다 | Microsoft Azure"
	description="Apache Hive 정보 및 HDInsight에서 Hadoop와 Apache Hive를 사용하는 방법을 알아봅니다. Apache log4j 샘플 파일을 분석하기 위해 Hive 작업을 어떻게 실행할지 선택하고 HiveQL을 사용합니다." 
	keywords="hiveql,what is hive"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="04/23/2015"
	ms.author="larryfr"/>

# 샘플 Apache log4j 파일 분석을 위해 HDInsight에서 Hadoop와 함께 Hive 및 HiveQL 사용

[AZURE.INCLUDE [hive-선택기](../includes/hdinsight-selector-use-hive.md)]


이 자습서에서는 HDInsight에서 Hadoop의 Apache Hive를 사용하는 방법을 알아보고 Hive 작업을 실행하는 방법을 선택합니다. 또한 HiveQL 및 샘플 Apache log4j 파일을 분석 하는 방법에 대해 알아봅니다.

##<a id="why"></a>Hive의 정의 및 사용하는 이유 
[Apache Hive](http://hive.apache.org/)는 HiveQL를 사용하여 데이터 요약, 쿼리 및 분석을 수행할 수 있는 Hadoop용 데이터 웨어하우스 시스템입니다.(SQL과 유사한 쿼리 언어) 쌍방향으로 데이터를 검토하거나 다시 사용할 수 있는 일괄 처리 작업을 만드는 데 하이브를 사용할 수 있습니다.

Hive를 사용하면 크게 구조가 없는 데이터에 구조를 투영할 수 있습니다. 구조를 정의한 후에 Java 또는 MapReduce 지식 없이 해당 데이터를 쿼리할 때에 하이브를 사용할 수 있습니다. **HiveQL**(Hive 쿼리 언어)을 사용하여 T-SQL과 같은 문이 포함된 쿼리를 작성할 수 있습니다.

Hive를 사용하면 특정 문자로 구분되는 필드에 위치한 텍스트 파일과 같은 구조화 및 반구조화된 데이터로 작업하는 방법을 이해합니다. 또한 Hive는 복잡하거나 불규칙하게 구조화된 데이터에 대한 사용자 지정을 **serializer/deserializers(SerDe)** 지원합니다. 자세한 내용은 [HDInsight와  JSON SerDe의 사용자 지정 사용 방법](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx)을 참조하십시오.

Hive는 **사용자 정의 함수(UDF)**를 통해 확장 될 수도 있습니다. UDF를 사용하면 HiveQL에서 쉽게 모델링할 수 있는 기능 또는 논리를 구현할 수 있습니다. Hive와 UDF를 사용하는 예로, 다음을 참조하십시오:

* [HDInsight에서 Hive 및 Pig와 함께 Python 사용](hdinsight-python.md)

* [HDInsight에서 Hive 및 Pig와 함께 C# 사용](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight에 사용자 지정 하이브 UDF를 추가 하는 방법](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

##<a id="data"></a>Apache log4j 파일인 샘플 데이터 정보

해당 예제는 사용자의 blob 저장 컨테이너의 **/example/data/sample.log**에 저장된 *log4j* 샘플 파일을 사용합니다. 파일 내부의 각 로그는 유형과 심각도를 표시하는 `[LOG LEVEL]`  필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들어:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

이전 예에서 로그 수준은 ERROR입니다.

> [AZURE.NOTE][Apache Log4j](http://en.wikipedia.org/wiki/Log4j) 도구 로깅하여 log4j 파일을 생성하고 해당 파일을 사용자의 blob 컨테이너에 업로드할 수도 있습니다. 해당 지침은 [HDInsight에 데이터 업로드](hdinsight-upload-data.md)를 참조하세요. HDInsight에서 Azure Blob 저장소를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용](hdinsight-use-blob-storage.md)을 참조하세요.

샘플 데이터는 HDInsight가 기본 파일 시스템으로 사용하는 Azure Blob 저장소에 저장됩니다. HDInsight에서는 **wasb** 접두사를 사용하여 Blob 저장소에 저장된 파일에 액세스할 수 있습니다. 예를 들어 sample.log 파일에 액세스하려는 경우 다음 구문을 사용합니다.

	wasb:///example/data/sample.log

Azure Blob 저장소가 HDInsight의 기본 저장소이므로 HiveQL의 **/example/data/sample.log**를 사용하여 파일에 액세스할 수도 있습니다.

> [AZURE.NOTE]구문 **wasb:///**는 HDInsight 클러스터의 기본 저장소 컨테이너에 저장된 파일에 액세스하는 데 사용됩니다. 클러스터를 프로비전할 때 추가 저장소 계정을 지정한 경우 이러한 계정에 저장된 파일에 액세스하려면 컨테이너 이름과 저장소 계정 주소를 지정하여 데이터에 액세스하면 됩니다. 예를 들어:**wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>샘플 작업: 구분된 데이터에 열을 투영

다음 HiveQL 문은 **wasb:///example/data** 디렉터리에 저장된 구분된 데이터의 열에 투영됩니다.

	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

이전 예제에서 HiveQL 문은 다음 작업을 수행합니다:

* **DROP TABLE**: 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.
* **CREATE EXTERNAL TABLE** - Hive에서 새 **외부** 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 원래 형태로 남아 있습니다.
* **ROW FORMAT**: 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
* **STORED AS TEXTFILE LOCATION**: 데이터가 저장된 위치(example/data 디렉터리)에 텍스트로 저장되었음을 Hive에 알립니다. 데이터는 디렉터리 내에서 하나의 파일 또는 여러 파일에 걸쳐 분산될 수 있습니다.
* **SELECT**: **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 경우 이 값을 포함하는 행이 3개 있으므로 **3** 값이 반환되어야 합니다.

> [AZURE.NOTE]자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하며 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
>
> 외부 테이블을 삭제하면 데이터가 삭제되지 **않고** 테이블 정의만 삭제됩니다.

외부 테이블을 만든 후에 다음 문은 **내부** 테이블을 만드는데 사용됩니다.

	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

이러한 문은 다음 작업을 수행합니다.

* **CREATE TABLE IF NOT EXISTS**: 테이블이 아직 없는 경우 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 내부 테이블입니다.
* **STORED AS ORC**: 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
* **덮어쓰기 삽입... SELECT**:  **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를  **errorLogs** 테이블에 삽입합니다.

> [AZURE.NOTE]외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

##<a id="usetez"></a>성능 개선을 위해 Tez 사용

[Apache Tez](http://tez.apache.org)는 Hive와 같이 데이터를 많이 사용하는 응용 프로그램을 큰 규모에서도 훨씬 더 효율적으로 실행할 수 있는 프레임워크입니다. HDInsight의 최신 릴리스에서는 Hive를 Tez에서 실행할 수 있습니다. 현재는 이러한 실행 기능이 기본적으로 해제되어 있으며 사용하도록 설정해야 합니다. Tez를 사용하려면 Hive 쿼리에 대해 다음 값을 설정해야 합니다:

	set hive.execution.engine=tez;

이 값을 쿼리 시작 부분에 배치하여 쿼리별로 제출할 수 있습니다. 클러스터를 만들 때 구성 값을 설정하여 클러스터에 대해 이 기능을 기본적으로 설정할 수도 있습니다. 자세한 내용은 [HDInsight 클러스터 프로비전](hdinsight-provision-clusters.md)에서 확인할 수 있습니다.

[Tez의 Hive 디자인 문서](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)에는 선택 가능한 구현 및 튜닝 구성과 관련한 여러 세부 정보가 포함되어 있습니다.


##<a id="run"></a>HiveQL 작업 실행 방법 선택

HDInsight는 다양한 메서드를 사용하여 HiveQL 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르십시오.

| 원하는 경우 **이것을 사용**하십시오... | ...**대화형** 셸 | ...**일괄** 처리 | ...**클러스터 운영 체제**로 | ...**클라이언트 운영 체제**에서 |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-hive-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X, 또는 Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) | &nbsp; | ✔ | Linux 또는or Windows | Linux, Unix, Mac OS X, 또는 Windows |
| [쿼리 콘솔e](hdinsight-hadoop-use-hive-query-console.md) | &nbsp; | ✔ | Windows | 브라우저 기반 |
| [Visual Studio용 HDInsight 도구](hdinsight-hadoop-use-hive-visual-studio.md) | &nbsp; | ✔ | Linux 또는or Windows | Windows |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux 또는or Windows | Windows(당분간) |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) | &nbsp; | ✔ | Linux 또는or Windows | Windows |
| [원격 데스크톱](hdinsight-hadoop-use-hive-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

##<a id="nextsteps"></a>다음 단계

이제 Hive의 정의 및 HDInsight에서 Hadoop와 Hive를 사용하는 방법을 살펴보았으므로 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]
* [HDInsight에서 MapReduce 작업 사용][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[1]: hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=54-->