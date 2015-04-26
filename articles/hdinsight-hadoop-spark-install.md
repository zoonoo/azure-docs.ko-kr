<properties 
	pageTitle="HDInsight에서 스크립트 작업을 사용하여 Hadoop 클러스터에 Spark 설치| Azure" 
	description="HDInsight 클러스터를 사용자 지정하여 Spark를 설치하는 방법을 알아보세요. 스크립트를 사용하여 Spark를 설치하려면 스크립트 작업 구성 옵션을 사용하게 됩니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# HDInsight Hadoop 클러스터에서 Spark 1.0 설치 및 사용

**스크립트 작업** 클러스터 사용자 지정을 사용하여 HDInsight의 Hadoop에서 모든 유형의 클러스터에 Spark를 설치할 수 있습니다. 스크립트 작업을 사용하면 클러스터를 생성할 때에만 클러스터를 사용자 지정하는 스크립트를 실행할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-cluster-customize](영문)을 참조하세요.

이 항목에서는 스크립트 작업을 사용하여 Spark 1.0을 설치하는 방법에 대해 알아봅니다. Spark를 설치한 다음에는 HDInsight 클러스터에서 Spark 쿼리를 실행하는 방법도 알아봅니다.


## 이 문서의 내용

- [Spark란?](#whatis)
- [Spark를 설치하는 방법](#install)
- [HDInsight에서 Spark를 사용하는 방법](#usespark)
- [PowerShell을 사용하여 HDInsight Hadoop 클러스터에 Spark 설치](#usingPS)
- [.NET SDK를 사용하여 HDInsight Hadoop 클러스터에서 Spark 설치](#usingSDK) 


## <a name="whatis"></a>Spark란?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> (영문)는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. Spark는 메모리 내 계산 기능을 지원하여 기계 학습 및 그래프 계산의 반복 알고리즘에 적합합니다.

Spark를 사용하여 기존 디스크 기반 데이터 처리를 수행할 수도 있습니다. Spark는 중간 단계에서 디스크에 쓰기를 방지하여 기존 MapReduce 프레임워크에 비해 향상됩니다. 또한 Spark는 HDFS 및 WASB와 호환되므로 Spark를 사용하여 기존 데이터를 쉽게 처리할 수 있습니다. 

이 항목에서는 HDInsight 클러스터를 사용자 지정하여 Spark를 설치하는 방법에 대한 지침을 제공합니다.   

## <a name="install"></a>Spark를 설치하는 방법

HDInsight 클러스터에 Spark를 설치하는 샘플 스크립트는 읽기 전용 Azure 저장소 Blob([https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1))에서 다운로드할 수 있습니다. 이 섹션에서는 Azure 관리 포털을 사용하여 클러스터를 프로비전하면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다. 

> [AZURE.NOTE] 샘플 스크립트는 HDInsight 클러스터 버전 3.1에서만 작동합니다.  HDInsight 클러스터 버전에 대한 자세한 내용은 [HDInsight 클러스터 버전](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)을 참조하세요.

1. [사용자 지정 옵션을 사용하여 클러스터를 프로비저닝](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal)에 설명된 대로 **사용자 지정 만들기** 옵션을 사용하여 클러스터 프로비저닝을 시작합니다. 
2. 아래와 같이 마법사의 **스크립트 작업** 페이지에서 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다. 예: <b>Install Spark</b>.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다. 예를 들면 다음과 같습니다. <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1</i></td></tr>
		<tr><td>노드 유형</td>
			<td>사용자 지정 스크립트가 실행되는 노드를 지정합니다. <b>모든 노드</b>, <b>Head nodes only</b> 또는 <b>Worker nodes only</b>를 선택할 수 있습니다.
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다. Spark를 설치하는 스크립트에는 매개 변수가 필요하지 않으므로 비워 둘 수 있습니다.</td></tr>
	</table>	

	두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다. 스크립트를 추가한 후 확인 표시를 클릭하여 클러스터 프로비저닝을 시작합니다.

스크립트를 사용하여 PowerShell 또는 HDInsight .NET SDK를 사용하는 HDInsight에 Spark를 설치할 수도 있습니다. 이 절차에 대한 지침은 이 항목의 뒷부분에 제공됩니다.

## <a name="usespark"></a>HDInsight에서 Spark를 사용하는 방법
Spark는 Scala, Python 및 Java의 API를 제공합니다. 대화형 Spark 셸을 사용하여 Spark 쿼리를 실행할 수도 있습니다. 이 섹션에서는 다음 두 가지 접근 방식으로 Spark를 사용하는 방법을 소개합니다.

- [Spark  셸 사용](#sparkshell)
- [독립 실행형 Scala 프로그램 사용](#standalone)

###<a name="sparkshell"></a>Spark 셸 사용
대화형 Spark 셸에서 Spark 쿼리를 실행하려면 다음 단계를 수행합니다. 이 섹션에서는 기본적으로 HDInsight 클러스터에서 사용할 수 있는 샘플 데이터 파일(/example/data/gutenberg/davinci.txt)에 대한 Spark 쿼리를 실행합니다.

1. Azure 관리 포털에서 Spark를 설치하여 만든 클러스터에 대해 원격 데스크톱을 사용하도록 설정한 다음 클러스터에 원격으로 연결합니다. 자세한 내용은 <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP를 사용하여 HDInsight 클러스터에 연결</a>을 참조하세요.

2. RDP 세션의 데스크톱에서 Hadoop 명령줄을 열고 Spark가 설치된 위치(예: **C:\apps\dist\spark-1.0.2**)로 이동합니다.

3. 다음 명령을 실행하여 Spark 셸을 시작합니다.

		 .\bin\spark-shell --master yarn

	명령 실행이 완료되면 Scala 프롬프트가 표시됩니다.

		 scala>

5. Scala 프롬프트에 아래에 표시된 Spark 쿼리를 입력합니다. 이 쿼리는 클러스터와 연결된 WASB 저장소의 /example/data/gutenberg/ 위치에서 사용할 수 있는 davinci.txt 파일에 나오는 각 단어의 수를 계산합니다.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 출력은 다음과 유사합니다.

	![Output from running Scala interactive shell in an HDInsight cluster][img-hdi-scala-interactive]

7. :q 입력하여 Scala 프롬프트를 종료합니다.

		:q

### <a name="standalone"></a>독립 실행형 Scala 프로그램 사용

이 섹션에서는 기본적으로 HDInsight 클러스터에서 사용할 수 있는 샘플 데이터 파일(/example/data/gutenberg/davinci.txt)에 문자 'a' and 'b'를 포함하는 줄 수를 계산하는 Scala 응용 프로그램을 작성합니다. Spark 설치를 통해 사용자 지정된 클러스터에서 독립 실행형 Scala 프로그램을 작성하고 사용하려면 다음 단계를 수행해야 합니다.

- Scala 프로그램 작성
- 프로그램을 빌드하여 .jar 파일 가져오기
- 클러스터에서 작업 실행

#### Scala 프로그램 작성
이 섹션에서는 샘플 데이터 파일에서 'a' and 'b'가 포함된 줄 수를 계산하는 Scala 프로그램을 작성합니다. 

1. 텍스트 편집기를 열고 다음 코드를 붙여 넣습니다.

		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//location of sample data file on WASB
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. 파일을 **SimpleApp.scala**라는 이름으로 저장합니다.

#### Scala 프로그램 빌드
이 섹션에서는 <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (영문)(또는 sbt)을 사용하여 scala 프로그램을 빌드합니다. sbt를 사용하려면 Java 1.6 이상이 필요하므로 이 섹션을 계속 진행하기 전에 올바른 버전의 Java가 설치되어 있는지 확인합니다.

1. http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html(영문)에서 sbt를 설치합니다.

2. **SimpleScalaApp**라는 폴더를 만들고 이 폴더 내에 **simple.sbt**라는 파일을 만듭니다. 이 구성 파일에는 Scala 버전, 라이브러리 종속성 등에 대한 정보가 포함됩니다. simple.sbt 파일에 다음을 붙여 넣고 파일을 저장합니다.

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.0.2"



	>[AZURE.NOTE] 파일의 빈 줄을 그대로 유지해야 합니다.

	
3. **SimpleScalaApp** 폴더 아래에 디렉터리 구조 **\src\main\scala**를 만들고 앞에서 \src\main\scala 폴더에 만든 Scala 프로그램(**SimpleApp.scala**)을 붙여 넣습니다.
4. 명령 프롬프트를 열고 SimpleScalaApp 디렉터리로 이동한 후 다음 명령을 입력합니다.


		sbt package


	응용 프로그램이 컴파일되면 루트 SimpleScalaApp 폴더 내의 **\target\scala-2.10** 디렉터리 아래에 **simpleapp_2.10-1.0.jar**이 만들어집니다.


#### 클러스터에서 작업 실행
이 섹션에서는 Spark가 설치된 클러스터에 원격으로 연결한 다음 SimpleScalaApp 프로젝트의 대상 폴더를 복사합니다. 그런 다음 **spark-submit** 명령을 사용하여 클러스터에서 작업을 제출합니다.

1. Spark가 설치된 클러스터에 원격으로 연결합니다. SimpleApp.scala 프로그램을 작성 및 빌드한 컴퓨터에서 **SimpleScalaApp\target** 폴더를 복사하여 클러스터의 위치에 붙여 넣습니다.
2. RDP 세션의 데스크톱에서 Hadoop 명령줄을 열고 **target** 폴더를 붙여 넣은 위치로 이동합니다.
3. 다음 명령을 입력하여 SimpleApp.scala 프로그램을 실행합니다.

		C:\apps\dist\spark-1.0.2\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. 프로그램 실행이 완료되면 콘솔에 출력이 표시됩니다.


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>PowerShell을 사용하여 HDInsight Hadoop 클러스터에 Spark 설치

이 섹션에서는 클러스터를 사용자 지정하기 위해 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet을 사용하여 스크립트 작업을 사용하는 스크립트를 호출하고 클러스터를 사용자 지정합니다. 계속하기 전에 PowerShell을 설치 및 구성했는지 확인하세요. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

다음 단계를 수행합니다.

1. Azure PowerShell 창을 열고 다음 변수를 선언합니다.

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. 사용할 기본 저장소 및 클러스터의 노드와 같은 구성 값을 지정합니다.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** cmdlet을 사용하여 클러스터 구성에 스크립트 작업을 추가합니다. 나중에 클러스터가 생성되는 중에 스크립트 작업이 실행됩니다. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1

	**Add-AzureHDInsightScriptAction** cmdlet에서 다음과 같은 매개 변수를 사용합니다.

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">매개 변수</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">정의</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">스크립트 작업 정보가 추가되는 구성 개체입니다.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">이름</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트 작업의 이름입니다.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">사용자 지정 스크립트가 실행되는 노드를 지정합니다. 유효한 값은 HeadNode(헤드 노드에 설치) 또는 DataNode(데이터 노드에 설치)입니다. 두 값 중 하나 또는 모두 사용할 수 있습니다.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">실행되는 스크립트의 URI를 지정합니다.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">매개 변수</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">스크립트에 필요한 매개 변수입니다. 이 항목에서 사용되는 샘플 스크립트에는 매개 변수가 필요하지 않으므로 위의 코드 조각에는 이 매개 변수가 없습니다.
	</td></tr>
	</table>
	
4. 마지막으로 Spark가 설치된 사용자 지정된 클러스터 프로비전을 시작합니다.  
	
		# START PROVISIONING A CLUSTER WITH SPARK INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.


## <a name="usingSDK"></a>.NET SDK를 사용하여 HDInsight Hadoop 클러스터에서 Spark 설치

HDInsight .NET SDK는 .NET 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. 이 섹션에서는 SDK에서 스크립트 작업을 사용하여 Spark가 설치된 클러스터를 프로비전하는 방법에 대한 지침을 제공합니다. 다음 절차를 수행해야 합니다.

- HDInsight .NET SDK 설치
- 자체 서명된 인증서 만들기
- 콘솔 응용 프로그램 만들기
- 응용 프로그램 실행


**HDInsight .NET SDK를 설치하려면**

[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)(영문)에서 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

**자체 서명된 인증서를 만들려면**

자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요. 


**Visual Studio 응용 프로그램을 만들려면**

1. Visual Studio 2013을 엽니다.

2. 파일 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 클릭합니다.

3. 새 프로젝트에서 다음 값을 입력하거나 선택합니다.
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">속성</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">값</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">범주</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">템플릿</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">콘솔 응용 프로그램</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">이름</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
	</table>

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭하고

6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	.NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다.

7. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다.

8. 파일 맨 위에 다음 using 문을 추가합니다.

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Main() 함수에서 다음 코드를 복사하여 붙여 넣고 변수 값을 지정합니다.
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. 사용자 지정 스크립트를 호출하여 Spark를 설치하는 [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 클래스를 사용하는 다음 코드를 Main() 함수에 추가합니다.

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1"), // Location of the script to install Spark
		  null //because the script used does not require any parameters.
        ));

11. 마지막으로 클러스터를 만듭니다.

		client.CreateCluster(clusterInfo);

11. 응용 프로그램에 대한 변경 내용을 저장하고 솔루션을 빌드합니다. 

**응용 프로그램을 실행하려면**

PowerShell 콘솔을 열고 Visual Studio 프로젝트를 저장한 위치로 이동하고 프로젝트 내의 \bin\debug 디렉터리로 이동한 후 다음 명령을 실행합니다.

	.\CreateSparkCluster <cluster-name>

클러스터 이름을 제공하고 ENTER 키를 눌러 Spark가 설치된 클러스터를 프로비전합니다.


## 참고 항목##
- [HDInsight 클러스터에서 R 설치][hdinsight-install-r](영문) - 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 R을 설치하고 사용하는 방법에 대한 지침을 제공합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경으로, 수백 개의 통계 함수를 기본 제공하고 기능적인 측면과 개체 지향 프로그래밍이 결합된 프로그래밍 언어를 자체 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.
- [HDInsight 클러스터에 Giraph 설치](hdinsight-hadoop-giraph-install.md)(영문). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.
- [HDInsight 클러스터에 Solr 설치](hdinsight-hadoop-solr-install.md)(영문). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Solr을 설치합니다. Solr을 사용하면 저장된 데이터에서 강력한 검색 작업을 수행할 수 있습니다.





[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[powershell-install-configure]: ../install-configure-powershell/


<!--HONumber=42-->
