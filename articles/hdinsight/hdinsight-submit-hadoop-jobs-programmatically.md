<properties
	pageTitle="HDInsight에서 Hadoop 작업 제출 | Microsoft Azure"
	description="Azure HDInsight Hadoop에 Hadoop 작업을 제출하는 방법에 대해 알아봅니다."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="jgao"/>

# HDInsight에서 Hadoop 작업 제출

Azure PowerShell을 사용하여 MapReduce, Hive 작업을 제출하는 방법과 HDInsight.NET SDK를 사용하여 MapReduce, Hadoop 스트리밍 및 Hive 작업을 제출하는 방법을 알아봅니다.

> [AZURE.NOTE]이 문서의 단계는 Windows 클라이언트에서 수행되어야 합니다. HDInsight에서 MapReduce, Hive 또는 Pig 와 작업하도록 Linux, OS X 또는 Unix 클라이언트 사용에 대한 자세한 내용은 다음 문서를 참조하고 각 **SSH** 또는 **Curl** 링크를 선택합니다.
>
> - [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
> - [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
> - [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure HDInsight 클러스터**. 자세한 내용은 [HDInsight 시작][hdinsight-get-started] 또는 [HDInsight에서 Hadoop 클러스터 프로비전][hdinsight-provision]을 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.



##Azure PowerShell을 사용하여 MapReduce 작업 제출
Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. HDInsight와 함께 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]를 참조하세요.

Hadoop MapReduce는 방대한 양의 데이터를 처리하는 응용 프로그램을 작성하기 위한 소프트웨어 프레임워크입니다. HDInsight 클러스터는 여러 MapReduce 예제가 포함된 JAR 파일(*\\example\\jars\\hadoop-mapreduce-examples.jar*에 위치함)과 함께 제공됩니다.

예제 중 하나는 소스 파일에서 단어의 빈도수를 계산하는 것입니다. 이 세션에서는 단어 개수 샘플을 실행하기 위해 워크스테이션에서 Azure PowerShell을 사용하는 방법에 대해 알아봅니다. MapReduce 작업의 개발 및 실행에 대한 자세한 내용은 [HDInsight와 함께 MapReduce 사용][hdinsight-use-mapreduce]을 참조하세요.

**Azure PowerShell을 사용하여 단어 개수 MapReduce 프로그램을 실행하려면**

1.	**Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

3. 다음 PowerShell 명령을 실행하여 다음 변수를 설정합니다:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"

	구독 이름은 HDInsight 클러스터를 만드는 데 사용한 것입니다. HDInsight 클러스터는 MapReduce 작업 실행에 사용하려는 것입니다.

5. 다음 명령을 실행하여 MapReduce 작업 정의를 만듭니다.

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	두 개의 인수가 있습니다. 첫 번째 인수는 원본 파일 이름이고, 두 번째 인수는 출력 파일 경로입니다. wasb:// 접두사에 대한 자세한 내용은 [HDInsight와 함께 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

6. 다음 명령을 실행하여 MapReduce 작업을 실행합니다.

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

	MapReduce 작업 정의뿐 아니라 MapReduce 작업을 실행하려는 HDInsight 클러스터 이름도 제공해야 합니다.

7. 다음 명령을 실행하여 MapReduce 작업 완료를 확인합니다.

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600


8. 다음 명령을 실행하여 MapReduce 작업 실행 오류를 확인합니다.

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

	다음 스크린샷은 성공한 실행 출력을 보여 줍니다. 그렇지 않으면 오류 메시지가 표시됩니다.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]


**MapReduce 작업 결과를 검색하려면**

1. **Azure PowerShell**을 엽니다.
2. 다음 PowerShell 명령을 실행하여 다음 변수를 설정합니다:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

	저장소 계정 이름은 HDInsight 클러스터를 만드는 동안 지정한 Azure 저장소 이름입니다. 저장소 계정은 기본 HDInsight 클러스터 파일 시스템으로 사용되는 Blob 컨테이너를 호스트하는 데 사용됩니다. 일반적으로 컨테이너 이름은 클러스터를 만들 때 다른 이름을 지정하지 않을 경우 HDInsight 클러스터와 동일한 이름을 공유합니다.

3. 다음 명령을 실행하여 Azure Blob 저장소 컨텍스트 개체를 만듭니다:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription**은 여러 구독이 있을 경우 현재 구독을 설정하는 데 사용되며, 기본 구독은 사용하지 않습니다.

4. 다음 명령을 실행하여 Blob 컨테이너의 MapReduce 작업 출력을 워크스테이션으로 다운로드합니다:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*example/data/WordCountOutput* 폴더는 MapReduce 작업을 실행할 때 지정된 출력 폴더입니다. *part-r-00000*은 MapReduce 작업 출력의 기본 파일 이름입니다. 로컬 폴더의 동일한 폴더 구조에 파일이 다운로드됩니다. 예를 들어 다음 스크린샷에서 현재 폴더는 C: 루트 폴더입니다. 파일은 다음에 다운로드됩니다:

**C:\\example\\data\\WordCountOutput*

5. 다음 명령을 실행하여 MapReduce 작업 출력 파일을 인쇄합니다.

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	MapReduce 작업에서 *part-r-00000*이라는 파일을 생성하며 단어와 개수를 포함합니다. 스크립트는 **findstr** 명령을 사용하여 "there"가 포함된 모든 단어를 나열합니다.


> [AZURE.NOTE]./example/data/WordCountOutput/part-r-00000(MapReduce 작업의 여러 줄 출력)을 메모장에서 열면 줄 바꿈이 제대로 렌더링되지 않는 것을 확인할 수 있습니다. 예상된 동작입니다.









































































































































##Azure PowerShell을 사용하여 Hive 작업 제출
[Apache Hive][apache-hive]는 대규모 데이터의 요약, 쿼리 및 분석에 적용할 수 있는 SQL 같은 스크립팅 언어인 *HiveQL*을 통해 MapReduce 작업을 실행하는 수단을 제공합니다.

HDInsight 클러스터는 *hivesampletable*이라는 샘플 Hive 테이블과 함께 제공됩니다. 이 세션에서는 Azure PowerShell을 사용하여 Hive 테이블의 일부 데이터를 나열하기 위한 Hive 작업을 실행합니다.

**Azure PowerShell을 사용하여 Hive 작업 실행**

1.	**Azure PowerShell**을 엽니다. Azure PowerShell 콘솔 창을 여는 방법에 대한 지침은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

2. 다음 명령에서 처음 2개 변수를 설정한 후 명령을 실행합니다.

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring은 HiveQL 쿼리입니다.

3. 다음 명령을 실행하여 Hive 작업을 실행할 Azure 구독과 클러스터를 선택합니다:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. 다음 명령를 실행하여 Hive 작업을 제출합니다:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	사용자는 Hadoop 분산 파일 시스템 (HDFS)에서 HiveQL 스크립트 파일을 지정 하는 **-파일** 스위치를 사용할 수 있습니다.

Hive에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight-use-hive]을 참조하세요.


## Visual Studio를 사용하여 Hive 작업 제출

[Visual Studio용 HDInsight Hadoop 도구 사용 시작][hdinsight-visual-studio-tools]을 참조하세요.

##Azure PowerShell을 사용하여 Sqoop 작업 제출

[HDInsight와 함께 Sqoop 사용][hdinsight-use-sqoop]을 참조하세요.

##HDInsight .NET SDK를 사용하여 MapReduce 작업 제출
HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. HDInsight 클러스터는 여러 MapReduce 예제가 포함된 JAR 파일(*\\example\\jars\\hadoop-mapreduce-examples.jar*에 위치함)과 함께 제공됩니다. 예제 중 하나는 소스 파일에서 단어의 빈도수를 계산하는 것입니다. 이 세션에서는 단어 개수 샘플을 실행하는 .NET 응용 프로그램을 만드는 방법에 대해 알아봅니다. MapReduce 작업의 개발 및 실행에 대한 자세한 내용은 [HDInsight와 함께 MapReduce 사용][hdinsight-use-mapreduce]을 참조하세요.

**WordCount MapReduce 작업을 제출하려면**

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 만듭니다.
2. NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Program.cs 파일에서 다음 using 문을 사용합니다.

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Main() 함수에 다음 코드를 추가합니다.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. 클래스에 다음과 같은 도우미 함수를 추가합니다.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. **F5** 키를 눌러 응용 프로그램을 실행합니다.










##HDInsight .NET SDK를 사용하여 Hadoop 스트리밍 작업 제출
HDInsight 클러스터는 C#으로 개발된 단어 수 계산 Hadoop 스트림 프로그램과 함께 제공됩니다. 매퍼 프로그램은 */example/apps/cat.exe*이고 reduce 프로그램은 */example/apps/wc.exe*입니다. 이 세션에서는 단어 개수 샘플을 실행하는 .NET 응용 프로그램을 만드는 방법에 대해 알아봅니다.

MapReduce 작업 제출을 위한 .Net 응용 프로그램을 만드는 방법에 대한 자세한 내용은 [HDInsight .NET SDK를 사용하여 MapReduce 작업 제출](#mapreduce-sdk)을 참조하세요.

Hadoop 스트리밍 작업 개발 및 배포에 대한 자세한 내용은 [HDInsight용 C# Hadoop 스트리밍 프로그램 개발][hdinsight-develop-streaming-jobs]을 참조하세요.

다음 절차는 Windows의 HDInsight 클러스터에서만 작동합니다. C# 스트리밍은 Linux 클러스터에서 아직 지원되지 않습니다. 그러나 .NET 프로그램을 사용하여 Linux 클러스터에서 지원되는 다른 프로그래밍 언어로 작성된 스트림 작업을 제출할 수 있습니다. 예를 들어 Python입니다. Python 스트리밍 예제는 [HDInsight용 Python 스트리밍 프로그램 개발](hdinsight-hadoop-streaming-python.md)을 참조하세요.

**WordCount MapReduce 작업을 제출하려면**

1. Visual Studio 패키지 관리자 콘솔에서 다음 Nuget 명령을 실행하여 패키지를 가져옵니다.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Program.cs 파일에서 다음 using 문을 사용합니다.

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Main() 함수에 다음 코드를 추가합니다.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. 도움말 함수를 클래스에 추가합니다.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. **F5** 키를 눌러 응용 프로그램을 실행합니다.

##HDInsight .NET SDK를 사용하여 Hive 작업 제출
HDInsight 클러스터는 *hivesampletable*이라는 샘플 Hive 테이블과 함께 제공됩니다. 이 세션에서는 HDInsight 클러스터에서 만든 Hive 테이블을 나열하기 위한 Hive 작업을 실행하는 .NET 응용 프로그램을 만듭니다. Hive에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight-use-hive]을 참조하세요.

SDK를 사용하여 HDInsight 클러스터를 만들려면 다음 절차가 필요합니다.

- HDInsight .NET SDK 설치
- 콘솔 응용 프로그램 만들기
- 응용 프로그램 실행


**HDInsight .NET SDK를 설치하려면** [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)에서 가장 최근에 게시된 SDK 빌드를 설치할 수 있습니다. 지침은 다음 절차에서 설명합니다.

**Visual Studio 콘솔 응용 프로그램을 만들려면**

1. Visual Studio 2013 또는 2015 열기

2. 다음 설정으로 새 프로젝트를 만듭니다.

	|속성|값|
	|--------|-----|
	|Template|Templates/Visual C#/Windows/Console Application|
	|이름|SubmitHiveJob|

3. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.
4. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	.NET 라이브러리 및 이에 대한 참조가 현재 Visual Studio 프로젝트에 추가됩니다.

5. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 열고 다음 코드를 붙여 넣은 후 변수 값을 제공합니다.

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. **F5** 키를 눌러 응용 프로그램을 실행합니다.

##Visual Studio용 HDInsight 도구를 사용하여 작업 제출

Visual Studio용 HDInsight 도구를 사용하여 Hive 쿼리 및 Pig 스크립트를 실행할 수 있습니다. [HDInsight용 Visual Studio Hadoop 도구를 사용하기 시작](hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.


##다음 단계
이 문서에서는 HDInsight 클러스터를 만드는 여러 가지 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [HDInsight에서 Hadoop 클러스터 만들기][hdinsight-provision]
* [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]
* [HDInsight Cmdlet 참조 문서][hdinsight-powershell-reference]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Oct15_HO3-->