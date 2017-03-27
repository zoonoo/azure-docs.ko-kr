---
title: "PowerShell에서 Mahout HDInsight를 사용하여 추천 생성 | Microsoft Docs"
description: "Apache Mahout Machine Learning 라이브러리를 사용하여 클라이언트에서 실행 중인 PowerShell에서 HDInsight(Hadoop)로 영화 추천을 생성하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: fdca3ed832dc0895a04dc3fda2dcf863d8938d8a
ms.lasthandoff: 01/18/2017


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>HDInsight(PowerShell)의 Hadoop 및 Apache Mahout을 사용하여 영화 추천 생성
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Azure HDInsight에서 [Apache Mahout](http://mahout.apache.org) 기계 학습 라이브러리를 사용하여 영화 추천을 생성하는 방법에 대해 알아봅니다. 이 문서에서는 Azure PowerShell을 사용하여 Mahout를 원격으로 실행하는 방법을 알아봅니다.

Mahout은 Apache Hadoop용 [기계 학습][ml] 라이브러리입니다. Mahout에는 필터링, 분류 및 클러스터링과 같은 데이터 처리를 위한 알고리즘이 포함됩니다. 이 문서에서는 권장 엔진을 사용하여 친구가 본 영화를 기준으로 영화 권장을 생성합니다.

## <a name="prerequisites"></a>필수 조건

* Linux 기반 HDInsight 클러스터입니다. HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 사용 시작][getstarted]을 참조하세요.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

* **Azure PowerShell이 포함된 워크스테이션**.

    > [!IMPORTANT]
    > Azure 서비스 관리자를 사용하여 HDInsight 리소스를 관리하는 Azure PowerShell 지원은 더 이상 **지원되지 않고** 2017년 1월 1일에 제거됩니다. 이 문서의 단계에서는 Azure Resource Manager로 작동하는 새 HDInsight cmdlet을 사용합니다.
    >
    > [Azure PowerShell 설치 및 구성](/powershell/azureps-cmdlets-docs) 단계를 수행하여 최신 버전의 Azure PowerShell을 설치합니다. Azure Resource Manager로 작동하는 새로운 cmdlet을 사용하도록 수정해야 하는 스크립트가 있는 경우 자세한 내용은 [HDInsight 클러스터에 대한 Azure Resource Manager 기반 개발 도구에 마이그레이션](hdinsight-hadoop-development-using-azure-resource-manager.md) 을 참조하세요.

## <a name="recommendations"></a>Azure PowerShell을 사용하여 추천 생성

> [!NOTE]
> 이 섹션에 사용된 작업은 지금은 Azure PowerShell에서 작동하지만 Mahout과 함께 제공되는 클래스 중 다수가 Azure PowerShell에서 현재 작동하지 않으며 Hadoop 명령줄을 사용하여 실행해야 합니다. Azure PowerShell에서 작동하지 않는 클래스의 목록은 [문제 해결](#troubleshooting) 섹션을 참조하세요.
>
> SSH를 사용하여 HDInsight에 연결하고 클러스터에서 Mahout 예제를 직접 실행하는 예제의 경우 [Mahout 및 HDInsight (SSH)를 사용하여 영화 추천 생성](hdinsight-hadoop-mahout-linux-mac.md)을 참조하세요.

Mahout에서 제공하는 기능 중 하나가 추천 엔진입니다. 이 엔진은 `userID`, `itemId` 및 `prefValue`(항목에 대한 사용자 선호도) 형식의 데이터를 허용합니다. 그런 다음 Mahout에서 동시 발생 분석을 수행하여 *특정 항목에 대한 선호도를 가진 사용자가 다른 항목에 대한 선호도도 갖고 있는지*확인할 수 있습니다. Mahout은 좋아하는 항목 선호도를 가진 사용자를 확인하며, 이 선호도는 추천하는 데 사용할 수 있습니다.

다음은 영화를 사용하는 매우 단순한 예제입니다.

* **동시 발생**: Joe, Alice 및 Bob은 모두 *스타워즈*, *제국의 역습* 및 *제다이의 귀환*을 좋아합니다. Mahout은 이러한 영화 중 하나를 좋아하면서 다른 두 개도 좋아하는 사용자를 확인합니다.

* **동시 발생**: Bob 및 Alice는 *보이지 않는 위협*, *클론의 습격* 및 *시스의 복수*도 좋아합니다. Mahout은 이전의&3;개 영화를 좋아하는 사용자가 이&3;개 영화도 좋아하는지 확인합니다.

* **유사성 추천**: Joe가 첫&3;개 영화를 좋아하므로, Mahout은 유사한 선호도를 가진 다른 사람이 좋아하지만 Joe는 본(좋아하거나 평가한) 적이 없는 영화를 검색합니다. 이 경우 Mahout은 *보이지 않는 위협*, *클론의 습격* 및 *시스의 복수*를 추천합니다.

### <a name="understanding-the-data"></a>데이터 이해

편의를 위해 [GroupLens Research][movielens]에서 Mahout과 호환되는 형식으로 영화에 대한 평가 데이터를 제공합니다. 이 데이터는 클러스터의 기본 저장소( `/HdiSamples//HdiSamples/MahoutMovieData`)에서 사용할 수 있습니다.

`moviedb.txt`(영화에 대한 정보) 및 `user-ratings.txt`의 두 가지 파일이 있습니다. user-ratings.txt 파일은 분석 중에 사용되고, moviedb.txt는 분석 결과를 표시할 때 사용자에게 친숙한 텍스트 정보를 제공하는 데 사용됩니다.

user-ratings.txt에 포함된 데이터의 구조는 `userID`, `movieID`, `userRating` 및 `timestamp`이며, 각 사용자의 영화 등급 평가를 보여 줍니다. 다음은 데이터의 예제입니다.

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>작업 실행

다음 Windows PowerShell 스크립트를 사용하여 영화 데이터로 Mahout 추천 엔진을 사용하는 작업을 실행합니다.

> [!NOTE]
> 이 파일은 HDInsight 클러스터에 연결하고 작업을 실행하는 데 사용하는 정보를 안내합니다. 작업을 완료하고 output.txt 파일을 다운로드하는 데 몇 분이 걸립니다.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster (the default name is usually 'admin')"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Use Hive to figure out the path to the mahout examples
#Because the file name/path has a version number in it that changes
$queryString = "!ls /usr/hdp/current/mahout-client"
$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString
$hiveJob=Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -HttpCredential $creds
$dummy = wait-azurermhdinsightjob -ClusterName $clusterName -JobId $hiveJob.JobId -HttpCredential $creds
#Get the files returned from Hive
$files=get-azurermhdinsightjoboutput -clustername $clusterName -JobId $hiveJob.JobId -DefaultContainer $container -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -HttpCredential $creds
#Find the file that starts with mahout-examples and ends in job.jar
$jarFile = $files | select-string "mahout-examples.+job\.jar" | % {$_.Matches.Value}
#Add the full path
$jarFile = "file:///usr/hdp/current/mahout-client/$jarFile"

# The arguments for the mahout job
# * input - the path to the data uploaded to HDInsight
# * output - the path to store output data
# * tempDir - the directory for temp files
$jobArguments = "-s", "SIMILARITY_COOCCURRENCE", `
                "--input", "/HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt",
                "--output", "/example/out",
                "--tempDir", "/example/temp"

# Create the job definition
$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
    -JarFile $jarFile `
    -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
    -Arguments $jobArguments

# Start the job
$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Wait on the job to complete
Write-Host "Wait for the job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds

# Write out any error information
Write-Host "STDERR"
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError

# Download the output
Get-AzureStorageBlobContent `
        -Blob example/out/part-r-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context
#Download movie and user files for use in displaying results
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/moviedb.txt" `
        -Container $container `
        -Destination moviedb.txt `
        -Context $context
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt" `
        -Container $container `
        -Destination user-ratings.txt `
        -Context $context
```

> [!NOTE]
> Mahout 작업은 작업을 처리하는 동안 생성된 임시 데이터를 제거하지 않습니다. 이 `--tempDir` 매개 변수는 쉽게 삭제할 수 있도록 임시 파일을 특정 디렉터리로 분리하기 위해 예제 작업에서 지정되었습니다.

Mahout 작업은 STDOUT로 출력을 반환하지 않습니다. 대신 지정된 출력 디렉터리에 **part-r-00000**으로 저장합니다. 이 스크립트는 이 파일을 워크스테이션의 현재 디렉터리에서 **output.txt** 에 다운로드합니다.

다음은 이 파일의 내용에 대한 예제입니다.

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

첫 번째 열은 `userID`입니다. '[' 및 ']'에 포함된 값은 `movieId`:`recommendationScore`입니다.

또한 스크립트는 출력의 가독성을 개선하도록 서식 지정하는 데 필요한 `moviedb.txt` 및 `user-ratings.txt` 파일을 다운로드합니다.

### <a name="view-the-output"></a>출력 보기

생성된 출력을 응용 프로그램에서 사용할 수 있긴 하지만 사용자가 읽을 수 있는 형식은 아닙니다. 서버의 `moviedb.txt`를 사용하여 영화 이름으로 `movieId`를 분석할 수 있습니다. 다음 PowerShell 스크립트를 사용하여 영화 이름과 함께 추천 항목을 표시합니다.

```powershell
<#
.SYNOPSIS
    Displays recommendations for movies.
.DESCRIPTION
    Displays recommendations generated by Mahout
    with HDInsight example in a human readable format.
.EXAMPLE
    .\Show-Recommendation -userId 4
        -userDataFile "user-ratings.txt"
        -movieFile "moviedb.txt"
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
```

다음은 스크립트를 실행하는 예제입니다.

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

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

## <a name="troubleshooting"></a>문제 해결

### <a name="cannot-overwrite-files"></a>파일을 덮어쓸 수 없음

Mahout 작업은 처리 중 생성된 임시 파일을 정리하지 않습니다. 또한 이 작업은 기존 출력 파일을 덮어쓰지 않습니다.

Mahout 작업을 실행할 때 오류를 방지하려면 실행 사이에 임시 및 출력 파일을 삭제하거나 고유한 임시 및 출력 디렉터리 이름을 사용하세요. 다음 PowerShell 스크립트를 사용하여 이 문서의 이전 스크립트에서 만든 파일을 제거합니다.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard, 
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Azure PowerShell에서 작동하지 않는 클래스

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

이러한 클래스를 사용하는 작업을 실행하려면 SSH를 사용하여 HDInsight 클러스터에 연결하고 명령줄에서 작업을 실행하세요. SSH를 사용하여 Mahout 작업을 실행하는 예제는 [Mahout 및 HDInsight(SSH)를 사용하여 영화 추천 생성](hdinsight-hadoop-mahout-linux-mac.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Mahout을 사용하는 방법을 배웠으므로 HDInsight에서 데이터로 작업하는 다른 방법을 검색합니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 사용](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

