<properties 
	pageTitle="HDInsight에서 Apache Spark 작업 서버 | Microsoft Azure" 
	description="Spark 작업 서버를 사용하여 원격으로 제출하고 Spark 클러스터에서 작업을 관리하는 방법을 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Azure HDInsight 클러스터에서 Spark 작업 서버(Windows)

> [AZURE.NOTE] 이제 HDInsight가 Linux에서 Spark 클러스터를 제공하며 Spark 클러스터에 원격으로 작업을 제출하기 위해 Livy를 사용합니다. Linux에서 HDInsight Spark 클러스터와 함께 Livy를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Spark 클러스터와 함께 Livy를 사용하여 원격으로 Spark 작업 제출(Linux)](hdinsight-apache-spark-livy-rest-interface.md)을 참조하세요.

Azure HDInight에서 Apache Spark 클러스터가 Spark 작업 서버를 클러스터 배포의 일부로 패키징 Spark 작업 서버는 REST API를 제공하여 Spark 컨텍스트를 만들고 Spark 응용 프로그램을 컨텍스트에 제출하며 작업 상태를 확인하고 컨텍스트를 중단합니다. 이 문서는 작업 서버를 사용하여 Spark 클러스터에서 일반적인 작업을 수행하기 위해 Curl을 사용하는 방법에 대한 일부 예제를 제공합니다.

>[AZURE.NOTE] Spark 작업 서버에 대한 전체 설명서는 [https://github.com/spark-jobserver/spark-jobserver](https://github.com/spark-jobserver/spark-jobserver)를 참조하세요.

## <a name="uploadjar"></a>Spark 클러스터에 jar 업로드

	curl.exe -k -u "<hdinsight user>:<user password>" --data-binary @<location of jar on the computer> https://<cluster name>.azurehdinsight.net/sparkjobserver/jars/<application name>

예:
	
	curl.exe -k -u "myuser:myPass@word1" --data-binary @C:\mylocation\eventhubs-examples\target\spark-streaming-eventhubs-example-0.1.0-jar-with-dependencies.jar https://mysparkcluster.azurehdinsight.net/sparkjobserver/jars/streamingjar


##<a name="createcontext"></a>작업 서버에서 새 영구 컨텍스트 만들기

	curl.exe -k -u "<hdinsight user>:<user password>" -d "" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context name>?num-cpu-cores=<value>&memory-per-node=<value>"

예:

	curl.exe -k -u "myuser:myPass@word1" -d "" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming?num-cpu-cores=4&memory-per-node=1024m"


##<a name="submitapp"></a>클러스터에 응용 프로그램 제출

	curl.exe -k -u "<hdinsight user>:<user password>" -d @<input file name> "https://<cluster name>.azurehdinsight.net/sparkjobserver/jobs?appName=<app name>&classPath=<class path>&context=<context>"

예:

	curl.exe -k -u "myuser:myPass@word1" -d @mypostdata.txt "https://mysparkcluster.azurehdinsight.net/sparkjobserver/jobs?appName=streamingjar&classPath=org.apache.spark.streaming.eventhubs.example.EventCountJobServer&context=mystreaming"

mypostdata.txt가 응용 프로그램을 정의합니다.


##<a name="submitapp"></a>작업 삭제

	curl.exe -X DELETE -k -u "<hdinsight user>:<user password>" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context>"

예:

	curl.exe -X DELETE -k -u "myuser:myPass@word1" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming"


##<a name="seealso"></a>참고 항목

* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview-v1.md)
* [HDInsight에서 Spark 클러스터 만들기](hdinsight-apache-spark-provision-clusters.md)
* [BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools-v1.md)
* [HDInsight에서 Spark를 사용하여 기계 학습 응용 프로그램 빌드](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->