<properties 
	pageTitle="스크립트 작업을 사용하여 Linux 기반 HDInsight에 Apache Spark 설치(Hadoop) | Microsoft Azure" 
	description="스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터에 Spark를 설치하는 방법을 알아봅니다. 스크립트 작업을 사용하면 클러스터 구성을 변경하거나 서비스 및 유틸리티를 설치하여 생성 중 클러스터를 사용자 지정할 수 있습니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="larryfr"/>

# HDInsight Hadoop 클러스터에서 Spark 설치 및 사용

이 문서에서는 스크립트 작업을 사용하여 Spark를 설치하는 방법에 대해 알아봅니다. 스크립트 작업을 사용하면 클러스터를 생성할 때에만 클러스터를 사용자 지정하는 스크립트를 실행할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize](영문)을 참조하세요. Spark를 설치한 다음에는 HDInsight 클러스터에서 Spark 쿼리를 실행하는 방법도 알아봅니다.

> [AZURE.NOTE]HDInsight는 클러스터 유형으로 Spark를 제공하여 Hadoop 클러스터를 수정하지 않고 Spark를 직접 프로비전할 수 있습니다. 그러나 현재는 Windows 기반 클러스터로 제한됩니다. Spark 클러스터 유형을 사용하여 Spark 버전 1.3.1과 함께 Windows 기반 HDInsight 버전 3.2 클러스터를 얻습니다. 자세한 내용은 [HDInsight에서 Apache Spark 시작](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)을 참조하세요.

## <a name="whatis"></a>Spark란?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. Spark는 메모리 내 계산 기능을 지원하여 기계 학습 및 그래프 계산의 반복 알고리즘에 적합합니다.

Spark를 사용하여 기존 디스크 기반 데이터 처리를 수행할 수도 있습니다. Spark는 중간 단계에서 디스크에 쓰기를 방지하여 기존 MapReduce 프레임워크를 향상시킵니다. 또한 Spark는 HDFS(Hadoop Distributed File System) 및 Azure Blob 저장소와 호환되므로 Spark를 통해 기존 데이터를 쉽게 처리할 수 있습니다.

이 항목에서는 HDInsight 클러스터를 사용자 지정하여 Spark를 설치하는 방법에 대한 지침을 제공합니다.

## <a name="whatis"></a>설치할 수 있는 Spark 버전

이 항목에서는 스크립트 작업 사용자 지정 스크립트를 사용하여 HDInsight 클러스터에 Spark를 설치합니다. 이 스크립트는 Spark 1.5.1을 설치합니다.

이 스크립트를 수정하거나 사용자 고유의 스크립트를 만들어 다른 버전의 Spark를 설치할 수 있습니다.

## 스크립트가 수행하는 작업

이 스크립트는 Spark 1.5.1을 `/usr/hdp/current/spark`에 설치합니다.

> [AZURE.WARNING]HDInsight 클러스터에서 기본적으로 몇 가지 Spark 1.3.1 바이너리가 설치되는 경우도 있습니다. 이러한 작업은 사용하지 않으며, 향후 업데이트에서는 HDInsight 클러스터 이미지에서 제거될 것입니다.

## <a name="install"></a>스크립트 동작을 사용하여 Spark 설치

HDInsight 클러스터에 Spark를 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh))에서 다운로드할 수 있습니다. 이 섹션에서는 Azure 포털을 사용하여 클러스터를 만들면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [AZURE.NOTE]또한 이 스크립트를 사용하여 클러스터를 만드는 데 Azure PowerShell 또는 HDInsight.NET SDK를 사용할 수도 있습니다. 이 방법을 사용하는 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

1. [Linux 기반 HDInsight 클러스터 만들기](hdinsight-provision-linux-clusters.md#portal)의 단계를 사용하여 클러스터를 만들기 시작하지만 완료하지 마세요.

2. **선택적 구성** 블레이드에서 **스크립트 동작**을 선택하고 아래 정보를 제공합니다.

	* __NAME__: 스크립트 작업의 이름을 입력합니다.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh
	* __HEAD__: 이 옵션 선택
	* __WORKER__: 이 옵션 선택 안 함
	* __ZOOKEEPER__: 이 옵션 선택 안 함
	* __PARAMETERS__: 이 필드는 공백으로 둡니다.
    
    > [AZURE.NOTE]이 Spark 스크립트 예에서는 헤드 노드에서만 구성 요소를 설치하므로 다른 노드 유형은 선택 취소할 수 있습니다.

3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다. 마지막으로 **선택적 구성** 블레이드의 아래 쪽에서 **선택** 단추를 사용하여 선택적 구성 정보를 저장합니다.

4. [Linux 기반 HDInsight 클러스터 만들기](hdinsight-provision-linux-clusters.md#portal)에서 설명한 대로 클러스터를 계속 프로비전합니다.

## <a name="usespark"></a>HDInsight에서 Spark를 사용하는 방법

Spark는 Scala, Python 및 Java의 API를 제공합니다. 대화형 Spark 셸을 사용하여 Spark 쿼리를 실행할 수도 있습니다. 클러스터 만들기가 완료되면 다음을 사용하여 HDInsight 클러스터에 연결합니다.

	ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
	
HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

연결되면 다음 섹션에서 Spark 사용에 대한 특정 단계를 따르세요.

- [Spark 셸을 사용하여 대화형 쿼리 실행](#sparkshell)
- [Spark 셸을 사용하여 Spark SQL 쿼리 실행](#sparksql) 
- [독립 실행형 Scala 프로그램 사용](#standalone)

###<a name="sparkshell"></a>Spark 셸을 사용하여 대화형 쿼리 실행

1. 다음 명령을 실행하여 Spark 셸을 시작합니다.

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	명령 실행이 완료되면 Scala 프롬프트가 표시됩니다.

		 scala>

5. Scala 프롬프트에 아래에 표시된 Spark 쿼리를 입력합니다. 이 쿼리는 클러스터와 연결된 Azure Blob 저장소의 /example/data/gutenberg/ 위치에서 사용할 수 있는 davinci.txt 파일에 나오는 각 단어의 수를 계산합니다.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 출력은 다음과 유사합니다.

		(felt,,1)
		(axle-tree,1)
		(deals,9)
		(virtuous,4)

7. :q 입력하여 Scala 프롬프트를 종료합니다.

		:q

###<a name="sparksql"></a>Spark 셸을 사용하여 Spark SQL 쿼리 실행

Spark SQL을 사용하면 Spark를 사용하여 SQL(구조적 쿼리 언어), HiveQL 또는 Scala로 표현된 관계형 쿼리를 실행할 수 있습니다. 이 섹션에서는 Spark를 사용하여 샘플 Hive 테이블에서 Hive 쿼리를 실행하는 방법을 알아봅니다. 이 섹션에서 사용하는 Hive 테이블(**hivesampletable**이라고 함)은 클러스터를 만들 때 기본적으로 사용할 수 있습니다.

1. 다음 명령을 실행하여 Spark 셸을 시작합니다.

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	명령 실행이 완료되면 Scala 프롬프트가 표시됩니다.

		 scala>

2. Scala 프롬프트에서 Hive 컨텍스트를 설정합니다. 이는 Spark를 사용하여 Hive 쿼리 작업을 수행하는 데 필요합니다.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	> [AZURE.NOTE]이 문의 `sc`는 Spark 셸을 시작할 때 설정되는 기본 Spark 컨텍스트입니다.

5. Hive 컨텍스트를 사용하여 Hive 쿼리를 실행하고 출력을 콘솔에 인쇄합니다. 이 쿼리는 특정 제조업체의 장치에 대한 데이터를 검색하며, 검색되는 레코드 수를 20으로 제한합니다.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. 다음과 유사한 출력이 표시됩니다.

		[820,11:35:17,ko-KR,Android,HTC,Inspire 4G,Louisiana,UnitedStates, 2.7383836,0,1]
		[1055,17:24:08,ko-KR,Android,HTC,Incredible,Ohio,United States,18.0894738,0,0]
		[1067,03:42:29,ko-KR,Windows Phone,HTC,HD7,District Of Columbia,United States,null,0,0]

7. :q 입력하여 Scala 프롬프트를 종료합니다.

		:q

### <a name="standalone"></a>독립 실행형 Scala 프로그램 사용

이 섹션에서는 샘플 데이터 파일(/example/data/gutenberg/davinci.txt)에서 'a' 및 'b' 문자가 포함된 줄 수를 계산하는 Scala 응용 프로그램을 만듭니다.

1. 다음 명령을 사용하여 Scala Build Tool을 설치합니다.

		echo "deb http://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
		sudo apt-get update
		sudo apt-get install sbt
		
	계속하지 묻는 메시지가 표시되면 __Y__를 선택하여 계속합니다.

2. Scala 프로젝트에 대한 디렉터리 구조를 만듭니다.

		mkdir -p SimpleScalaApp/src/main/scala
		
3. 이 프로젝트에 대한 구성 정보를 포함하는 __simple.sbt__라는 새 파일을 만듭니다.

		cd SimpleScalaApp
		nano simple.sbt
		
	파일 내용으로 다음을 사용합니다.

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"


	> [AZURE.NOTE]각 항목 사이에 빈 줄이 있어야 합니다.
	
	__Ctrl+X__를 사용한 다음 __Y__와 __Enter__ 키를 사용하여 파일을 저장합니다.

4. 다음 명령을 사용하여 __SimpleScalaApp/src/main/scala__ 디렉터리에 __SimpleApp.scala__라는 새 파일을 만듭니다.

		nano src/main/scala/SimpleApp.scala

	파일 내용으로 다음을 사용합니다.

		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

	__Ctrl+X__를 사용한 다음 __Y__와 __Enter__ 키를 사용하여 파일을 저장합니다.

5. __SimpleScalaApp__ 디렉터리에서 다음 명령을 사용하여 응용 프로그램을 빌드하고 jar 파일에 저장합니다.

		sbt package

	응용 프로그램이 컴파일되면 \_\_SimpleScalaApp/target/scala-2.10** 디렉터리 아래에 **simpleapp\_2.10-1.0.jar** 파일이 만들어집니다.

6. 다음 명령을 사용하여 SimpleApp.scala 프로그램을 실행합니다.


		/usr/hdp/current/spark/bin/spark-submit --class "SimpleApp" --master yarn target/scala-2.10/simpleapp_2.10-1.0.jar

4. 프로그램 실행이 완료되면 콘솔에 출력이 표시됩니다.

		Lines with a: 21374, Lines with b: 11430

## 다음 단계

- [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)입니다. Hue는 기본 저장소에서 HDInsight 클러스터를 쉽게 찾을 뿐만 아니라 Pig 및 Hive 작업을 편리하게 만들고 실행하고 저장할 수 있도록 하는 웹 UI입니다.

- [HDInsight 클러스터에 R 설치][hdinsight-install-r] - 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 R을 설치하고 사용하는 방법에 대한 지침을 제공합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. 수백 개의 기본 제공 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 고유한 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.

- [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.

- [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.

- [HDInsight 클러스터에서 Hue를 설치](hdinsight-hadoop-hue-linux.md)합니다. 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 Hue를 설치합니다. Hue는 Hadoop 클러스터와 상호 작용하는 데 사용되는 웹 응용 프로그램 집합입니다.



[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=Oct15_HO4-->