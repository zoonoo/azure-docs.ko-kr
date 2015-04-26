<properties 
	pageTitle="HDInsight에서 Hadoop Pig 사용 | Azure" 
	description="HDInsight에서 Pig를 사용하는 방법에 대해 알아봅니다. 이를 위해 Pig Latin 문을 작성하여 응용 프로그램 로그 파일을 분석하고 데이터에 대해 쿼리를 실행하여 분석을 위한 출력을 생성합니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>



# HDInsight에서 Hadoop과 Pig 사용

이 자습서에서는 HDInsight에서 [Apache Pig][apachepig-home](영문) 작업을 실행하여 큰 데이터 파일을 분석하는 방법을 알아봅니다. Pig는 Java 코드를 작성하는 대신 *MapReduce* 작업을 실행하는 스크립트 언어를 제공합니다. Pig의 스크립트 언어는 *Pig Latin*입니다.

**필수 조건**

* Azure HDInsight 클러스터를 미리 프로비전해야 합니다. 자세한 내용은 [Azure HDInsight 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 자습서를 완료하려면 클러스터의 이름이 필요합니다.

* 워크스테이션에서 Azure PowerShell을 미리 설치 및 구성해야 합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요. 

**완료 예상 시간:** 30분

## 이 문서의 내용

* [Pig를 사용하는 이유](#usage)
* [이 자습서에서 수행하는 작업](#what)
* [분석할 데이터 식별](#data)
* [Pig Latin 이해](#understand)
* [PowerShell을 사용하여 Pig 작업 제출](#powershell)
* [HDInsight .NET SDK를 사용하여 Pig 작업 제출](#sdk)
* [다음 단계](#nextsteps)
 
## <a id="usage"></a>Pig를 사용하는 이유
빅데이터는 관계형 데이터베이스 및 통계/시각화 패키지를 사용하여 작업하기가 어렵습니다. 데이터 양이 많고 이 데이터를 계산하기 위해 수십, 수백, 심지어 수천 대의 서버에서 병렬로 실행하는 소프트웨어가 있어야 합당한 시간 내에 이 데이터를 계산할 수 있는 경우가 많습니다. Hadoop의 *MapReduce* 프레임워크에서는 대규모 컴퓨터 클러스터에서 구조화된 데이터 및 구조화되지 않은 대량의 데이터를 안정성과 내결함성이 높은 방식으로 병렬 처리하는 응용 프로그램을 작성할 수 있습니다.

![HDI.Pig.Architecture][image-hdi-pig-architecture]

[Apache *Pig*][apachepig-home](영문)는 Java 기반 MapReduce 프레임워크를 통한 추상화 계층을 제공하므로 사용자가 Java 또는 MapReduce에 대해 몰라도 데이터를 분석할 수 있습니다. Pig는 쉽게 사용할 수 있는 데이터 흐름 언어인 *Pig Latin*을 통해 큰 데이터 집합을 분석하는 플랫폼입니다. Pig를 사용하면 매퍼 및 리듀서 프로그램을 작성하는 데 필요한 시간이 줄어듭니다. 그리고 Java에 대해 몰라도 됩니다. Java 코드를 Pig와 유연하게 결합할 수도 있습니다. 사람이 인식할 수 있는 Pig 코드 다섯 줄 미만으로 많은 복잡한 알고리즘을 작성할 수 있습니다. 

Pig Latin 문은 다음과 같은 일반적인 흐름을 따릅니다.   

- **로드**: 파일 시스템에서 조작할 데이터 읽기
- **변환**: 데이터 조작 
- **덤프 또는 저장**: 데이터를 화면에 출력하거나 처리를 위해 저장

Pig Latin에 대한 자세한 내용은 [Pig Latin 참조 설명서 1][piglatin-manual-1](영문) 및 [Pig Latin 참조 설명서 2][piglatin-manual-2](영문)를 참조하세요.

## <a id="what"></a>이 자습서에서 수행하는 작업
이 자습서에서는 Apache 로그 파일(*sample.log*)을 분석하여 INFO, DEBUG, TRACE 등의 여러 로그 수준 수를 확인합니다. 이 문서에서 수행할 작업이 아래 두 그림에 표시되어 있습니다. 첫 번째 그림은 sample.log 파일의 코드 조각을 보여 줍니다.

![Whole File Sample][image-hdi-log4j-sample]

두 번째 그림은 스크립트에서 Pig 코드 줄을 실행할 때 데이터의 흐름과 변환을 보여 줍니다.

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]

이 자습서에서 만드는 Pig 작업도 같은 흐름을 따릅니다.

## <a id="data"></a>분석할 데이터 식별

HDInsight는 Azure Blob 저장소 컨테이너를 Hadoop 클러스터의 기본 파일 시스템으로 사용합니다. 클러스터 프로비전의 일부분으로 일부 샘플 데이터 파일이 Blob 저장소에 추가됩니다. 클러스터에서 Hive 쿼리를 실행하는 데 이러한 샘플 데이터 파일을 사용할 수 있습니다. 원하는 경우 클러스터와 연결된 Blob 저장소 계정에 고유한 데이터 파일을 업로드할 수도 있습니다. 해당 지침은 [HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요. HDInsight에서 Azure Blob 저장소를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

Blob 저장소의 파일에 액세스하기 위한 구문은 다음과 같습니다.

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만, HDInsight 3.0 클러스터에서는 지원되지 않으며 이후 버전에서도 지원되지 않습니다.

기본 파일 시스템 컨테이너에 저장된 파일은 HDInsight에서 다음 URI를 사용하여 액세스할 수도 있습니다(sample.log를 예로 사용함).  이 파일은 이 자습서에서 사용되는 데이터 파일입니다.

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

저장소 계정에서 바로 파일에 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

	example/data/sample.log

이 문서에서는 HDInsight 클러스터와 함께 제공되며 *\example\data\sample.log*에 저장되는 *log4j* 샘플 파일을 사용합니다. 자체 데이터 파일 업로드에 대한 자세한 내용은 [HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요.




## <a id="understand"></a> Pig Latin 이해

이 섹션에서는 일부 Pig Latin 문을 개별적으로 검토한 다음 문을 실행한 후의 결과를 검토합니다. 다음 섹션에서는 PowerShell을 실행하여 샘플 로그 파일 분석을 위해 Pig 문을 함께 실행합니다. 개별 Pig Latin 문은 HDInsight 클러스터에 대해 직접 실행해야 합니다.

1. [RDP를 사용하여 HDInsight 클러스터에 연결](http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp)의 지침에 따라 HDInsight 클러스터에 대해 원격 데스크톱을 사용하도록 설정합니다. 클러스터 노드에 로그인한 다음 바탕 화면에서 **Hadoop 명령줄**을 클릭합니다.

2. 명령줄에서 **Pig**가 설치된 디렉터리로 이동합니다. 형식:

		C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3. 명령 프롬프트에서 *pig*를 입력하고 Enter 키를 눌러 *grunt* 셸로 이동합니다.

		C:\apps\dist\pig-<version>\bin>pig
		...
		grunt>  

4. 다음을 입력하여 파일 시스템의 샘플 파일에서 데이터를 로드하고 결과를 표시합니다. 

		grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
		grunt> DUMP LOGS;
	
	다음과 유사하게 출력됩니다.
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

5. 데이터 파일에서 각 줄을 검토하여 6개의 로그 수준에서 일치 항목을 찾습니다.

		grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
6. 일치 항목이 없는 행을 필터링하고 결과를 표시합니다. 그러면 빈 행이 제거됩니다.

		grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		grunt> DUMP FILTEREDLEVELS;

	다음과 유사하게 출력됩니다.

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

7. 모든 로그 수준을 고유 행으로 그룹화하고 결과를 표시합니다.

		grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		grunt> DUMP GROUPEDLEVELS;

	다음과 유사하게 출력됩니다.

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


8. 각 그룹에서 로그 수준의 수를 계산하고 결과를 표시합니다.

		grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		grunt> DUMP FREQUENCIES;
 
	다음과 유사하게 출력됩니다.

		(INFO,96)
		(WARN,11)
		(DEBUG,434)
		(ERROR,6)
		(FATAL,2)
		(TRACE,816)

9. 빈도를 내림차순으로 정렬하고 결과를 표시합니다.

		grunt> RESULT = order FREQUENCIES by COUNT desc;
		grunt> DUMP RESULT;   

	다음과 유사하게 출력됩니다. 

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
	 	(ERROR,6)
		(FATAL,2)

## <a id="powershell"></a>PowerShell을 사용하여 Pig 작업 제출

이 섹션에서는 PowerShell cmdlet 사용 지침을 설명합니다. 이 섹션을 진행하기 전에 먼저 로컬 환경을 설정하고 Azure 연결을 구성해야 합니다. 자세한 내용은 [Azure HDInsight 시작][hdinsight-get-started] 및 [PowerShell을 사용하여 HDInsight 관리][hdinsight-admin-powershell]를 참조하세요.


**PowerShell을 사용하여 Pig Latin을 실행하려면**

1. Windows PowerShell ISE를 엽니다. Windows 8 시작 화면에서 **PowerShell_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. 자세한 내용은[Windows 8 및 Windows에서 Windows PowerShell 시작][powershell-start](영문)을 참조하세요.

2. 아래쪽 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

		Add-AzureAccount

	Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 구독 연결을 추가하는 이 메서드의 시간이 초과하고 12시간 후에 cmdlet을 다시 실행해야 합니다. 

	> [AZURE.NOTE] 여러 Azure 구독이 있는 경우 기본 구독이 사용하려는 구독이 아니면 <strong>Select-AzureSubscription</strong> cmdlet을 사용하여 현재 구독을 선택합니다.
2. 스크립트 창에서 다음 줄을 복사하여 붙여 넣습니다.

		$clusterName = "<HDInsightClusterName>" 	#Specify the cluster name
		$statusFolder = "/tutorials/usepig/status"	#Specify the folder to dump results

	지정한 상태 폴더가 없으면 스크립트가 해당 폴더를 만듭니다. 

3. 다음 줄을 스크립트 창에 추가합니다. 이러한 줄은 Pig Latin 쿼리 문자열을 정의하고 Pig 작업 정의를 만듭니다.

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

	**-File** 스위치를 사용하여 HDFS에서 Pig 스크립트 파일을 지정할 수도 있습니다. **-StatusFolder** 스위치는 표준 오류 로그 및 표준 출력 파일을 폴더에 추가합니다.

4. Pig 작업 제출을 위한 다음 줄을 추가합니다.
		
		# Submit the Pig job
		Write-Host "Submit the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

5. Pig 작업이 완료될 때까지 기다리기 위한 다음 줄을 추가합니다.		

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Pig 작업 출력을 인쇄하기 위한 다음 줄을 추가합니다.
		
		# Print the standard error and the standard output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	> [AZURE.NOTE] 다음 스크린샷에서 Get-AzureHDInsightJobOut cmdlet 중 하나는 출력을 간단히 나타내기 위해 주석으로 처리되었습니다.   

7. **F5** 키를 눌러 스크립트를 실행합니다.

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	Pig 작업은 다양한 로그 종류의 빈도를 계산합니다.


## <a id="sdk"></a>HDInsight .NET SDK를 사용하여 Pig 작업 제출

다음 단계에 따라 C# 응용 프로그램을 사용하여 Pig 작업을 제출합니다.   Hadoop 작업 제출을 위한 C# 응용 프로그램을 만드는 방법은 [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]을 참조하세요.

1. 자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.

2. Visual Studio 콘솔 응용 프로그램을 만들고 HDInsight 패키지를 설치합니다. 도구 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭하고 프롬프트에 다음을 입력합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

3. Program.cs를 두 번 클릭하고 다음 코드를 붙여 넣어 Pig 작업을 제출합니다. 이때 변수에 값을 지정해야 합니다.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		
		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;
		
		namespace SubmitPigJobs
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					// Set the variables
					string subscriptionID = "<Azure subscription ID>";
					string certFriendlyName = "<certificate friendly name>";
			
					string clusterName = "<HDInsight cluster name>";
		            string statusFolderName = @"/tutorials/usepig/status";
		
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };
		
		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
		
		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);
		
		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);
		
		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);
		
		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
		            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            Console.WriteLine("----- Press ENTER to continue.");
		            Console.ReadLine();
		        }
		
		        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
		        {
		            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
		            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
		            {
		                jobInProgress = client.GetJob(jobInProgress.JobId);
		                Thread.Sleep(TimeSpan.FromSeconds(10));
		            }
		        }
		    }
		}

## <a id="nextsteps"></a>다음 단계

Pig를 사용하여 데이터를 분석할 수 있지만, HDInsight에 포함된 다른 언어도 유용할 수 있습니다. Hive는 HDInsight에 저장된 데이터를 손쉽게 쿼리할 수 있는 SQL과 유사한 쿼리 언어를 제공하며, Java로 작성된 MapReduce 작업은 복잡한 데이터를 분석할 수 있게 합니다. 자세한 내용은 다음을 참조하세요.


* [Azure HDInsight 시작][hdinsight-get-started]
* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell/

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png


<!--HONumber=42-->
