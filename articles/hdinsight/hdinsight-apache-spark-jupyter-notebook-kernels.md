<properties 
	pageTitle="Linux의 HDInsight Spark 클러스터에서 Jupyter 노트북과 함께 사용할 수 있는 커널 | Microsoft Azure" 
	description="Hdinsight Linux의 Spark 클러스터와 함께 사용할 수 있는 추가 Jupyter 노트북 커널에 대해 알아봅니다." 
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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# HDInsight(Linux)의 Spark 클러스터로 Jupyter 노트북에 사용할 수 있는 커널

HDInsight(Linux)의 Apache Spark 클러스터는 응용 프로그램을 테스트하는 데 사용할 수 있는 Jupyter 노트북을 포함합니다. 기본적으로 Jupyter 노트북은 **Python2** 커널과 함께 제공됩니다. HDInsight Spark 클러스터는 Jupyter 노트북에 사용할 수 있는 두 개의 추가 커널을 제공합니다. 다음과 같습니다.

1. **Spark**(Scala에서 작성한 응용 프로그램용)
2. **PySpark**(Python에서 작성한 응용 프로그램용)

이 문서에서는 이러한 커널을 사용하는 방법 및 이를 사용하여 얻을 수 있는 이점에 대해 배우게 됩니다.

**필수 조건:**

다음이 있어야 합니다.

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight Linux의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](hdinsight-apache-spark-jupyter-spark-sql.md)를 참조하세요.

## 커널은 어떻게 사용합니까? 

1. [Azure Preview 포털](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터 타일을 클릭합니다(Spark 클러스터를 시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.   

2. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

	> [AZURE.NOTE] 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 접근할 수 있습니다. __CLUSTERNAME__을 클러스터의 이름으로 바꿉니다.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 새 커널로 새 노트북을 만듭니다. **새로 만들기**를 클릭한 다음 **Pyspark** 또는 **Spark**를 클릭합니다. Scala 응용 프로그램에 대해 Spark 커널을 Python 응용 프로그램에 대해 PySpark 커널을 사용해야 합니다.

	![새 Jupyter 노트북 만들기](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "새 Jupyter 노트북 만들기")

3. 선택한 커널로 새 노트북을 열어야 합니다.

## 새 커널을 사용해야 하는 이유는 무엇입니까?

새 커널 사용에는 몇 가지 이점이 있습니다.

1. 기본 **Python2** 커널의 경우 개발하는 응용 프로그램으로 작업을 시작하기 전에 Spark, SQL 또는 Hive 컨텍스트를 설정해야 합니다. 새 커널(**Spark** 또는 **PySpark**)을 사용하는 경우 이러한 컨텍스트는 기본적으로 사용할 수 있습니다. 이러한 컨텍스트는 다음과 같습니다.

	* **sc** - Spark 컨텍스트용
	* **sqlContext** - SQL 컨텍스트용
	* **hiveContext** - Hive 컨텍스트용


	따라서 컨텍스트를 설정하기 위해 다음과 같은 문을 실행할 필요가 없습니다.

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	대신 응용 프로그램에서 직접 미리 설정된 컨텍스트를 사용할 수 있습니다.
	
2. **%sql** 및 **%hive** 마법을 직접 사용하여 SQL 또는 Hive 쿼리를 각각 사용할 수 있습니다. 따라서 이와 같은 경우는 선행 코드 문 없이 기본적으로 직접 작동합니다.

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## 새 커널을 사용하는 동안 고려 사항

사용하는 커널에 따라(Python2, PySpark 또는 Spark) 노트북을 실행 중인 상태로 두면 클러스터 리소스가 사용됩니다. Python2 노트북을 사용하는 경우 컨텍스트를 명시적으로 만들므로 응용 프로그램을 종료할 때 해당 컨텍스트를 종료할 수도 있습니다.

그러나 PySpark 및 Spark 커널을 사용하는 경우 컨텍스트는 미리 설정되기 때문에 컨텍스트 또한 명시적으로 중지할 수는 없습니다. 따라서 방금 노트북을 종료한 경우 컨텍스트는 여전히 실행되고 클러스터 리소스를 사용할 수 있습니다. PySpark 및 Spark 커널을 사용하는 경우 노트북의 **파일** 메뉴에서 **Close and Halt** 옵션을 사용하는 것이 좋습니다. 이는 컨텍스트를 종료한 다음 노트북을 종료합니다.


## 몇 가지 예제 보기

Jupyter 노트북을 여는 경우 루트 수준에서 사용할 수 있는 두 개의 폴더가 표시됩니다.

* **Python** 폴더에는 기본 **Python2** 커널을 사용하는 샘플 노트북이 있습니다.
* **Scala** 폴더에는 새 **Spark** 커널을 사용하는 샘플 노트북이 있습니다.

두 개의 폴더에서 동일한(예: **READ ME FIRST - HDInsight에서 Spark의 기본 사항 알아보기**) 노트북을 열어 Python2 노트북이 필요한 컨텍스트 설정으로 항상 시작하는 방법을 볼 수 있습니다. 반면 Spark 노트북은 미리 설정된 컨텍스트를 사용합니다.

## 사용자 의견

새로운 커널은 상당히 초기 단계에 있으며 시간이 지남에 따라 개선됩니다. 이는 API가 이러한 커널의 성숙에 따라 변경될 수 있음을 의미할 수 있습니다. 이러한 새로운 커널을 사용하는 동안 가진 의견을 보내주시면 감사하겠습니다. 이러한 커널의 최종 릴리스 형성에 매우 유용할 것입니다. 이 문서의 맨 아래 **의견** 섹션 아래에 의견/피드백을 남길 수 있습니다.


## <a name="seealso"></a>참고 항목


* [개요: Azure HDInsight에서 Apache Spark](hdinsight-apache-spark-overview.md)

### 시나리오

* [BI와 Spark: BI 도구와 함께 HDInsight에서 Spark를 사용하여 대화형 데이터 분석 수행](hdinsight-apache-spark-use-bi-tools.md)

* [기계 학습과 Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [기계 학습과 Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 스트리밍: HDInsight에서 Spark를 사용하여 실시간 스트리밍 응용 프로그램 빌드](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight의 Spark를 사용하여 웹 사이트 로그 분석](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 응용 프로그램 만들기 및 실행

* [Scala를 사용하여 독립 실행형 응용 프로그램 만들기](hdinsight-apache-spark-create-standalone-application.md)

* [Livy를 사용하여 Spark 클러스터에서 원격으로 작업 실행](hdinsight-apache-spark-livy-rest-interface.md)

### 도구 및 확장

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 응용 프로그램 만들기 및 제출](hdinsight-apache-spark-intellij-tool-plugin.md)

* [HDInsight에서 Spark 클러스터와 함께 Zeppelin Notebook 사용](hdinsight-apache-spark-use-zeppelin-notebook.md)

### 리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->