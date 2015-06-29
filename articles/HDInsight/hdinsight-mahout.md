<properties
	pageTitle="Mahout 및 Hadoop을 사용하여 추천 생성 | Microsoft Azure"
	description="Apache Mahout 기계 학습 라이브러리를 사용하여 HDInsight(Hadoop)에서 영화 추천을 생성하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="larryfr"/>

#HDInsight의 Hadoop과 함께 Apache Mahout을 사용하여 영화 추천 생성

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Azure HDInsight에서 [Apache Mahout](http://mahout.apache.org) 기계 학습 라이브러리를 사용하여 영화 추천을 생성하는 방법에 대해 알아봅니다.

> [AZURE.NOTE]이 문서의 단계는 Windows 클라이언트와 Windows 기반 HDInsight 클러스터에 필요합니다. Linux, OS X 또는 Unix 클라이언트, Linux 기반 HDInsight 클러스터에서 Mahout 사용에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop으로 Apache Mahout을 사용하여 영화 권장 구성 생성](hdinsight-hadoop-mahout-linux-mac.md)을 참조하세요.


##<a name="learn"></a>학습 내용

Mahout은 Apache Hadoop용 [기계 학습][ml] 라이브러리입니다. Mahout에는 필터링, 분류 및 클러스터링과 같은 데이터 처리를 위한 알고리즘이 포함됩니다. 이 문서에서는 추천 엔진을 사용하여 친구가 본 영화를 기준으로 영화 추천을 생성합니다. 또한 의사 결정 포리스트로 분류를 수행하는 방법에 대해서도 알아봅니다. 여기서는 다음 내용을 다룹니다.

* Windows PowerShell을 사용하여 Mahout 작업을 실행하는 방법

* Hadoop 명령줄에서 Mahout 작업을 실행하는 방법

* HDInsight 3.0 및 2.0 클러스터에 Mahout을 설치하는 방법

	> [AZURE.NOTE]Mahout는 HDInsight 3.1 버전의 클러스터에서 제공됩니다. 이전 버전의 HDInsight를 사용하는 경우 계속하기 전에 [Mahout 설치](#install)를 참조하세요.

##필수 조건:

* **HDInsight에서 Windows 기반 Hadoop 클러스터** HDInsight 클러스터 만들기에 대해서는 [HDInsight에서 Hadoop 사용 시작][getstarted]을 참조하세요.

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.


##<a name="recommendations"></a>WindowsPowerShell을 사용하여 추천 생성

> [AZURE.NOTE]이 섹션에 사용된 작업은 Windows PowerShell에서 작동하지만 Mahout과 함께 제공되는 클래스 중 다수가 Windows PowerShell에서 현재 작동하지 않으며 Hadoop 명령줄을 사용하여 실행해야 합니다. Windows PowerShell에서 작동하지 않는 클래스의 목록은 [문제 해결](#troubleshooting) 섹션을 참조하세요.
>
> Hadoop 명령줄을 사용하여 Mahout 작업을 실행하는 예제에 대해서는 [Hadoop 명령줄을 사용하여 데이터 분류](#classify)를 참조하세요.

Mahout에서 제공하는 기능 중 하나가 추천 엔진입니다. 이 엔진은 `userID`, `itemId` 및 `prefValue`(항목에 대한 사용자 선호도) 형식의 데이터를 허용합니다. 그런 다음 Mahout에서 동시 발생 분석을 수행하여 _특정 항목에 대한 선호도를 가진 사용자가 다른 항목에 대한 선호도도 갖고 있는지_ 확인할 수 있습니다. Mahout은 좋아하는 항목 선호도를 가진 사용자를 확인하며, 이 선호도는 추천하는 데 사용할 수 있습니다.

다음은 영화를 사용하는 매우 단순한 예제입니다.

* __동시 발생__: Joe, Alice 및 Bob은 모두 _스타워즈_, _제국의 역습_ 및 _제다이의 귀환_을 좋아합니다. Mahout은 이러한 영화 중 하나를 좋아하면서 다른 두 개도 좋아하는 사용자를 확인합니다.

* __동시 발생__: Bob 및 Alice는 _보이지 않는 위협_, _클론의 습격_ 및 _시스의 복수_도 좋아합니다. Mahout은 이전의 3개 영화를 좋아하는 사용자가 이 3개 영화도 좋아하는지 확인합니다.

* __유사성 추천__: Joe가 첫 3개 영화를 좋아하므로, Mahout은 유사한 선호도를 가진 다른 사람이 좋아하지만 Joe는 본(좋아하거나 평가한) 적이 없는 영화를 검색합니다. 이 경우 Mahout은 _보이지 않는 위협_, _클론의 습격_ 및 _시스의 복수_를 추천합니다.

###데이터 로드

편의를 위해 [GroupLens Research][movielens]에서 Mahout과 호환되는 형식으로 영화에 대한 평가 데이터를 제공합니다.

1. 1700개 영화에 대한 1,000명 사용자의 100,000개 평가를 포함하는 [MovieLens 100k][100k] 압축 파일을 다운로드합니다.

2. 압축 파일을 풉니다. 여기에는 __u.__ 접두사가 지정된 많은 데이터 파일을 포함하는 __ml-100k__ 디렉터리가 있습니다. Mahout에서 분석하는 파일은 __u.data__입니다. 이 파일의 데이터 구조는 `userID`, `movieID`, `userRating` 및 `timestamp`입니다. 다음은 데이터의 예제입니다.


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. HDInsight 클러스터에서 __u.data__ file to __example/data/u.data__ 파일을 업로드합니다. [Azure PowerShell][aps]이 있는 경우 [HDInsight-Tools][tools] 모듈을 사용하여 파일을 업로드할 수 있습니다. 파일을 업로드하는 다른 방법에 대해서는 [HDInsight에 Hadoop 작업용 데이터 업로드][upload]를 참조하세요. 다음 명령에서는 `Add-HDInsightFile`을 사용하여 파일을 업로드 합니다.

    	PS C:> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"

    이 명령은 클러스터용 기본 저장소의 __example/data/u.data__에 __u.data__ 파일을 업로드합니다. 그런 다음 HDInsight 작업에서 __wasb:///example/data/u.data__ URI를 사용하여 이 데이터에 액세스할 수 있습니다.

###작업 실행

다음 Windows PowerShell 스크립트를 사용하여 앞서 업로드한 __u.data__ 파일과 함께 Mahout 추천 엔진을 사용하는 작업을 실행합니다.

	# The HDInsight cluster name.
	$clusterName = "the cluster name"

	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	# So dynamically grab it using Hive.
	$mahoutPath = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target*-job.jar' | where {$_.startswith("C:\apps\dist")}
	$noCRLF = $mahoutPath -replace "`r`n", ""
	$cleanedPath = $noCRLF -replace "\", "/"
	$jarFile = "file:///$cleanedPath"
    #
	# If you are using an earlier version of HDInsight,
	# set $jarFile to the jar file you
	# uploaded.
	# For example,
	# $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

	# The arguments for this job
	# * input - the path to the data uploaded to HDInsight
	# * output - the path to store output data
	# * tempDir - the directory for temp files
	$jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///temp/mahout"

	# Create the job definition
	$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job

	# Write out any error information
	Write-Host "STDERR"
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [AZURE.NOTE]Mahout 작업은 작업을 처리하는 동안 생성된 임시 데이터를 제거하지 않습니다. 이 `--tempDir` 매개 변수는 쉽게 삭제할 수 있도록 임시 파일을 특정 경로로 분리하기 위해 예제 작업에서 지정되었습니다.
>
> 이러한 파일을 제거하려면 [HDInsight에서 Hadoop 작업용 데이터 업로드][upload]에 언급된 도구 중 하나를 사용할 수 있습니다. 또는 [HDInsight-Tools][tools] 모듈에서 `Remove-HDInsightFile` 함수를 사용할 수 있습니다.
>
> 임시 파일 또는 출력 파일을 제거하지 않으면 작업을 다시 실행할 때 오류 메시지가 발생합니다.

Mahout 작업은 STDOUT로 출력을 반환하지 않습니다. 대신 지정된 출력 디렉터리에 __part-r-00000__으로 저장합니다. 파일을 다운로드하여 보려면 [HDInsight-Tools][tools] 모듈에서 `Get-HDInsightFile` 함수를 사용하세요.

다음은 파일의 내용에 대한 예제입니다.

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

첫 번째 열은 `userID`입니다. '[' 및 ']'에 포함된 값은 `movieId`:`recommendationScore`입니다.

###출력 보기

생성된 출력을 응용 프로그램에서 사용할 수 있긴 하지만 사용자가 읽을 수 있는 형식은 아닙니다. 앞서 __ml-100k__ 폴더에 압축을 푼 다른 파일 중 일부를 사용하여 `movieId`를 영화 이름으로 확인할 수 있으며, 다음 PowerShell스크립트에서 수행하는 내용입니다.

	<#
	.SYNOPSIS
	    Displays recommendations for movies.
	.DESCRIPTION
	    Displays recommendations generated by Mahout
	    with HDInsight example in a human readable format.
	.EXAMPLE
	    .\Show-Recommendation -userId 4
	        -userDataFile "u.data"
	        -movieFile "u.item"
	        -recommendationFile "output.txt"
	#>

	[CmdletBinding(SupportsShouldProcess = $true)]
	param(
	    #The user ID
	    [Parameter(Mandatory = $true)]
	    [String]$userId,

	    [Parameter(Mandatory = $true)]
	    [String]$userDataFile,

	    [Parameter(Mandatory = $true)]
	    [String]$movieFile,

	    [Parameter(Mandatory = $true)]
	    [String]$recommendationFile
	)
	# Read movie ID & description into hash table
	Write-Host "Reading movies descriptions" -ForegroundColor Green
	$movieById = @{}
	foreach($line in Get-Content $movieFile)
	{
	    $tokens = $line.Split("|")
	    $movieById[$tokens[0]] = $tokens[1]
	}
	# Load movies user has already seen (rated)
	# into a hash table
	Write-Host "Reading rated movies" -ForegroundColor Green
	$ratedMovieIds = @{}
	foreach($line in Get-Content $userDataFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        # Resolve the ID to the movie name
	        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
	    }
	}
	# Read recommendations generated by Mahout
	Write-Host "Reading recommendations" -ForegroundColor Green
	$recommendations = @{}
	foreach($line in get-content $recommendationFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        #Trim leading/treailing [] and split at ,
	        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
	        foreach($movieIdAndScore in $movieIdAndScores)
	        {
	            #Split at : and store title and score in a hash table
	            $idAndScore = $movieIdAndScore.Split(":")
	            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
	        }
	        break
	    }
	}

	Write-Host "Rated movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	               @{Expression={$_.Value};Label="Rating"}
	$ratedMovieIds | format-table $ratedFormat
	Write-Host "---------------------------" -ForegroundColor Green

	write-host "Recommended movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	                        @{Expression={$_.Value};Label="Score"}
	$recommendations | format-table $recommendationFormat

이 스크립트를 사용하려면 앞서 압축을 푼 __ml-100k__ 폴더뿐 아니라 Mahout 작업에서 생성된 __part-r-00000__ 출력 파일의 로컬 사본도 있어야 합니다. 다음은 스크립트를 실행하는 예제입니다.

	PS C:> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

출력은 다음과 유사합니다.

	Reading movies descriptions
	Reading rated movies
	Reading recommendations
	Rated movies
	---------------------------
	Movie                                    Rating
	-----                                    ------
	Devil's Own, The (1997)                  1
	Alien: Resurrection (1997)               3
	187 (1997)                               2
	(lines ommitted)

	---------------------------
	Recommended movies
	---------------------------

	Movie                                    Score
	-----                                    -----
	Good Will Hunting (1997)                 4.6504064
	Swingers (1996)                          4.6862745
	Wings of the Dove, The (1997)            4.6666665
	People vs. Larry Flynt, The (1996)       4.834559
	Everyone Says I Love You (1996)          4.707071
	Secrets & Lies (1996)                    4.818182
	That Thing You Do! (1996)                4.75
	Grosse Pointe Blank (1997)               4.8235292
	Donnie Brasco (1997)                     4.6792455
	Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Hadoop 명령줄을 사용하여 데이터 분류

Mahout에서 사용 가능한 분류 방법 중 하나는 [랜덤 포리스트][forest]를 빌드하는 것입니다. 이 방법은 학습 데이터를 사용하여 의사 결정 트리를 생성한 후 이를 사용하여 데이터를 분류하는 여러 단계의 프로세스입니다. 이 프로세스는 Mahout에서 제공하는 __org.apache.mahout.classifier.df.tools.Describe__ 클래스를 사용합니다. 지금은 Hadoop 명령줄을 사용하여 이 프로세스를 실행해야 합니다.

###데이터 로드

1. [NSL-KDD 데이터 집합](http://nsl.cs.unb.ca/NSL-KDD/)에서 다음 파일을 다운로드합니다.

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): 학습 파일

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): 테스트 데이터

2. 각 파일을 열어 상단에서 '@'으로 시작하는 줄을 제거한 후 파일을 저장합니다. 이러한 줄을 제거하지 않으면 Mahout에 이 데이터를 사용할 때 오류 메시지가 발생합니다.

2. __example/data__에 파일을 업로드합니다. [HDInsight-Tools][tools] 모듈에서 `Add-HDInsightFile` 함수를 사용하여 그렇게 할 수 있습니다.

###작업 실행

1. 이 작업에는 Hadoop 명령줄이 필요하므로 먼저 [Azure 포털][management]을 통해 원격 데스크톱을 사용하도록 설정해야 합니다. 포털에서 HDInsight 클러스터를 선택한 다음 __구성__ 페이지의 하단에서 __원격 사용__을 선택합니다.

    ![원격 사용][enableremote]

    메시지가 표시되면 원격 세션에 사용할 사용자 이름 및 암호를 입력합니다.

2. 원격 액세스가 설정되면 __연결__을 선택하여 연결을 시작합니다. 그러면 원격 데스크톱 세션을 시작하는 데 사용할 수 있는 __.rdp__ 파일이 다운로드됩니다.

    ![연결][connect]

3. 연결한 후 __Hadoop 명령줄__ 아이콘을 사용하여 Hadoop 명령줄을 엽니다.

	![hadoop cli][hadoopcli]

3. 다음 명령으로 Mahout을 사용하는 파일 설명자(__KDDTrain+.info__)를 생성합니다.

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	`N 3 C 2 N C 4 N C 8 N 2 C 19 N L`은 파일의 데이터 특성을 설명합니다. 예를 들어 L은 레이블을 나타냅니다.

4. 다음 명령을 사용하여 의사 결정 트리의 포리스트를 빌드합니다.

		hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    이 작업의 출력은 HDInsight 클러스터(__wasb://user/&lt;username>/nsl-forest/nsl-forest.seq)의 저장소에 있는 __nsl-forest__ 디렉터리에 저장됩니다. &lt;username>은 원격 데스크톱 세션에 사용되는 사용자 이름입니다. 이 파일은 사용자가 읽을 수 없습니다.

5. __KDDTest+.arff__ 데이터 집합을 분류하여 포리스트를 테스트합니다. 다음 명령을 사용합니다.

    	hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    이 명령은 다음과 유사한 형태로 분류 프로세스에 대한 요약 정보를 반환합니다.

	    14/07/02 14:29:28 INFO mapreduce.TestForest:

	    =======================================================
	    Summary
	    -------------------------------------------------------
	    Correctly Classified Instances          :      17560       77.8921%
	    Incorrectly Classified Instances        :       4984       22.1079%
	    Total Classified Instances              :      22544

	    =======================================================
	    Confusion Matrix
	    -------------------------------------------------------
	    a       b       <--Classified as
	    9437    274      |  9711        a     = normal
	    4710    8123     |  12833       b     = anomaly

	    =======================================================
	    Statistics
	    -------------------------------------------------------
	    Kappa                                       0.5728
	    Accuracy                                   77.8921%
	    Reliability                                53.4921%
	    Reliability (standard deviation)            0.4933

  또한 이 작업은 __wasb:///example/data/predictions/KDDTest+.arff.out__에 있는 파일을 생성합니다. 그러나 이 파일은 사용자가 읽을 수 없습니다.

> [AZURE.NOTE]Mahout 작업은 파일을 덮어쓰지 않습니다. 이러한 작업을 다시 실행하려는 경우 이전 작업에서 생성된 파일을 삭제해야 합니다.

##<a name="troubleshooting"></a>문제 해결

###<a name="install"></a>Mahout 설치

Mahout은 HDInsight 3.1 클러스터에 설치되며, 다음 단계를 사용하여 HDInsight 3.0 또는 HDInsight 2.1 클러스터에 수동으로 설치할 수 있습니다.

1. 사용할 Mahout 버전은 사용 중인 클러스터의 HDInsight 버전에 따라 다릅니다. 다음 [Azure PowerShell][aps] 명령을 사용하여 클러스터 버전을 찾을 수 있습니다.

    	PS C:> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * __HDInsight 2.1__의 경우 [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar)가 포함된 JAR(Java Archive) 파일을 다운로드할 수 있습니다.

  * __HDInsight 3.0의 경우__ [소스에서 Mahout을 빌드하고][build], HDInsight에서 제공하는 Hadoop 버전을 지정해야 합니다. 빌드 페이지에 나열된 필수 구성 요소를 설치하고, 소스를 다운로드한 후 다음 명령을 사용하여 Mahout jar 파일을 만듭니다.

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

    	> [AZURE.NOTE] When Mahout 1.0 is released, you should be able to use the prebuilt packages with HDInsight 3.0.

2. 클러스터용 기본 저장소의 __example/jars__에 jar 파일을 업로드합니다. 다음 예제에서는 [HDInsight-Tools][tools]의 add-hdinsightfile을 사용하여 파일을 업로드합니다.

    	PS C:> .\Add-HDInsightFile -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

###파일을 덮어쓸 수 없음

Mahout 작업은 처리 중 생성된 임시 파일을 정리하지 않습니다. 또한 이 작업은 기존 출력 파일을 덮어쓰지 않습니다.

Mahout 작업을 실행할 때 오류를 방지하려면 실행 사이에 임시 및 출력 파일을 삭제하거나 고유한 임시 및 출력 디렉터리 이름을 사용하세요.

###JAR 파일을 찾을 수 없음

HDInsight 3.1 클러스터에는 Mahout이 포함되어 있습니다. 경로 및 파일 이름에는 클러스터에 설치된 Mahout의 버전 번호가 포함됩니다. 이 자습서의 Windows PowerShell 예제 스크립트는 2014년 7월을 기준으로 유효한 경로를 사용하지만 향후 HDInsight 업데이트에서 버전 번호가 변경될 예정입니다. 사용 중인 클러스터용 Mahout JAR 파일의 현재 경로를 확인하려면 다음 Windows PowerShell 명령을 사용한 후 반환된 파일 경로를 참조하도록 스크립트를 수정하세요.

	Use-AzureHDInsightCluster -Name $clusterName
	$jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target*-job.jar'

###<a name="nopowershell"></a>Windows PowerShell에서 작동하지 않는 클래스

Windows PowerShell에서 사용하는 경우 다음 클래스를 사용하는 Mahout 작업은 다양한 오류 메시지를 반환합니다.

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

이러한 클래스를 사용하는 작업을 실행하려면 HDInsight 클러스터에 연결하여 Hadoop 명령줄을 사용하는 작업을 실행하세요. 예제에 대해서는 [Hadoop 명령줄을 사용하여 데이터 분류](#classify)를 참조하세요.

##다음 단계

이제 Mahout을 사용하는 방법을 배웠으므로 HDInsight에서 데이터로 작업하는 다른 방법을 검색합니다.

* [HDInsight에서 Hive](../hadoop-use-hive.md)
* [HDInsight에서 Pig](../hadoop-use-pig.md)
* [HDInsight에서 MapReduce](../hadoop-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: ../hdinsight-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=58_postMigration-->