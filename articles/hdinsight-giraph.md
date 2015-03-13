<properties 
	pageTitle="Azure HDInsight에서 Apache Giraph를 사용하는 방법" 
	description="Azure HDInsight에서 Apache Giraph를 사용하여 그래프 처리를 수행하는 방법에 대해 알아봅니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/14/2014" 
	ms.author="larryfr"/>

#Azure HDInsight(Hadoop)에서 Apache Giraph를 사용하는 방법 알아보기

[Apache Giraph][giraph]를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다. 

그래프는 인터넷과 같은 대규모 네트워크의 라우터 간 연결, 소셜 네트워크(또는 소셜 그래프)상의 사람들 간 관계 등 개체 간의 관계를 모델링합니다. 그래프 처리를 통해 그래프의 개체 간 관계를 추론하여 다음을 수행할 수 있습니다.

* 현재 관계를 기반으로 하여 잠재적인 친구 파악

* 네트워크의 두 컴퓨터 간에 가장 짧은 경로 파악

* 웹 페이지의 페이지 순위 계산

##다음 방법에 대해 알아봅니다.

* [Apache Giraph를 빌드하여 HDInsight 클러스터에 배포](#build)

* [SimpleShortestPathsComputation 예제 실행](#run)

	Giraph와 함께 제공되는 다른 예제 목록에 대해서는 [org.apache.giraph.examples 패키지](https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html)(영문)를 참조하세요.

* [발생할 수 있는 문제 해결](#tshoot)

##요구 사항

* Azure HDInsight 클러스터 버전 3.0 또는 3.1

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 이상

##<a id="build"></a>Giraph 빌드 및 배포

Giraph는 HDInsight 클러스터의 일부로 제공되지 않으므로 소스에서 빌드해야 합니다.  [Giraph 리포지토리](https://github.com/apache/giraph)에서 Giraph 빌드에 대한 자세한 내용을 볼 수 있습니다.

1. 현재(2014년 7월 14일) Giraph를 사용하려면 HDInsight에 사용되는 WASB 파일 저장소와 함께 작동할 패치가 필요합니다. 이 패치는 Apache Giraph 프로젝트에 제출되었지만 아직 허용되지 않았습니다. [GIRAPH-930](https://issues.apache.org/jira/browse/GIRAPH-930)의 __attachments__ 섹션에서 패치를 다운로드하여 로컬 드라이브에 __giraph-930.diff__로 저장합니다.

1. 명령줄에서 다음 Git 명령을 사용하여 Giraph 리포지토리의 복제를 만듭니다.

		git clone https://github.com/apache/giraph.git

2. 2단계의 복제 작업에서 만든 __giraph__ 디렉터리로 이동합니다.

		cd giraph

3. 다음 명령을 사용하여 로컬 리포지토리로 패치를 병합합니다.

		git apply giraph-930.diff

	__giraph-930.diff__를 1단계에서 만든 파일의 경로로 바꿉니다.

3. 다음 명령 중 하나를 사용하여 HDInsight 클러스터 버전용 Giraph를 빌드합니다.

	* __HDInsight 3.0__(Hadoop 2.2)의 경우

			mvn package -Phadoop_0.20.203 - DskipTests

	* __HDInsight 3.1__(Hadoop 2.4)의 경우

			mvn package -Phadoop_0.23 -DskipTests

	빌드를 완료하면 __\\giraph\\giraph-examples\\target__에서 예제 JAR 파일을 찾습니다.

4. [Azure PowerShell][aps] 및 [HDInsight-Tools][tools]를 사용하여 HDInsight 클러스터용 기본 저장소로 예제 JAR 파일을 업로드합니다.

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	__giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar__을 이전 단계에서 생성한 JAR 파일의 경로 및 이름으로 바꾸고 __clustername__을 HDInsight 클러스터의 이름으로 바꿉니다. 예를 들어 `-Phadoop_0.20.203` 매개 변수로 패키지를 빌드한 경우 JAR의 파일 이름은 __hadoop-0.20.203__을 포함합니다.

	명령이 완료되면 JAR 파일이 wasb:///example/jars/giraph.jar로 업로드됩니다.

	> [AZURE.NOTE] HDInsight에 파일을 업로드하는 데 사용할 수 있는 유틸리티 목록은 [HDInsight에서 Hadoop 작업용 데이터 업로드](http://azure.microsoft.com/documentation/articles/hdinsight-upload-data/)를 참조하세요.

##<a id="run"></a>예제 실행

SimpleShortestPathsComputation은 그래프의 개체 간 가장 짧은 경로를 찾기 위한 기본 [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) 구현을 보여 줍니다. 다음 단계에 따라 샘플 데이터를 업로드하고, SimpleShortestPathsComputation 예제를 사용하여 작업을 실행한 후 결과를 봅니다.

> [AZURE.NOTE] 이 소스와 기타 예제는 [GitHub 리포지토리](https://github.com/apache/giraph)(영문)의 [release-1.1 branch](https://github.com/apache/giraph/tree/release-1.1)에서 찾아볼 수 있습니다.

1. __tiny\_graph.txt__라는 새 파일을 만듭니다. 이 파일은 다음 줄을 포함해야 합니다.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	이 데이터는 `[source_id,source_value,[[dest_id], [edge_value] 등의]]` 형식을 사용하여 [방향이 지정된 그래프](http://en.wikipedia.org/wiki/Directed_graph)에서 개체 간 관계를 설명합니다. 각 줄은 __source\_id__와 하나 이상의 __dest\_id__ 개체 간 관계를 나타냅니다. __edge\_value__(또는 가중치)는 __source\_id__와 __dest\_id__ 간 연결의 강도 또는 거리로 생각할 수 있습니다. 

	개체 간 거리로 위의 값(또는 가중치)을 사용하여 그리면 다음과 같을 수 있습니다.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)


2. [Azure PowerShell][aps] 및 [HDInsight-Tools][tools]를 사용하여 HDInsight 클러스터용 기본 저장소로 __tiny\_graph.txt__ 파일을 업로드합니다.

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	클러스터 이름을 HDInsight 클러스터의 이름으로 바꿉니다.

3. 다음 PowerShell에서 __tiny\_graph.txt__ 파일을 입력으로 사용하여 __SimpleShortstPathsComputation__ 예제를 실행합니다. 그렇게 하려면 [Azure PowerShell][aps]을 설치하고 구성해야 합니다.

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
		                "-ca", "mapred.job.tracker=headnodehost:9010", `
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
		                "-vip", "wasb:///example/data/tinygraph.txt", `
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
		                "-op",  "wasb:///example/output/shortestpaths", `
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		  -JarFile $jarFile `
		  -ClassName "org.apache.giraph.GiraphRunner" `
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	위의 예제에서는 __clustername__을 HDInsight 클러스터의 이름으로 바꿉니다.

###결과 보기

작업이 완료되면 결과는 __wasb:///example/out/shotestpaths__ 폴더에 __part-m-#####__ 파일로 저장됩니다. [Azure PowerShell][aps] 및 [HDInsight-Tools][tools]를 사용하여 출력 파일을 다운로드합니다.

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

그러면 현재 디렉터리에 __example/output/shortestpaths__ 디렉터리 구조가 생성되고 __part__로 시작하는 파일이 다운로드됩니다. 그런 다음 __Cat__ cmdlet에서 다음과 같은 파일의 내용을 표시합니다.

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

SimpleShortestPathComputation 예제는 개체 ID 1로 시작하도록 하드 코딩되며 다른 개체에 대한 가장 짧은 경로를 찾습니다. 따라서 출력은  `destination_id distance`이며, 여기서 distance는 가장자리를 기준으로 개체 ID 1과 대상 ID 간의 거리 값(또는 가중치)입니다.

이를 시각화하면 ID 1과 다른 모든 개체 간의 가장 짧은 경로를 이동하여 결과를 확인할 수 있습니다. ID 1과 ID 4 간의 가장 짧은 경로는 5입니다. 이는 <span style="color:orange">ID 1과 ID 3 간</span>, 그리고 <span style="color:red">ID 3과 ID 4</span>간의 전체 거리입니다.

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>문제 해결

###출력 디렉터리가 이미 있음

Giraph 작업은 지정된 출력 디렉터리를 런타임으로 만듭니다. 디렉터리가 이미 있는 경우 출력 디렉터리가 이미 있음을 나타내는 오류가 발생합니다.

작업을 여러 번 실행하려는 경우 작업 간의 출력 디렉터리를 제거하거나 각 작업에 대해 다른 출력 디렉터리를 지정해야 합니다.

###<a id="cmd"></a>Hadoop 명령줄 사용

이 문서에서는 PowerShell을 통해 Giraph 작업을 실행하는 방법을 설명하지만 Hadoop 명령줄을 사용하여 작업을 실행할 수도 있습니다.

> [AZURE.NOTE] Hadoop 명령줄은 원격 데스크톱을 사용하여 HDInsight 클러스터에 연결할 경우에만 사용할 수 있습니다.
> 
> HDInsight 클러스터와 같은 Azure 계산 리소스에 대한 원격 데스크톱 세션은 Windows 기반 원격 데스크톱 클라이언트에서만 작동할 수 있습니다.

HDInsight 클러스터에 연결하려면 다음 단계를 수행하세요.

1. [Azure 관리 포털](https://manage.windowsazure.com)에서, HDInsight 클러스터를 선택한 다음 __구성__을 선택합니다.

2. 페이지 하단에서 __원격 사용__을 선택하고 원격 데스크톱 연결의 사용자 이름, 암호 및 만료 날짜를 입력합니다.

3. 원격 데스크톱을 사용하는 요청이 처리되면 __연결__에 대한 새로운 항목이 페이지 맨 아래에 나타납니다. 이 항목을 선택하여 원격 데스크톱 세션용 .RDP 파일을 다운로드합니다.

4. .RDP 파일은 저장하거나 즉시 열어서 원격 데스크톱 클라이언트를 실행할 수 있습니다. 연결 프로세스 중 원격 데스크톱 연결을 사용할 때 사용한 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다.

5. 연결된 후에는 데스크톱의 __Hadoop 명령줄__ 아이콘을 사용하여 Hadoop 명령줄을 시작합니다.

6. 다음 예제에서는 __giraph.jar__ 파일을 클러스터 헤드 노드로 복사한 후 Hadoop 명령줄을 사용하여 작업을 실행하는 방법을 보여 줍니다.

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###이전 버전의 HDInsight

이전 버전의 HDInsight에서 Giraph를 사용하려는 경우 해당 버전에서 지원하는 특정 Hadoop 버전용으로 컴파일해야 합니다. 사용 중인 HDInsight 버전에 해당하는 Hadoop 버전을 알아보려면 [HDInsight 클러스터 버전의 새로운 기능](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)을 참조하세요.

또한 이전 버전의 HDInsight를 사용하려면 Hadoop 명령줄에서 Giraph 작업을 실행해야 할 수 있습니다. PowerShell에서 작업을 실행할 때 오류가 발생하면 [Hadoop 명령줄]에서 작업을 실행해보세요.(#cmd)

##다음 단계

HDInsight에서 Giraph를 사용하는 방법에 대해 알아보았으므로 이제 HDInsight에서 [Pig][] 및 [Hive][]를 사용해보세요.

[giraph]: http://giraph.apache.org
[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/documentation/articles/hdinsight-use-hive/
<!--HONumber=42-->
