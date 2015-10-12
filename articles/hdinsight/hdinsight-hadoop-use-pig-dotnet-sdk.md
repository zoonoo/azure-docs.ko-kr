<properties
   pageTitle="HDInsight에서 .NET과 Hadoop Pig 사용 | Microsoft Azure"
   description="HDInsight에서 Hadoop로 Pig 작업을 제출하기 위해 Hadoop용 .NET SDK를 사용하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#HDInsight에서 Hadoop용 .NET SDK를 사용하여 Pig 작업 실행

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

이 문서는 HDInsight 클러스터의 Hadoop에 Pig 작업을 제출하기 위해 Hadoop용 .NET SDK를 사용하는 예시를 보여 줍니다.

HDInsight .NET SDK는 .NET에서 HDInsight 클러스터로 더 쉽게 작업하도록 지원하는 .NET 클라이언트 라이브러리를 제공합니다. Pig를 사용하면 일련의 데이터 변환을 모델링하여 MapReduce 작업을 만들 수 있습니다. HDInsight 클러스터에는 Pig 작업을 제출하는 기본 C#응용 프로그램을 사용하는 방법에 대해 배웁니다.

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [HDInsight에서 Hadoop용 .NET SDK를 사용하여 Pig 작업 실행](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Azure HDInsight(HDInsight의 Hadoop) 클러스터(Windows 또는 Linux 기반)

* Visual Studio 2012 또는 2013

##<a id="certificate"></a>관리 인증서 만들기

Azure HDInsight에 응용 프로그램을 인증하려면 자체 서명된 인증서를 만들고 개발 워크스테이션에 설치하여 Azure 구독에 업로드해야 합니다.

이 작업을 수행하는 방법에 대한 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.

> [AZURE.NOTE] 인증서를 만들 때 나중에 사용할 것이므로 친숙한 이름을 사용해야 합니다.

##<a id="subscriptionid"></a>구독 ID 찾기

각각의 Azure 구독은 구독 ID라는 GUID 값으로 식별됩니다. 이 값을 찾으려면 다음 단계를 이용합니다.

1. [Azure Preview 포털][preview-portal]을 방문합니다.

2. 포털 왼쪽의 명령 모음에서 __모두 찾아보기__를 선택한 다음 __찾아보기__ 블레이드에서 __구독__을 선택합니다.

3. __구독 ID__ 블레이드에 표시되는 정보에서 사용할 구독을 찾고 **구독 ID** 열의 값을 기록해 둡니다.

나중에 사용할 것이므로 구독 ID를 저장합니다.

##<a id="create"></a>응용 프로그램 만들기

1. Visual Studio 2012 또는 2013 열기

2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.

3. 새 프로젝트에서 다음 값을 입력하거나 선택합니다.

	<table>
<tr>
<th>속성</th>
<th>값</th>
</tr>
<tr>
<th>Category</th>
<th>Templates/Visual C#/Windows</th>
</tr>
<tr>
<th>Template</th>
<th>Console Application</th>
</tr>
<tr>
<th>이름</th>
<th>SubmitPigJob</th>
</tr>
</table>

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **라이브러리 패키지 관리자** 또는 **Nuget 패키지 관리자**를 선택한 다음 **패키지 관리자 콘솔**을 선택합니다.

6. 콘솔에서 다음 명령을 실행하여 .NET SDK 패키지를 설치합니다.

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. 솔루션 탐색기에서 **Program.cs**를 두 번 클릭하여 엽니다. 기존 코드를 다음으로 바꿉니다.

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

		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");

		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");

		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";

		            // The Pig Latin statements to run
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
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);

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

		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. 파일을 저장합니다.

##<a id="run"></a>응용 프로그램 실행

**F5** 키를 눌러 응용 프로그램을 시작합니다. 메시지가 표시되면 **구독 ID**, **인증서 이름** 및 **HDInsight 클러스터 이름**을 입력합니다. 응용 프로그램이 실행되면 다음과 유사하게 끝나는 여러 줄의 정보를 생성합니다.

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

**Enter** 키를 눌러 응용 프로그램을 종료합니다.

##<a id="summary"></a>요약

이처럼 Hadoop용 .NET SDK를 사용하면 Pig 작업을 HDInsight 클러스터를 제출 하고, 작업 상태를 모니터링하며, 출력을 검색하는 .NET 응용 프로그램을 만들 수 있습니다.

##<a id="nextsteps"></a>다음 단계

HDInsight에서 Pig에 대한 일반 정보.

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

* [SSH를 사용하여 HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
[preview-portal]: https://portal.azure.com/

<!---HONumber=Oct15_HO1-->