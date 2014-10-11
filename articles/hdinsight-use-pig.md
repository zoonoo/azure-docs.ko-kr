<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# HDInsight에서 Hadoop과 Pig 사용

HDInsight에서 [Apache Pig][] 작업을 실행하여 Apache log4j 로그 파일을 분석하는 방법을 알아봅니다.

**예상 완료 시간:** 30분

## 이 문서에서는 다음을 수행합니다.

-   [Pig 사용 사례][]
-   [필수 조건][]
-   [Pig Latin 이해][]
-   [PowerShell을 사용하여 Pig 작업 제출][]
-   [HDInsight .NET SDK를 사용하여 Pig 작업 제출][]
-   [다음 단계][]

## <span id="usage"></span></a>Pig 사용 사례

데이터베이스는 작은 데이터 집합과 대기 시간이 짧은 쿼리에 매우 적합합니다. 그러나 빅데이터와 테라바이트 규모의 대용량 데이터 집합의 경우에는 기존의 SQL 데이터베이스가 이상적인 솔루션이 아닙니다. 역사적으로 데이터베이스 로드가 증가하고 성능이 떨어지면서, 데이터베이스 관리자는 더 큰 하드웨어를 구입해야 했습니다.

일반적으로 모든 응용 프로그램은 오류, 예외 및 기타 코딩 문제를 로그 파일에 저장하므로, 관리자는 문제를 검토하거나 로그 파일 데이터에서 특정 메트릭을 생성할 수 있습니다. 이러한 로그 파일은 대개 크기가 상당히 크며, 처리 및 마이닝을 거쳐야 하는 풍부한 데이터를 포함합니다.

로그 파일은 빅데이터의 좋은 예입니다. 빅데이터는 관계형 데이터베이스 및 통계/시각화 패키지를 사용하여 작업하기가 어렵습니다. 데이터 양이 많고 이 데이터를 계산하기 위해 수십, 수백, 심지어 수천 대의 서버에서 병렬로 실행하는 소프트웨어가 있어야 합당한 시간 내에 이 데이터를 계산할 수 있는 경우가 많습니다. Hadoop의 MapReduce 프레임워크에서는 대규모 컴퓨터 클러스터에서 구조화된 데이터 및 구조화되지 않은 대량의 데이터를 안정성과 내결함성이 높은 방식으로 병렬 처리하는 응용 프로그램을 작성할 수 있습니다.

[Apache *Pig*][Apache Pig]는 Java 코드를 작성하는 대신 *MapReduce* 작업을 실행하는 스크립트 언어를 제공합니다. Pig의 스크립트 언어는 *Pig Latin*입니다. Pig Latin 문은 다음과 같은 일반적인 흐름을 따릅니다.

-   **로드**: 파일 시스템에서 조작할 데이터 읽기
-   **변환**: 데이터 조작
-   **덤프 또는 저장**: 데이터를 화면에 출력하거나 처리를 위해 저장

Pig를 사용하면 매퍼 및 리듀서 프로그램을 작성하는 데 필요한 시간이 줄어듭니다. 다시 말해서 Java가 필요 없으며 상용구 코드도 필요 없습니다. Java 코드를 Pig와 유연하게 결합할 수도 있습니다. 사람이 인식할 수 있는 Pig 코드 다섯 줄 미만으로 많은 복잡한 알고리즘을 작성할 수 있습니다.

다음 두 그림은 이 자습서에서 수행할 내용을 시각적으로 표현합니다. 이 그림은 스크립트에서 Pig 코드 줄을 진행함에 따른 데이터의 흐름과 변환을 표현하는 데이터 집합의 전형적인 샘플을 보여 줍니다. 첫 번째 그림은 log4j 파일 샘플을 보여 줍니다.

![전체 파일 샘플][]

두 번째 그림은 데이터 변환을 보여 줍니다.

![HDI.PIG.Data.Transformation][]

Pig Latin에 대한 자세한 내용은 [Pig Latin 참조 설명서 1][](영문) 및 [Pig Latin 참조 설명서 2][](영문)를 참조하세요.

## <span id="prerequisites"></span></a>필수 조건

이 문서를 시작하기 전에 다음 요구 사항을 확인하세요.

-   Azure HDInsight 클러스터. 자세한 내용은 [Azure HDInsight 시작][] 또는 [HDInsight 클러스터 프로비전][]을 참조하세요. 자습서를 완료하려면 다음 데이터가 필요합니다.

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">클러스터 속성</th>
    <th align="left">PowerShell 변수 이름</th>
    <th align="left">값</th>
    <th align="left">설명</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight 클러스터 이름</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">이 자습서를 실행할 HDInsight 클러스터입니다.</td>
    </tr>
    </tbody>
    </table>

-   Azure PowerShell을 설치하고 구성합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성][]을 참조하세요.

**HDInsight 저장소 이해**

HDInsight는 데이터 저장소로 Azure Blob 저장소를 사용합니다. 이를 *WASB* 또는 *Azure 저장소 - Blob*이라고 합니다. WASB는 Azure Blob 저장소에 구현한 Microsoft의 HDFS입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][]을 참조하세요.

HDInsight 클러스터를 프로비전할 때 Azure 저장소 계정 및 이 계정에서 오는 특정 Blob 저장소 컨테이너가 HDFS의 경우와 같이 기본 파일 시스템으로 지정됩니다. 프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전][]을 참조하세요. 이 자습서에서 사용되는 PowerShell 스크립트를 간소화하기 위해 모든 파일이 */tutorials/usepig*에 위치한 기본 파일 시스템 컨테이너에 저장됩니다. 기본적으로 이 컨테이너 이름은 HDInsight 클러스터 이름과 동일합니다.
WASB 구문은 다음과 같습니다.

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 클러스터 버전 3.0에서는 *wasb://* 구문만 지원됩니다. 이전 *asv://* 구문은 HDInsight 2.1 및 1.6 클러스터에서 지원되지만 HDInsight 3.0 클러스터에서는 지원되지 않으며 이후 버전에서도 지원되지 않을 것입니다.

> [WACOM.NOTE] WASB 경로는 가상 경로입니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][]을 참조하세요.

기본 파일 시스템 컨테이너에 저장된 파일은 HDInsight에서 다음 URI를 사용하여 액세스할 수 있습니다(sample.log를 예로 사용함). 이 파일은 이 자습서에서 사용되는 데이터 파일입니다.

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

저장소 계정에서 바로 파일에 액세스하려는 경우 파일의 Blob 이름은 다음과 같습니다.

    example/data/sample.log

이 문서에서는 HDInsight 클러스터와 함께 제공되는 log4j 샘플 파일(*\\example\\data\\sample.log*에 저장됨)을 사용합니다. 자체 데이터 파일 업로드에 대한 자세한 내용은 [HDInsight에 데이터 업로드][](영문)를 참조하세요.

## <span id="understand"></span></a> Pig Latin 이해

이 세션에서는 일부 Pig Latin 문과 이 문을 실행한 후의 결과를 검토합니다. 다음 세션에서는 PowerShell을 실행하여 Pig 문을 실행합니다.

1.  파일 시스템에서 데이터를 로드하고 결과를 표시합니다.

        LOGS = LOAD 'wasb:///example/data/sample.log';
        DUMP LOGS;

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

2.  데이터 파일에서 각 줄을 검토하여 6개의 로그 수준에서 일치 항목을 찾습니다.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  일치 항목이 없는 행을 필터링합니다. 빈 행을 예로 들 수 있습니다.

        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        DUMP FILTEREDLEVELS;

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

4.  모든 로그 수준을 고유 행으로 그룹화합니다.

        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        DUMP GROUPEDLEVELS;

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

5.  각 그룹에서 로그 수준의 수를 계산합니다. 각 로그 수준의 빈도입니다.

        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        DUMP FREQUENCIES;

    다음과 유사하게 출력됩니다.

        (INFO,3355)
        (WARN,361)
        (DEBUG,15608)
        (ERROR,181)
        (FATAL,37)
        (TRACE,29950)

6.  빈도를 내림차순으로 정렬합니다.

        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;   

    다음과 유사하게 출력됩니다.

        (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)
        (ERROR,181)
        (FATAL,37)

## <span id="powershell"></span></a>PowerShell을 사용하여 Pig 작업 제출

이 섹션에서는 PowerShell cmdlet 사용 지침을 설명합니다. 이 섹션을 진행하기 전에 먼저 로컬 환경을 설정하고 Azure 연결을 구성해야 합니다. 자세한 내용은 [Azure HDInsight 시작][] 및 [PowerShell을 사용하여 HDInsight 관리][]를 참조하세요.

**PowerShell을 사용하여 Pig Latin을 실행하려면**

1.  Windows PowerShell ISE를 엽니다. Windows 8 시작 화면에서 **PowerShell\_ISE**를 입력한 후 **Windows PowerShell ISE**를 클릭하면 됩니다. [Windows 8 및 Windows에서 Windows PowerShell 시작][](영문)을 참조하세요.
2.  아래쪽 창에서 다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다. 구독 연결을 추가하는 이 메서드의 시간이 초과하고 12시간 후에 cmdlet을 다시 실행해야 합니다.

    > [WACOM.NOTE] 여러 Azure 구독이 있는 경우 기본 구독이 사용하려는 구독이 아니면 **Select-AzureSubscription** cmdlet을 사용하여 현재 구독을 선택합니다.

3.  스크립트 창에서 다음 줄을 복사하여 붙여 넣습니다.

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  변수 $clusterName을 설정합니다.

5.  다음 줄을 스크립트 창에 추가합니다. 이러한 줄은 Pig Latin 쿼리 문자열을 정의하고 Pig 작업 정의를 만듭니다.

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

    -File 스위치를 사용하여 HDFS에서 Pig 스크립트 파일을 지정할 수도 있습니다. -StatusFolder 스위치는 표준 오류 로그 및 표준 출력 파일을 폴더에 추가합니다.

6.  Pig 작업 제출을 위한 다음 줄을 추가합니다.

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Pig 작업이 완료될 때까지 기다리기 위한 다음 줄을 추가합니다.

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Pig 작업 출력을 인쇄하기 위한 다음 줄을 추가합니다.

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] 다음 스크린샷에서 Get-AzureHDInsightJobOut cmdlet 중 하나는 출력을 간단히 나타내기 위해 주석 처리되었습니다.

9.  **F5** 키를 눌러 스크립트를 실행합니다.
    ![HDI.Pig.PowerShell][]

    Pig 작업은 다양한 로그 종류의 빈도를 계산합니다.

## <span id="sdk"></span></a>HDInsight .NET SDK를 사용하여 Pig 작업 제출

다음은 HDInsight .NET SDK를 사용하는 Pig 작업 제출 샘플입니다. Hadoop 작업 제출을 위한 C# 응용 프로그램을 만드는 방법은 [프로그래밍 방식으로 Hadoop 작업 제출][](영문)을 참조하세요.

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

## <span id="nextsteps"></span></a>다음 단계

Pig를 사용하여 데이터를 분석할 수 있지만, HDInsight에 포함된 다른 언어도 유용할 수 있습니다. Hive는 HDInsight에 저장된 데이터를 손쉽게 쿼리할 수 있는 SQL과 유사한 쿼리 언어를 제공하며, Java로 작성된 MapReduce 작업은 복잡한 데이터를 분석할 수 있게 합니다. 자세한 내용은 다음을 참조하세요.

-   [Azure HDInsight 시작][]
-   [HDInsight에 데이터 업로드][]
-   [프로그래밍 방식으로 Hadoop 작업 제출][]
-   [HDInsight에서 Hive 사용][]

  [Apache Pig]: http://pig.apache.org/
  [Pig 사용 사례]: #usage
  [필수 조건]: #prerequisites
  [Pig Latin 이해]: #understand
  [PowerShell을 사용하여 Pig 작업 제출]: #powershell
  [HDInsight .NET SDK를 사용하여 Pig 작업 제출]: #sdk
  [다음 단계]: #nextsteps
  [전체 파일 샘플]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Pig Latin 참조 설명서 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Pig Latin 참조 설명서 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Azure HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
  [PowerShell을 사용하여 HDInsight 관리]: ../hdinsight-administer-use-powershell/
  [Windows 8 및 Windows에서 Windows PowerShell 시작]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [프로그래밍 방식으로 Hadoop 작업 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
